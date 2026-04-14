# Estate Agent Platform

A conversion-first property platform for UK letting agencies, focused on turning enquiries into confirmed viewings faster.

The platform is designed as a **workflow + marketplace hybrid** for letting agents inside Greater London, with a core loop that helps agencies reduce admin, lower no-shows, and improve viewing conversion.

## What it does

- Lets agencies manage branches, users, and property listings
- Supports image uploads and property publication
- Provides slot-based viewing availability
- Allows guests to book viewings without creating an account
- Supports seeker accounts, saved searches, and email alerts
- Includes secure in-app messaging between seekers and agents
- Sends automated confirmation and reminder emails
- Tracks attendance outcomes and funnel analytics
- Provides an admin moderation and agency approval workflow

## Positioning

The platform is **not** trying to compete head-on with consumer property portals. Instead, it is positioned as a **conversion OS** for letting agents — a system that helps agencies convert interest into booked viewings and measure the resulting ROI.

## Target users

- Letting agencies
- Independent estate agencies
- Agency admins
- Agents
- Property seekers

Primary launch focus: **Greater London / inside the M25**

## Key features

### Agency side
- Multi-tenant onboarding
- Branch and team management
- Property listing creation and publishing
- Viewing slot management
- Booking diary with attended / no-show tracking
- Messaging inbox
- Analytics dashboard

### Seeker side
- Property search and filters
- Map-based discovery
- Guest booking flow
- Seeker registration and login
- Saved searches and alerts
- Secure messaging

### Platform admin
- Agency approval and suspension
- Content moderation
- Platform-wide analytics

## Technology stack

- **Language:** Go
- **Web rendering:** `html/template`
- **Frontend interaction:** HTMX
- **Styling:** Bootstrap 5
- **Database:** PostgreSQL
- **Query generation:** sqlc
- **Migrations:** goose
- **Storage:** S3-compatible object storage
- **Deployment:** Docker

## Architecture

The application uses a **modular monolith** approach with strict feature boundaries.

Core modules include:

- `tenant`
- `auth`
- `listing`
- `search`
- `booking`
- `messaging`
- `analytics`
- `notification`
- `admin`
- `seeker`

Multi-tenancy is enforced using **row-level tenancy** with PostgreSQL RLS.

## MVP scope

Included in MVP:
- Tenant onboarding and management
- Branch and agent management
- Property CRUD and publishing
- Viewing slot management
- Public property search
- Guest booking flow
- Seeker accounts and saved searches
- Secure messaging
- Automated reminders
- Analytics
- Admin moderation
- GDPR and security controls

Deferred for later:
- Google / Outlook calendar sync
- OAuth login
- Native mobile apps
- Portal syndication
- Companies House verification automation
- Mortgage / conveyancing integrations
- Tenant credit checks
- Auction module
- WebSockets

## Development

The project is intended to run as a single Go binary, with templates and static assets embedded into the build.

Local development uses:
- PostgreSQL
- MinIO or another S3-compatible service
- Docker Compose

## Security and compliance

- CSRF protection
- Rate limiting
- Security headers
- GDPR support
- Cookie consent handling
- Right to erasure support
- Structured logging
- Health and metrics endpoints

## License

Proprietary. All rights reserved.