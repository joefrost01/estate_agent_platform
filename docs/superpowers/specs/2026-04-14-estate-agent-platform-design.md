# Estate Agent Platform — Design Specification

**Date:** 2026-04-14
**Status:** Approved for implementation planning
**Approach:** Conversion OS (Approach B)

---

## 1. Strategic Context

### 1.1 Market Positioning

The platform does **not** compete head-on with Rightmove or Zoopla as a consumer portal. That is a distribution battle against incumbents with entrenched network effects (Rightmove: £425m revenue, >80% share of time on UK property portals, 16.8bn minutes spent in 2025).

Instead, the platform positions as a **"conversion OS"** for letting agents: a workflow + marketplace hybrid that produces measurable ROI (reduced admin, fewer no-shows, faster fill cycles) and creates a distinctive consumer promise ("bookable, responsive listings") that portals cannot easily copy without deep operational integration.

### 1.2 Primary Customer

**Letting agents and independent agencies** in **Greater London (inside the M25)**.

- B2B-led wedge: agents are the paying customers
- B2C pull: property seekers generate booking volume that proves agent ROI
- Lettings-first: high frequency, high scheduling urgency — maximises the value of the booking feature

### 1.3 Positioning Statement

> For UK letting agencies that want to convert enquiries into confirmed viewings faster, the Estate Agent Platform is a conversion-first marketplace that lets renters book viewings and message securely in minutes — cutting admin, reducing no-shows, and proving ROI with end-to-end analytics.

---

## 2. Scope

### 2.1 In Scope (MVP)

- Multi-tenant agency onboarding and management
- Branch and agent user management (invite-based)
- Property listing creation, editing, and publication with image uploads
- Slot-based availability management per property
- Public property search (location, filters, map view)
- Guest booking flow (name, email, phone — no account required)
- Hybrid seeker accounts (prompted post-booking, not required)
- Saved searches and email alerts for seeker accounts
- Secure in-app messaging (seeker ↔ agent, per property thread)
- Automated booking confirmation and reminder emails (24h + 1h)
- Agent booking diary with attended/no-show outcome tracking
- Agent analytics dashboard (views → bookings → attended funnel)
- Platform admin moderation panel (agency approval, content review)
- GDPR compliance (cookie consent, right to erasure)
- CSRF protection, rate limiting, security headers

### 2.2 Explicitly Deferred

| Feature | Planned phase |
|---|---|
| Google / Outlook calendar sync | Post-MVP |
| OAuth2 login (Google, Microsoft) | Post-MVP |
| Native iOS / Android apps | Post-MVP |
| Portal feed syndication (Rightmove ADF, Zoopla) | Post-MVP |
| Companies House API automated verification | v2 |
| Mortgage / conveyancing integrations | v2 |
| Tenant credit / reference checking | v2 |
| Auction module | v2 |
| WebSockets (real-time messaging) | Post-MVP (polling sufficient) |

---

## 3. Technology Stack

| Layer | Choice | Rationale |
|---|---|---|
| Language | Go (`net/http`) | Single binary, fast, cloud-agnostic, excellent concurrency for background workers |
| Templating | `html/template` (stdlib) | Server-side rendering, auto-escapes output, no build pipeline |
| Frontend interaction | HTMX | HTML-over-the-wire, no SPA framework, no jQuery, no build step |
| Styling | Bootstrap 5 | Consistent, accessible, well-known; overridden via `app.css` |
| Database | PostgreSQL | Row-level tenancy via RLS, PostGIS for spatial search, sqlc for type-safe queries |
| ORM / query tool | sqlc | Generates type-safe Go from `.sql` files; no ORM magic, no reflection |
| Migrations | goose | SQL files in `db/migrations/`, auto-run in dev, manual pre-deploy step in prod |
| Object storage | S3-compatible API | Works with AWS S3, GCS (interop), Cloudflare R2, Azure Blob, MinIO (local dev) |
| Deployment | Docker (single binary) | `go:embed` for templates + static assets; one container, one port |
| Configuration | Environment variables | 12-factor app; no config files in repo |

---

## 4. Architecture

### 4.1 Multi-Tenancy

**Strategy:** Row-level tenancy — `tenant_id UUID` on every tenant-scoped table. PostgreSQL Row Level Security (RLS) policies enforce that queries only ever touch rows belonging to the current tenant.

**Tenant resolution:** Subdomain-first (`foxtons.platform.com`), falling back to an `X-Tenant-ID` request header for API clients. Middleware resolves the tenant early in the request lifecycle and injects it into the Go context. The current tenant UUID is set as a Postgres session variable (`app.current_tenant`) on each connection before any query runs.

```sql
-- Applied to every tenant-scoped table
ALTER TABLE properties ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON properties
  USING (tenant_id = current_setting('app.current_tenant')::uuid);
```

**Tenant hierarchy:**

```
Tenant (agency)
└── Branch (office location)
    └── User (agent, assigned to branch)
        └── Property (listing)
            ├── availability_slots
            ├── bookings
            ├── messages
            └── property_media
```

**Seekers are platform-wide** — not tenant-scoped. A seeker can find and book properties across any agency. Only `tenants`, `branches`, `users`, `properties`, `availability_slots`, `bookings`, `messages`, and `property_media` carry `tenant_id`.

### 4.2 Request Flow

```
Browser (HTMX request)
  → Tenant middleware (resolve from subdomain / header)
  → Auth middleware (session cookie → role: public / seeker / agent / agency_admin / platform_admin)
  → Handler (parse request, call service)
  → Service (business logic)
  → DB (sqlc query, RLS enforced)
  → Template (SSR full page or HTMX partial fragment)
  → Browser (HTMX swaps target element)
```

Handlers detect the `HX-Request` header to determine whether to render a full page layout or just the swapped fragment (partial in `web/templates/partials/`).

### 4.3 Modulith Architecture

The application is a **modular monolith** (modulith): a single deployable binary whose internals are divided into strict, self-contained feature modules. Each module owns its own handlers, service logic, repository queries, and domain types. Modules communicate only through exported interfaces — never by importing another module's internals directly.

**Why modulith:**
- Single binary keeps deployment and operations simple at MVP scale
- Clear feature boundaries keep each module small enough to fit in a coding agent's context window — no multi-file sprawl to reason about
- Cross-module contracts are explicit interfaces, making dependencies visible and testable
- If a module needs to scale independently in future, it can be extracted to a service with minimal refactoring (the interface boundary is already there)

**Module list:**

| Module | Responsibility |
|---|---|
| `tenant` | Agency onboarding, branch management, tenant status machine |
| `auth` | Session management, password auth (v1), OAuth (v2) |
| `listing` | Property CRUD, publication workflow, media uploads |
| `search` | Public property search, map queries, PostGIS, saved searches |
| `booking` | Slot management, atomic reservation, outcome tracking, reminders |
| `messaging` | Seeker ↔ agent threads, inbox, response time tracking |
| `analytics` | Funnel metrics (views → bookings → attended), agent dashboard |
| `notification` | Email dispatch (wraps Mailer interface); all modules call this, never mail directly |
| `admin` | Platform-level moderation, agency approval, platform analytics |
| `seeker` | Seeker account management, GDPR erasure |

**Cross-module rules:**
- A module may depend on `notification` (for sending emails) and `storage` (for media URLs) — both via interfaces injected at startup
- `booking` may read from `listing` only via the `listing.Reader` interface (not the full listing package)
- `analytics` reads from `booking` and `listing` via read-only interfaces
- No module imports another module's `repository` or `handler` packages directly

**`listing.Reader`** (cross-module read interface used by `booking` and `analytics`):

```go
// listing/reader.go
type Reader interface {
    GetProperty(ctx context.Context, id uuid.UUID) (*model.Property, error)
    ListActiveProperties(ctx context.Context, tenantID uuid.UUID) ([]model.Property, error)
}
```

**Reminder worker** (`booking/worker.go`): a goroutine started at application startup running a ticker every 1 minute. It queries for bookings where `starts_at` is within 25 hours (and `reminder_24h_sent = false`) or within 65 minutes (and `reminder_1h_sent = false`), dispatches emails via `notification.Mailer`, and updates the sent flags. No external queue or cron dependency — keeps the single-binary constraint.

### 4.4 Project Structure

```
estate-agent-platform/
├── cmd/server/
│   └── main.go                    # wires modules together, registers routes
├── internal/
│   ├── config/
│   │   └── config.go              # env-based config struct
│   ├── platform/                  # shared cross-cutting infrastructure
│   │   ├── db/
│   │   │   ├── migrations/        # SQL migration files (goose)
│   │   │   └── db.go              # pgx pool init
│   │   ├── middleware/
│   │   │   ├── tenant.go          # resolve + inject tenant
│   │   │   ├── auth.go            # session cookie → role
│   │   │   ├── csrf.go            # CSRF token
│   │   │   └── logging.go         # structured request logging
│   │   └── storage/
│   │       ├── storage.go         # Storage interface
│   │       ├── s3.go              # S3-compatible implementation
│   │       └── local.go           # local disk (dev)
│   │
│   ├── tenant/                    # MODULE: agency + branch management
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go          # sqlc queries for this module
│   │   ├── model.go               # Tenant, Branch domain types
│   │   └── queries/               # .sql files for sqlc
│   │
│   ├── auth/                      # MODULE: authentication
│   │   ├── provider.go            # auth.Provider interface
│   │   ├── session.go             # session-based implementation
│   │   ├── handler.go             # login/logout/register routes
│   │   ├── repository.go
│   │   └── model.go               # Session, credentials types
│   │
│   ├── listing/                   # MODULE: property listings + media
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── reader.go              # listing.Reader interface (used by other modules)
│   │   ├── repository.go
│   │   ├── model.go               # Property, PropertyMedia types
│   │   └── queries/
│   │
│   ├── search/                    # MODULE: public search + saved searches
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go          # PostGIS spatial queries
│   │   ├── model.go               # SearchParams, SearchResult types
│   │   └── queries/
│   │
│   ├── booking/                   # MODULE: slots + reservations + outcomes
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go          # atomic slot reservation queries
│   │   ├── model.go               # Slot, Booking types
│   │   ├── worker.go              # reminder background job
│   │   └── queries/
│   │
│   ├── messaging/                 # MODULE: seeker ↔ agent threads
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go
│   │   ├── model.go               # Thread, Message types
│   │   └── queries/
│   │
│   ├── analytics/                 # MODULE: funnel metrics + dashboards
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go
│   │   └── queries/
│   │
│   ├── notification/              # MODULE: email dispatch (all modules use this)
│   │   ├── mailer.go              # Mailer interface
│   │   ├── smtp.go                # SMTP implementation
│   │   └── templates/             # email HTML templates
│   │
│   ├── seeker/                    # MODULE: seeker accounts + GDPR erasure
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go
│   │   ├── model.go               # Seeker, SavedSearch types
│   │   └── queries/
│   │
│   └── admin/                     # MODULE: platform moderation
│       ├── handler.go
│       ├── service.go
│       ├── repository.go
│       └── queries/
│
├── web/
│   ├── templates/
│   │   ├── layout/                # base.html, agent.html, admin.html
│   │   ├── public/                # search, property, booking, confirmation
│   │   ├── seeker/                # dashboard, register, login
│   │   ├── agent/                 # dashboard, listings, slots, diary, inbox, analytics
│   │   ├── admin/                 # agencies, properties, analytics
│   │   └── partials/              # HTMX fragment responses
│   └── static/
│       ├── css/app.css            # Bootstrap 5 + overrides
│       └── js/app.js              # htmx.min.js + minimal vanilla JS
├── Dockerfile
├── docker-compose.yml             # local: postgres + minio + app
├── Makefile
└── go.mod
```

**Each module is independently workable:** a coding agent working on `booking` reads only the `booking/` package plus the interfaces it depends on (`listing.Reader`, `notification.Mailer`). It never needs to reason about `messaging` or `analytics` internals.

### 4.5 Key Go Interfaces

All external dependencies are defined as Go interfaces. This enables swappable implementations without touching handlers, and makes the service layer testable with mocks.

**`storage.Storage`**

```go
type Storage interface {
    // PresignPut returns a pre-signed URL for direct browser upload (15 min TTL)
    PresignPut(ctx context.Context, key string, ttl time.Duration) (string, error)
    // PublicURL returns the CDN-served URL for a stored object
    PublicURL(key string) string
    // Delete removes an object (used on GDPR erasure)
    Delete(ctx context.Context, key string) error
}
// Implementations: s3.Storage (AWS/GCS/R2/MinIO), local.Storage (dev)
```

**`notification.Mailer`**

```go
type Mailer interface {
    SendBookingConfirmation(ctx context.Context, b *model.Booking) error
    SendReminder(ctx context.Context, b *model.Booking) error
    SendAgentInvite(ctx context.Context, u *model.User, token string) error
    SendSearchAlert(ctx context.Context, s *model.Seeker, props []model.Property) error
}
// Implementation: smtp.Mailer (any SMTP provider — SES, SendGrid, Postmark, etc.)
// All modules call notification.Send*, never the SMTP implementation directly.
```

**`auth.Provider`**

```go
type Provider interface {
    Authenticate(ctx context.Context, email, password string) (*model.User, error)
    CreateSession(ctx context.Context, userID uuid.UUID) (string, error)
    ValidateSession(ctx context.Context, token string) (*model.Session, error)
    DestroySession(ctx context.Context, token string) error
}
// v1: session-based email/password
// v2: add OAuthProvider wrapping this interface (Google, Microsoft)
```

---

## 5. Data Model

All tenant-scoped tables include `tenant_id UUID NOT NULL` and have RLS enabled.

### 5.1 Core Tables

**`tenants`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| slug | text UNIQUE | subdomain identifier |
| name | text | display name |
| status | enum | `pending`, `email_verified`, `active`, `suspended` |
| subscription_tier | enum | `free`, `starter`, `pro`, `enterprise` |
| created_at | timestamptz | |

**`branches`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | RLS |
| name | text | |
| address | jsonb | structured address |
| phone | text | |
| active | boolean | |

**`users`** (agents and platform admins)

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | NULL for platform_admin |
| branch_id | uuid FK | nullable — agency admin may not be branch-specific |
| email | text UNIQUE | |
| password_hash | text | bcrypt |
| role | enum | `agent`, `agency_admin`, `platform_admin` |
| full_name | text | |
| invite_token | text | used for email invite flow |
| email_verified | boolean | |
| created_at | timestamptz | |

**`seekers`** (platform-wide, no tenant_id)

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| email | text UNIQUE | |
| password_hash | text | bcrypt |
| full_name | text | |
| phone | text | |
| created_at | timestamptz | |
| erased_at | timestamptz | soft-delete for GDPR |

**`properties`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | RLS |
| branch_id | uuid FK | |
| agent_id | uuid FK | |
| status | enum | `draft`, `active`, `under_offer`, `let`, `archived` |
| tenure | enum | `rent`, `sale` |
| address_line1 | text | |
| address_line2 | text | |
| city | text | |
| postcode | text | |
| location | geometry(Point, 4326) | PostGIS — lat/lng for map search |
| price_pcm | integer | pence (rent); price_sale integer (pence) for sales |
| bedrooms | integer | |
| bathrooms | integer | |
| furnished | enum | `furnished`, `part_furnished`, `unfurnished` |
| pets_allowed | boolean | |
| description | text | |
| features | jsonb | flexible key/value feature flags |
| view_count | integer | incremented on each property page view |
| created_at | timestamptz | |
| published_at | timestamptz | |

**`availability_slots`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | RLS |
| property_id | uuid FK | |
| agent_id | uuid FK | agent running the viewing |
| starts_at | timestamptz | |
| ends_at | timestamptz | |
| capacity | integer | 1 = private viewing, N = open house |
| booked_count | integer | incremented atomically on booking |

**`bookings`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | RLS |
| slot_id | uuid FK | |
| seeker_id | uuid FK | nullable — NULL for guest bookings |
| guest_name | text | populated if seeker_id IS NULL |
| guest_email | text | |
| guest_phone | text | |
| status | enum | `confirmed`, `cancelled`, `attended`, `no_show` |
| confirm_token | text | unique token for guest email confirmation link |
| reminder_24h_sent | boolean | |
| reminder_1h_sent | boolean | |
| created_at | timestamptz | |

**`messages`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | RLS |
| property_id | uuid FK | |
| seeker_id | uuid FK | |
| agent_id | uuid FK | the agent assigned to this thread |
| body | text | |
| sender | enum | `seeker`, `agent` |
| read_at | timestamptz | nullable |
| sent_at | timestamptz | |

**`property_media`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| tenant_id | uuid FK | RLS |
| property_id | uuid FK | |
| storage_key | text | path in object store: `{tenant_id}/{property_id}/{filename}` |
| content_type | text | e.g. `image/jpeg` |
| sort_order | integer | drag-to-reorder |
| kind | enum | `photo`, `floorplan`, `video_url` |
| created_at | timestamptz | |

**`saved_searches`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| seeker_id | uuid FK | |
| name | text | user-facing label |
| filters | jsonb | serialised search params |
| alert_frequency | enum | `instant`, `daily`, `off` |
| last_alerted_at | timestamptz | |

**`sessions`**

| Column | Type | Notes |
|---|---|---|
| id | uuid PK | |
| user_id | uuid | nullable — either user or seeker |
| seeker_id | uuid | nullable |
| token | text UNIQUE | stored in HTTP-only cookie |
| expires_at | timestamptz | |
| created_at | timestamptz | |

---

## 6. Route Map

### 6.1 Public Routes (no auth required)

| Method | Path | Description |
|---|---|---|
| GET | `/` | Homepage with search entry point |
| GET | `/properties` | Search results (HTMX-swappable) |
| GET | `/properties/:id` | Property detail + available slots |
| POST | `/properties/:id/book` | Submit guest or seeker booking |
| GET | `/confirm/:token` | Guest booking confirmation + account prompt |
| GET | `/seekers/register` | Seeker registration form |
| POST | `/seekers/register` | Create seeker account |
| GET | `/seekers/login` | Seeker login form |
| POST | `/seekers/login` | Authenticate seeker |
| POST | `/seekers/logout` | Destroy seeker session |

### 6.2 Seeker Routes (seeker auth required)

| Method | Path | Description |
|---|---|---|
| GET | `/seekers/dashboard` | Saved properties, bookings, alerts |
| POST | `/seekers/searches` | Save a search + set alert frequency |
| DELETE | `/seekers/searches/:id` | Remove saved search |
| GET | `/seekers/bookings` | Seeker's booking history |
| POST | `/seekers/bookings/:id/cancel` | Cancel a booking |

### 6.3 Agent Routes (agent auth required)

| Method | Path | Description |
|---|---|---|
| GET | `/dashboard` | Overview / KPI summary |
| GET | `/dashboard/properties` | Listings table (sort/filter via HTMX) |
| GET | `/dashboard/properties/new` | New listing form (multi-step) |
| POST | `/dashboard/properties` | Create listing |
| GET | `/dashboard/properties/:id/edit` | Edit listing form |
| PUT | `/dashboard/properties/:id` | Update listing |
| POST | `/dashboard/properties/:id/publish` | Set status → active |
| POST | `/dashboard/properties/:id/archive` | Set status → archived |
| GET | `/dashboard/properties/:id/media/upload-url` | Get pre-signed PUT URL |
| POST | `/dashboard/properties/:id/media/confirm` | Confirm upload, write to property_media |
| DELETE | `/dashboard/properties/:id/media/:media_id` | Delete a media item |
| GET | `/dashboard/properties/:id/slots` | Manage availability slots |
| POST | `/dashboard/properties/:id/slots` | Add a slot |
| DELETE | `/dashboard/properties/:id/slots/:slot_id` | Delete a slot |
| GET | `/dashboard/bookings` | Upcoming bookings diary |
| PATCH | `/dashboard/bookings/:id/outcome` | Mark attended or no_show |
| GET | `/dashboard/messages` | Message inbox (HTMX polled) |
| POST | `/dashboard/messages/:thread_id` | Send a reply |
| GET | `/dashboard/analytics` | Funnel: views → bookings → attended |
| GET | `/dashboard/team` | Branch + user management (agency_admin only) |
| POST | `/dashboard/team/invite` | Send agent invite email |
| DELETE | `/dashboard/team/users/:id` | Remove agent |
| GET | `/agent/login` | Agent login form |
| POST | `/agent/login` | Authenticate agent |
| POST | `/agent/logout` | Destroy agent session |

### 6.4 Admin Routes (platform_admin required)

| Method | Path | Description |
|---|---|---|
| GET | `/admin/agencies` | Agency list — approve / suspend |
| PATCH | `/admin/agencies/:id/approve` | Approve agency |
| PATCH | `/admin/agencies/:id/suspend` | Suspend agency |
| GET | `/admin/properties` | Content moderation queue |
| PATCH | `/admin/properties/:id/remove` | Remove a listing |
| GET | `/admin/analytics` | Platform-wide metrics |

### 6.5 System Routes

| Method | Path | Description |
|---|---|---|
| GET | `/healthz` | Health check (returns 200 OK) |
| GET | `/metrics` | Prometheus metrics |

---

## 7. Key User Flows

### 7.1 Agency Onboarding

1. Agency admin registers (company name, Companies House number, email, password). Unique subdomain auto-suggested. `INSERT tenants (status=pending)`. Verification email sent.
2. Admin clicks verification link. `UPDATE tenants (status=email_verified)`.
3. Platform admin reviews agency in `/admin/agencies` and approves. `UPDATE tenants (status=active)`. Welcome email sent.
4. Agency admin creates branches and invites agents by email (`INSERT branches`, `INSERT users` with `invite_token`, invite email sent).
5. Agent clicks invite link, sets password, lands on dashboard. Prompted to create first listing.

**Tenant status machine:** `pending` → `email_verified` → `active` ↔ `suspended`

### 7.2 Listing Creation + Availability Slots

1. Agent completes multi-step listing form (address autocomplete via OS Places API, property details, description, features). HTMX validates each step inline. `INSERT properties (status=draft)`.
2. Agent uploads images. Browser JS requests pre-signed PUT URL from server. Browser uploads directly to object storage (server never in the upload path). On completion, browser POSTs confirmation to server. `INSERT property_media`. Drag-to-reorder sets `sort_order`.
3. Agent adds viewing slots: date, start time, duration (30/45/60 min), capacity. `INSERT availability_slots`.
4. Agent publishes: `UPDATE properties (status=active)`. Property appears in public search.

### 7.3 Seeker: Search → Book (core loop)

1. Seeker enters location (postcode or draw polygon on map), price range, bedrooms, other filters. Results update via HTMX `hx-get` as filters change (debounced 300ms). PostGIS spatial query + full-text search.
2. Seeker opens property detail page. Available slots shown prominently. If no slots, "message agent" is primary CTA.
3. Seeker clicks a slot. HTMX swaps booking panel inline. Seeker enters name, email, phone. Submits. Booking created in a single atomic transaction (increments `booked_count`, creates booking row). Confirmation emails sent to seeker and agent.
4. Confirmation page shown. If seeker has no account: prompt to create one using the email already captured ("Save this booking and get alerts — 10 seconds"). One-click account creation.
5. Background worker sends reminder emails at 24h and 1h before slot start. Both seeker and agent notified. Easy reschedule/cancel link in email (cancel via `confirm_token`, no login required for guests).
6. After the viewing, agent marks booking as `attended` or `no_show` from their diary. Feeds analytics funnel.

### 7.4 Secure Messaging

1. Seeker (must be logged in — prevents spam) sends enquiry from property detail page. Creates a conversation thread scoped to `(property_id, seeker_id, agent_id)`. `INSERT messages`. Notification email sent to agent.
2. Agent replies from `/dashboard/messages`. HTMX polls for new messages every 30 seconds (`hx-trigger="every 30s"`). Reply sent inline. Notification email sent to seeker.
3. Conversations are grouped by property (not by seeker) in the agent inbox. Unread count shown in nav. Response time tracked per agent (from first inbound message to first reply).

### 7.5 Analytics

Agent dashboard shows the conversion funnel per property and across portfolio:

**Listing Views → Bookings Made → Attended**

Key metrics surfaced:
- **Attendance rate** (primary ROI metric — differentiates from portal impressions-only)
- **Average agent response time** (first message → first reply)
- **No-show rate**
- **Saves per listing**
- **Bookings per slot**

Platform admin sees these aggregated across all tenants.

---

## 8. HTMX Interaction Patterns

All tables (listings, bookings diary, admin agency list) follow a single consistent pattern:

- **Sort:** Column header is a link with `?sort=col&dir=asc/desc`. Server renders `<tbody>` only when `HX-Request` header is present.
- **Filter:** Filter inputs use `hx-get` with `hx-trigger="input changed delay:300ms"` targeting `#table-body`. Server returns rendered `<tbody>` partial.
- **Pagination:** `?page=N&per_page=25` query params, rendered as Bootstrap pagination component.

| Interaction | Trigger | Target | Server returns |
|---|---|---|---|
| Search filter change | `hx-get` debounced 300ms | `#results-list` | Property cards partial |
| Book a slot | `hx-post` on slot click | `#booking-panel` | Booking form or success |
| Table sort / filter | `hx-get` on header / input | `#table-body` | `<tbody>` rows only |
| Message inbox poll | `hx-trigger="every 30s"` | `#message-list` | Updated thread list |
| Send message | `hx-post` on form submit | `#message-list` | Appended message bubble |
| Listing form steps | `hx-post` per step | `#step-content` | Next step or inline errors |
| Image upload confirm | `hx-post` after S3 PUT | `#media-grid` | Updated grid with thumbnail |
| Mark booking outcome | `hx-patch` on button | Booking row | Updated row with status badge |

---

## 9. Image Storage

Images are stored in S3-compatible object storage, never in the database. The server is never in the upload path.

**Upload flow:**
1. Agent requests a pre-signed PUT URL: `GET /dashboard/properties/:id/media/upload-url`
2. Server generates pre-signed URL (15 min TTL) and returns it.
3. Browser PUTs file directly to object storage.
4. Browser POSTs confirmation: `POST /dashboard/properties/:id/media/confirm`
5. Server writes `storage_key` to `property_media` table.

**Storage key format:** `{tenant_id}/{property_id}/{uuid}.{ext}`

**Serving:** Public CDN URL — `{CDN_BASE}/{storage_key}`. No pre-signing needed for public property images. Private documents (if added later) would use pre-signed GETs.

**Storage provider switching:** Controlled by `STORAGE_PROVIDER` env var. The `storage.Storage` interface means the application code is identical regardless of provider.

---

## 10. Authentication

### 10.1 v1 — Session-based email/password

- Secure HTTP-only cookie containing a session token (UUID).
- Sessions stored in the `sessions` table with expiry.
- Passwords hashed with bcrypt (cost factor 12).
- Separate auth flows for seekers (public-facing) and agents (agent subdomain / `/agent/login`).
- Auth is abstracted behind the `auth.Provider` interface — implementations are swappable.

### 10.2 v2 — OAuth2 (planned, not in MVP)

- Google OAuth2 for seekers.
- Google + Microsoft OAuth2 for agents.
- Add `OAuthProvider` implementing the same `auth.Provider` interface — no handler changes required.

### 10.3 v3 — Enterprise SSO (planned)

- SAML or OIDC for large multi-branch agencies.
- Same interface, new implementation.

---

## 11. Non-Functional Requirements

| Concern | Approach |
|---|---|
| **CSRF** | Token in all HTML forms via middleware; validated on POST/PATCH/DELETE. HTMX requests include token via `hx-headers`. |
| **Rate limiting** | Per-IP token bucket on auth endpoints (login, register) and booking submission. Prevents brute force and booking spam. |
| **Security headers** | Middleware sets `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, `Content-Security-Policy`, `Referrer-Policy`. HTTPS enforced via redirect. |
| **Input sanitisation** | All template output auto-escaped by `html/template`. Free-text fields stripped of HTML on ingress. No raw HTML rendering. |
| **GDPR — right to erasure** | Soft-delete seekers: anonymise PII in DB (name → "Deleted User", email → SHA-256 hash), delete object storage images and media, purge message bodies. Audit log row retained. |
| **GDPR — cookie consent** | Cookie banner on first visit. Only the session cookie (strictly necessary) is set before consent. Future analytics gated behind consent. |
| **Database migrations** | goose SQL migration files in `internal/platform/db/migrations/`. Auto-run on startup in dev. Manual pre-deploy step in prod (`make migrate`). |
| **Observability** | Structured JSON logging via `slog`. Request ID middleware. `/healthz` health check. `/metrics` Prometheus endpoint (request count, latency, DB pool stats). |
| **Testing** | Unit tests for service layer with mocked interfaces. Integration tests for handlers via `httptest`. DB integration tests against real Postgres in CI (Docker). No mocking the database. |
| **Accessibility** | Bootstrap 5 semantic HTML baseline. ARIA labels on interactive elements. Keyboard navigation for slot picker. Target WCAG 2.1 AA. |
| **AML / agent verification** | Manual platform admin approval in v1. Companies House number recorded. Automated Companies House API verification deferred to v2. |

---

## 12. Deployment

The application is a 12-factor app. All configuration via environment variables.

```
DATABASE_URL=postgres://user:pass@host:5432/db?sslmode=require
SESSION_SECRET=<random-32-bytes>
STORAGE_PROVIDER=s3            # s3 | local
STORAGE_BUCKET=my-bucket
STORAGE_REGION=eu-west-2
STORAGE_CDN_BASE=https://cdn.example.com
SMTP_HOST=smtp.sendgrid.net
SMTP_PORT=587
SMTP_USER=apikey
SMTP_PASS=<key>
MAIL_FROM=hello@platform.com
BASE_URL=https://platform.com
PORT=8080
```

**Docker:** Multi-stage Dockerfile. Final image is a minimal Go binary (~20MB). Templates and static assets embedded via `go:embed` — no separate file server needed.

**Local dev:** `docker-compose.yml` runs Postgres + MinIO + the application. `make dev` starts everything.

**Cloud compatibility:** Runs on any platform that can run a Docker container with environment variables and a Postgres connection string. No cloud-provider-specific dependencies in the application code.

---

## 13. Out of Scope Decisions (Rationale)

| Decision | Rationale |
|---|---|
| No schema-per-tenant | Operational complexity outweighs isolation benefit at this scale. RLS provides strong isolation with one schema. |
| No WebSockets (polling instead) | 30-second HTMX polling is sufficient for messaging MVP. WebSockets add deployment complexity (sticky sessions, load balancer config). Add post-MVP. |
| No jQuery | Bootstrap 5 does not require jQuery. Vanilla JS covers the minimal client-side needs (pre-signed upload flow). |
| No SPA framework | HTMX HTML-over-the-wire is sufficient for all interactions. Avoids build pipeline, node_modules, and JS bundle complexity. |
| No ORM | sqlc generates type-safe Go from SQL. Queries are readable, reviewable, and performant. No N+1 hiding, no magic. |
| Slot-based (not calendar-sync) booking | Calendar sync (Google/Outlook OAuth) is complex to build, test, and support reliably. Slot-based booking is sufficient for MVP and delivers the core value prop. |
| Guest booking allowed | Maximises booking conversion rate — the primary metric that proves ROI to agents. Account creation is prompted post-booking. |
| London-only launch | Achieves listing density ("liquidity") in a tight geographic cell before expanding. Prevents the chicken-and-egg problem of low supply nationally. |
