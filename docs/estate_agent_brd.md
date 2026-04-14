**ESTATE AGENT PLATFORM**

*Business Requirements Document & Use Cases*

Version 1.0 \| April 2026 \| CONFIDENTIAL

**1. Executive Summary**

The Estate Agent Platform is a world-class, full-stack digital
marketplace designed to transform how properties are listed, discovered,
and transacted. The platform connects three distinct user groups ---
Estate Agents, Property Seekers, and Platform Administrators --- through
a seamless, data-driven experience that rivals the best PropTech
solutions globally.

The platform will deliver end-to-end functionality including property
registration, intelligent search and filtering, appointment scheduling,
in-app messaging, contract management, and advanced analytics --- all
underpinned by enterprise-grade security, GDPR compliance, and
AI-powered features.

**1.1 Document Information**

  ---------------- ------------------------------------------------------
  **Document       Estate Agent Platform --- Business Requirements
  Title**          Document

  ---------------- ------------------------------------------------------

  ---------------- ------------------------------------------------------
  **Version**      1.0

  ---------------- ------------------------------------------------------

  ---------------- ------------------------------------------------------
  **Status**       Draft for Review

  ---------------- ------------------------------------------------------

  ---------------- ------------------------------------------------------
  **Date**         April 2026

  ---------------- ------------------------------------------------------

  ---------------- ------------------------------------------------------
  **Author**       Product Strategy Team

  ---------------- ------------------------------------------------------

  ------------------ ------------------------------------------------------
  **Stakeholders**   CEO, CTO, Head of Product, Legal, Marketing,
                     Engineering Leads

  ------------------ ------------------------------------------------------

**1.2 Business Objectives**

-   Provide estate agents with a powerful, intuitive platform to list,
    manage, and promote properties globally.

-   Enable the public to discover, shortlist, schedule viewings, and
    engage with agents effortlessly.

-   Reduce time-to-transaction by automating appointment scheduling,
    communication, and document exchange.

-   Generate sustainable revenue through subscription tiers, premium
    listings, and lead generation services.

-   Achieve market leadership through AI-powered recommendations, rich
    media support, and superior UX.

**2. Scope & Boundaries**

**2.1 In Scope**

-   Multi-role user management: Estate Agents, Public Users
    (Buyers/Renters), Administrators

-   Property listing registration, management, and publication with rich
    media (photos, video tours, 3D walkthroughs)

-   Advanced property search, filtering, map-based discovery, and
    AI-powered recommendations

-   Real-time appointment booking and calendar synchronisation (Google,
    Outlook, Apple)

-   In-platform secure messaging between public users and agents

-   Notification system: push, email, SMS

-   Agent and agency profile management with reviews and ratings

-   Saved searches, property shortlisting, and comparison tools

-   Administrative back-office: moderation, analytics, billing, and
    compliance

-   Mobile-first responsive web app and native iOS/Android applications

-   API-first architecture for third-party integrations (Rightmove,
    Zoopla, OnTheMarket feeds)

-   GDPR-compliant data management, cookie consent, and right-to-erasure
    workflows

**2.2 Out of Scope (v1.0)**

-   Mortgage calculator and financial advice integration (planned v1.2)

-   Conveyancing and legal document generation (planned v2.0)

-   Tenant credit and reference checking (planned v1.3)

-   Auction module (planned v2.0)

**3. Stakeholders & User Personas**

**3.1 Stakeholder Register**

  -----------------------------------------------------------------------
  **Stakeholder**    **Role**              **Interest / Influence**
  ------------------ --------------------- ------------------------------
  Executive Team     Decision Makers       Budget approval, strategic
                                           direction

  Estate Agents      Primary Paying Users  Core revenue source; require
                                           reliable listing and lead
                                           tools

  Property Seekers   End Consumers         Drive platform traffic and
                                           appointment volume

  Legal & Compliance Risk Management       GDPR, AML, consumer protection
                                           adherence

  Engineering Team   Delivery              Feasibility, architecture, and
                                           technical constraints

  Marketing Team     Growth                SEO, brand visibility, user
                                           acquisition
  -----------------------------------------------------------------------

**3.2 User Personas**

**Persona A --- Estate Agent / Agency**

-   Typical user: mid-size agency with 2--50 agents managing 50--500
    active listings

-   Needs: Bulk listing upload, lead tracking, appointment diary,
    performance analytics

-   Pain points with existing tools: Poor mobile experience, fragmented
    lead management, high listing fees

**Persona B --- Property Buyer**

-   Typical user: First-time buyer, 25--40 years old, urban, tech-savvy

-   Needs: Accurate listings, map search, saved alerts, direct agent
    chat

-   Pain points: Stale listings, difficulty scheduling viewings, opaque
    pricing

**Persona C --- Property Renter**

-   Typical user: Young professional, 22--35 years old, frequent mover

-   Needs: Rental-specific filters (furnished, pet-friendly), quick
    application, tenancy document access

**Persona D --- Platform Administrator**

-   Internal staff responsible for moderation, agent verification,
    billing disputes, and system health

**4. Functional Requirements**

**4.1 User Registration & Authentication**

  ----------------------------------------------------------------------------------
  **REQ ID**    **Requirement**                    **Priority**   **User Role**
  ------------- ---------------------------------- -------------- ------------------
  FR-AUTH-001   Users shall register via           Must Have      All
                email/password, Google OAuth,                     
                Apple Sign-In, or Microsoft SSO.                  

  FR-AUTH-002   Multi-factor authentication        Must Have      All
                (TOTP/SMS) shall be available for                 
                all accounts and mandatory for                    
                Agent accounts.                                   

  FR-AUTH-003   Estate agents shall complete a     Must Have      Agent
                verified registration flow                        
                including company name,                           
                registration number, and ID                       
                document upload.                                  

  FR-AUTH-004   The system shall send email        Must Have      All
                verification on registration and                  
                password-reset tokens with 24-hour                
                expiry.                                           

  FR-AUTH-005   Role-based access control (RBAC)   Must Have      All
                shall distinguish Public User,                    
                Agent, Agency Admin, and Platform                 
                Admin roles.                                      

  FR-AUTH-006   Single Sign-On (SSO) shall be      Should Have    Agent
                supported for enterprise agency                   
                accounts.                                         

  FR-AUTH-007   The system shall support biometric Should Have    All
                authentication (Face ID, Touch ID)                
                in native mobile apps.                            
  ----------------------------------------------------------------------------------

**4.2 Property Listing Management (Estate Agents)**

  ----------------------------------------------------------------------------------
  **REQ ID**    **Requirement**                    **Priority**   **User Role**
  ------------- ---------------------------------- -------------- ------------------
  FR-LIST-001   Agents shall create property       Must Have      Agent
                listings via a guided multi-step                  
                form covering address, type,                      
                price, description, features, and                 
                EPC rating.                                       

  FR-LIST-002   The system shall support bulk      Must Have      Agent
                listing import via CSV/Excel                      
                template and direct API                           
                integration with major property                   
                portals.                                          

  FR-LIST-003   Each listing shall support upload  Must Have      Agent
                of up to 50 high-resolution images                
                (max 25 MB each) with automatic                   
                optimisation and WebP conversion.                 

  FR-LIST-004   Listings shall support embedded    Must Have      Agent
                virtual 3D tours (Matterport),                    
                video walkthroughs                                
                (YouTube/Vimeo/direct upload), and                
                floorplan uploads.                                

  FR-LIST-005   Address auto-complete shall use a  Must Have      Agent
                mapping API (Google Places / OS                   
                Places) to validate and geocode                   
                all listings.                                     

  FR-LIST-006   Agents shall be able to set        Must Have      Agent
                listing status: Draft, Pending                    
                Review, Active, Under Offer, Sold,                
                Archived.                                         

  FR-LIST-007   The system shall auto-populate     Should Have    Agent
                Ofcom broadband speed estimates                   
                and school proximity data using                   
                postcode enrichment.                              

  FR-LIST-008   Agents shall be able to duplicate  Should Have    Agent
                listings and create property                      
                templates for faster data entry.                  

  FR-LIST-009   AI-assisted description generation Nice to Have   Agent
                shall suggest property                            
                descriptions from structured data                 
                inputs.                                           

  FR-LIST-010   Listing performance analytics      Must Have      Agent
                (views, saves, enquiries, CTR)                    
                shall be visible in the agent                     
                dashboard.                                        
  ----------------------------------------------------------------------------------

**4.3 Property Search & Discovery (Public Users)**

  ----------------------------------------------------------------------------------
  **REQ ID**    **Requirement**                    **Priority**   **User Role**
  ------------- ---------------------------------- -------------- ------------------
  FR-SRCH-001   The search engine shall support    Must Have      Public
                keyword, location (city, postcode,                
                polygon draw), property type,                     
                price range, bedrooms, bathrooms,                 
                and tenure (sale/rent).                           

  FR-SRCH-002   Results shall be displayed in list Must Have      Public
                view, card grid view, and                         
                interactive map view with                         
                clustered markers.                                

  FR-SRCH-003   Map search shall support           Must Have      Public
                draw-your-own-boundary polygon                    
                filtering and radius-based search.                

  FR-SRCH-004   Advanced filters shall include EPC Must Have      Public
                rating, council tax band, parking,                
                garden, furnished, pets, new                      
                build, and retirement.                            

  FR-SRCH-005   Search results shall be sortable   Must Have      Public
                by: relevance, newest, price                      
                (asc/desc), and distance.                         

  FR-SRCH-006   AI-powered recommendation engine   Must Have      Public
                shall surface similar properties                  
                based on user behaviour and saved                 
                searches.                                         

  FR-SRCH-007   Users shall be able to save search Must Have      Public
                criteria and receive instant or                   
                daily email/push alerts for new                   
                matches.                                          

  FR-SRCH-008   A commute-time filter shall allow  Should Have    Public
                users to search by maximum travel                 
                time to a specified destination                   
                using public transport or car.                    

  FR-SRCH-009   A property comparison tool shall   Should Have    Public
                allow side-by-side comparison of                  
                up to 4 shortlisted properties.                   

  FR-SRCH-010   Neighbourhood insights shall       Should Have    Public
                display crime data, school                        
                ratings, transport links, and                     
                local amenities on property detail                
                pages.                                            
  ----------------------------------------------------------------------------------

**4.4 Appointment Booking**

  ----------------------------------------------------------------------------------
  **REQ ID**    **Requirement**                    **Priority**   **User Role**
  ------------- ---------------------------------- -------------- ------------------
  FR-APPT-001   Agents shall configure their       Must Have      Agent
                availability calendar with working                
                hours, blocked dates, and                         
                per-property time slots.                          

  FR-APPT-002   Public users shall book viewings   Must Have      Public
                directly from a property listing                  
                page without requiring a phone                    
                call.                                             

  FR-APPT-003   The system shall send instant      Must Have      All
                confirmation notifications (email,                
                SMS, push) to both the agent and                  
                the user upon booking.                            

  FR-APPT-004   Automated reminder notifications   Must Have      All
                shall be sent 24 hours and 1 hour                 
                before a scheduled appointment.                   

  FR-APPT-005   Agents shall be able to confirm,   Must Have      Agent
                reschedule, or cancel appointments                
                with automated notifications to                   
                the user.                                         

  FR-APPT-006   Calendar synchronisation shall be  Must Have      Agent
                supported with Google Calendar,                   
                Microsoft Outlook, and Apple                      
                Calendar via OAuth.                               

  FR-APPT-007   The system shall support virtual   Must Have      All
                viewing appointments via an                       
                integrated video call link (e.g.,                 
                built-in WebRTC or Zoom/Teams link                
                generation).                                      

  FR-APPT-008   Appointment history shall be       Must Have      All
                stored and accessible to both                     
                parties in their respective                       
                dashboards.                                       

  FR-APPT-009   Agents shall be able to add        Should Have    Agent
                internal notes to appointments                    
                (not visible to the user).                        

  FR-APPT-010   Group open-house events shall be   Should Have    Agent
                supported with attendee                           
                registration and capacity limits.                 
  ----------------------------------------------------------------------------------

**4.5 Messaging & Communication**

  ---------------------------------------------------------------------------------
  **REQ ID**   **Requirement**                    **Priority**   **User Role**
  ------------ ---------------------------------- -------------- ------------------
  FR-MSG-001   Users shall be able to send        Must Have      All
               enquiries to agents directly from                 
               a property listing page.                          

  FR-MSG-002   The platform shall provide a       Must Have      All
               secure, threaded in-app messaging                 
               system between users and agents.                  

  FR-MSG-003   Agents shall receive new message   Must Have      Agent
               notifications via email, SMS, and                 
               push notification.                                

  FR-MSG-004   The messaging system shall support Must Have      All
               file attachments (PDF, DOCX,                      
               images up to 10 MB each).                         

  FR-MSG-005   Agents shall have access to canned Should Have    Agent
               response templates for common                     
               enquiries.                                        

  FR-MSG-006   AI-powered chatbot shall handle    Should Have    All
               initial enquiries outside business                
               hours and triage to the correct                   
               agent.                                            

  FR-MSG-007   Message history shall be retained  Must Have      All
               for a minimum of 7 years for                      
               compliance purposes.                              

  FR-MSG-008   Agents shall be able to mark       Should Have    Agent
               conversations as read, archived,                  
               starred, or spam.                                 
  ---------------------------------------------------------------------------------

**4.6 Agent & Agency Profiles**

  ----------------------------------------------------------------------------------
  **REQ ID**    **Requirement**                    **Priority**   **User Role**
  ------------- ---------------------------------- -------------- ------------------
  FR-PROF-001   Each agent shall have a public     Must Have      Agent
                profile page displaying name,                     
                photo, biography, active listings,                
                and contact details.                              

  FR-PROF-002   Agency profiles shall display all  Must Have      Agent
                associated agents, the agency                     
                logo, social media links, office                  
                locations, and trading history.                   

  FR-PROF-003   Verified reviews and star ratings  Must Have      All
                from past clients shall be                        
                displayed on agent and agency                     
                profiles.                                         

  FR-PROF-004   Agents shall respond to reviews    Should Have    Agent
                through the platform, with                        
                responses publicly visible.                       

  FR-PROF-005   Profile completeness scores and    Nice to Have   Agent
                SEO tips shall be provided to                     
                incentivise complete, high-quality                
                profiles.                                         
  ----------------------------------------------------------------------------------

**4.7 Public User Dashboard**

  ----------------------------------------------------------------------------------
  **REQ ID**    **Requirement**                    **Priority**   **User Role**
  ------------- ---------------------------------- -------------- ------------------
  FR-DASH-001   Authenticated users shall have a   Must Have      Public
                personal dashboard displaying                     
                saved properties, saved searches,                 
                upcoming viewings, and recent                     
                enquiries.                                        

  FR-DASH-002   Users shall receive a personalised Must Have      Public
                \'Recommended for You\' property                  
                feed based on their activity and                  
                preferences.                                      

  FR-DASH-003   A property shortlist shall allow   Must Have      Public
                users to save, annotate, and share                
                favourite properties.                             

  FR-DASH-004   Users shall be able to set a       Must Have      Public
                personal property alert with                      
                email/push for instant new-match                  
                notifications.                                    

  FR-DASH-005   Users shall access a viewing       Should Have    Public
                history log and ability to leave                  
                post-viewing feedback.                            
  ----------------------------------------------------------------------------------

**4.8 Administration Back-Office**

  -----------------------------------------------------------------------------------
  **REQ ID**     **Requirement**                    **Priority**   **User Role**
  -------------- ---------------------------------- -------------- ------------------
  FR-ADMIN-001   Admins shall verify new agent      Must Have      Admin
                 registrations by reviewing                        
                 submitted identity and business                   
                 documents.                                        

  FR-ADMIN-002   Admins shall be able to suspend,   Must Have      Admin
                 warn, or permanently ban users or                 
                 agencies for policy violations.                   

  FR-ADMIN-003   Admins shall moderate reported     Must Have      Admin
                 listings and flag suspected                       
                 fraudulent or misleading content.                 

  FR-ADMIN-004   A real-time platform health        Must Have      Admin
                 dashboard shall display active                    
                 users, new listings, bookings, and                
                 revenue metrics.                                  

  FR-ADMIN-005   Admins shall manage subscription   Must Have      Admin
                 plans, promotional codes, and                     
                 billing disputes.                                 

  FR-ADMIN-006   Automated fraud detection shall    Must Have      Admin
                 flag listings with suspicious                     
                 pricing, duplicate photos, or                     
                 unverified agent accounts.                        

  FR-ADMIN-007   GDPR right-to-erasure requests     Must Have      Admin
                 shall be processable from the                     
                 admin panel within 30 days.                       
  -----------------------------------------------------------------------------------

**5. Non-Functional Requirements**

**5.1 Performance**

-   Property search results shall return within 1.5 seconds for 95% of
    requests under peak load.

-   Listing detail pages shall achieve a Google Lighthouse Performance
    score of 90+ (desktop and mobile).

-   The platform shall support a minimum of 100,000 concurrent active
    users without degradation.

-   Image delivery shall be served via CDN (e.g., Cloudflare, AWS
    CloudFront) with \< 200ms global TTFB.

**5.2 Scalability**

-   The architecture shall be cloud-native (AWS/GCP/Azure) with
    auto-scaling capabilities.

-   The platform shall be designed to scale to 10 million active
    listings without architectural redesign.

-   Microservices architecture shall allow independent scaling of
    search, messaging, and media processing services.

**5.3 Security**

-   All data in transit shall be encrypted using TLS 1.3 or above.

-   All sensitive data at rest (PII, documents) shall be encrypted using
    AES-256.

-   The platform shall achieve ISO 27001 certification within 12 months
    of launch.

-   OWASP Top 10 vulnerabilities shall be addressed as part of the SDLC;
    penetration testing shall occur quarterly.

-   API endpoints shall be protected by rate limiting, JWT
    authentication, and IP allow-listing for admin access.

**5.4 Availability & Reliability**

-   Target SLA: 99.9% uptime (less than 8.77 hours downtime per year).

-   Disaster recovery RTO \< 4 hours, RPO \< 1 hour.

-   Automated health checks and alerting shall be configured for all
    critical services.

**5.5 Compliance**

-   Full GDPR compliance: consent management, right of access, right to
    erasure, data portability.

-   Anti-Money Laundering (AML) controls for agent identity
    verification.

-   Accessibility: WCAG 2.2 Level AA compliance for all public-facing
    pages.

-   Consumer Protection from Unfair Trading Regulations 2008 compliance
    for listing accuracy standards.

**5.6 Usability**

-   New agents shall be able to publish their first listing within 15
    minutes of registration.

-   Mobile-first design: all core workflows shall be fully functional on
    screens 320px wide and above.

-   The platform shall support internationalisation (i18n) from launch,
    with initial languages: English, French, Spanish, Arabic.

**6. Use Cases**

The following use cases describe the primary interactions between users
and the platform. Each use case follows the standard IEEE template.

**UC-001: Estate Agent Registers and Lists a Property**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-001**

  **Title**            Estate Agent Registers and Lists a Property

  **Actor(s)**         Estate Agent (Primary), Platform Administrator
                       (Secondary)

  **Goal**             Agent successfully creates a verified account and
                       publishes a live property listing.

  **Preconditions**    Agent has a valid business email, company
                       registration number, and property to list.

  **Trigger**          Agent navigates to the platform and clicks
                       \'Register as Agent\'.

  **Main Flow**        1\. Agent enters personal and business details
                       (name, email, company, registration number). 2.
                       Agent uploads identity verification document
                       (passport / driving licence). 3. System sends a
                       verification email; agent confirms email address. 4.
                       Platform Administrator reviews and approves agent
                       account within 24 hours. 5. Agent receives approval
                       notification and logs in. 6. Agent navigates to
                       \'Add New Listing\' and completes guided multi-step
                       form. 7. Agent uploads photos, floorplan, and
                       optional virtual tour link. 8. Agent sets
                       availability for viewings. 9. Agent submits listing
                       for publication. 10. System auto-validates listing
                       (completeness check, address geocoding). 11. Listing
                       goes live and is indexed in the search engine.

  **Alternative        4a. Admin requests additional documentation ---
  Flows**              agent is notified and resubmits. 7a. Agent skips
                       optional media --- listing can still be published
                       with a \'Media Pending\' badge. 10a. Address cannot
                       be geocoded --- agent is prompted to confirm the
                       address manually.

  **Postconditions**   Listing is live, indexed in search, and visible to
                       public users.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**UC-002: Public User Searches for a Property**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-002**

  **Title**            Public User Searches for a Property

  **Actor(s)**         Public User (Guest or Authenticated)

  **Goal**             User finds relevant properties matching their
                       criteria.

  **Preconditions**    At least one active listing exists in the system.
                       User has internet access.

  **Trigger**          User visits the platform homepage or opens the
                       mobile app.

  **Main Flow**        1\. User enters a location (city, postcode, or draws
                       a boundary on the map). 2. User selects transaction
                       type (Buy or Rent) and enters a price range. 3. User
                       sets bedroom and property type filters. 4. System
                       returns a ranked list of matching listings. 5. User
                       switches to map view to explore results
                       geographically. 6. User clicks a property card to
                       view the full listing detail page. 7. User views
                       photos, virtual tour, floorplan, and neighbourhood
                       insights. 8. User saves the property to their
                       shortlist (prompted to log in if not authenticated).
                       9. User sets up a saved search alert for new
                       matching listings.

  **Alternative        4a. No results found --- system suggests broadening
  Flows**              filters or nearby areas. 8a. Guest user --- system
                       prompts lightweight registration to save shortlist.

  **Postconditions**   User has viewed one or more listings. Optionally,
                       user has saved a property and/or set a search alert.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**UC-003: Public User Books a Property Viewing**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-003**

  **Title**            Public User Books a Property Viewing Appointment

  **Actor(s)**         Public User (Primary), Estate Agent (Secondary)

  **Goal**             User successfully schedules a confirmed in-person or
                       virtual viewing appointment.

  **Preconditions**    User is authenticated. Property listing is active.
                       Agent has configured availability.

  **Trigger**          User clicks \'Book a Viewing\' on a property listing
                       detail page.

  **Main Flow**        1\. System displays the agent\'s available time
                       slots for the property. 2. User selects preferred
                       date and time and chooses In-Person or Virtual
                       Viewing. 3. User confirms contact details and
                       optionally adds a message to the agent. 4. System
                       creates the appointment and sends confirmation
                       notifications to both parties. 5. System adds the
                       appointment to the user\'s dashboard and agent\'s
                       diary. 6. Agent receives notification and confirms
                       or proposes an alternative time. 7. System sends
                       reminder notifications 24 hours and 1 hour before
                       the appointment. 8. For virtual viewings, a secure
                       video link is generated and shared with both
                       parties.

  **Alternative        2a. No slots available --- user can request the
  Flows**              agent to propose a time or join a waitlist. 6a.
                       Agent proposes alternative --- user is notified and
                       can accept or suggest another time. 6b. Agent
                       cancels --- user is notified immediately and invited
                       to rebook.

  **Postconditions**   Appointment is confirmed and recorded. Both parties
                       receive calendar invites and reminders.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**UC-004: Public User Contacts an Estate Agent**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-004**

  **Title**            Public User Sends an Enquiry to an Estate Agent

  **Actor(s)**         Public User (Primary), Estate Agent (Secondary)

  **Goal**             User sends a message to the agent and receives a
                       timely response.

  **Preconditions**    User is authenticated. Property listing or agent
                       profile is active.

  **Trigger**          User clicks \'Contact Agent\' or \'Send Enquiry\' on
                       a listing or agent profile page.

  **Main Flow**        1\. User types their enquiry in the messaging
                       interface. 2. User optionally attaches a relevant
                       document (e.g., proof of funds, ID). 3. User submits
                       the message. 4. System routes the message to the
                       agent\'s inbox and sends a push/email/SMS
                       notification. 5. Agent logs in, reads the message,
                       and composes a reply. 6. System notifies the user of
                       the agent\'s response. 7. Conversation continues in
                       a threaded message view accessible in both
                       dashboards.

  **Alternative        4a. Outside business hours --- AI chatbot
  Flows**              acknowledges receipt and sets response expectation.
                       4b. Agent does not respond within 48 hours ---
                       system sends an automated follow-up nudge to the
                       agent.

  **Postconditions**   A message thread exists in both the user\'s and
                       agent\'s dashboards. Response SLA clock is started.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**UC-005: Agent Manages Listing Performance & Leads**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-005**

  **Title**            Estate Agent Reviews Listing Performance and Manages
                       Leads

  **Actor(s)**         Estate Agent

  **Goal**             Agent gains actionable insight into listing
                       performance and manages inbound leads efficiently.

  **Preconditions**    Agent has at least one active listing with some
                       public engagement.

  **Trigger**          Agent navigates to the Agent Dashboard and selects a
                       listing.

  **Main Flow**        1\. Agent views the listing performance summary:
                       total views, unique visitors, photo views, save
                       rate, enquiry conversion rate, and days on market.
                       2. Agent reviews the list of inbound leads
                       (enquiries and viewing requests) sorted by date. 3.
                       Agent updates the status of each lead (New,
                       Contacted, Viewing Booked, Offer Received, Closed).
                       4. Agent uses the bulk-action tool to send a
                       follow-up message to all uncontacted leads. 5. Agent
                       updates the listing price or description in response
                       to performance data. 6. Agent views a 30-day trend
                       chart for views and enquiries. 7. Agent exports lead
                       data as a CSV for CRM integration.

  **Alternative        4a. Agent activates a \'Promoted Listing\' boost to
  Flows**              increase visibility --- system confirms and debits
                       credits.

  **Postconditions**   Agent has a clear picture of listing performance and
                       has taken follow-up actions on leads.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**UC-006: Administrator Verifies Agent and Moderates Content**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-006**

  **Title**            Platform Administrator Verifies an Agent and
                       Moderates a Reported Listing

  **Actor(s)**         Platform Administrator (Primary)

  **Goal**             Admin approves a legitimate agent and removes a
                       fraudulent listing, maintaining platform trust.

  **Preconditions**    A new agent has submitted a registration. A public
                       user has flagged a listing.

  **Trigger**          Admin logs into the back-office dashboard and views
                       the Moderation Queue.

  **Main Flow**        1\. Admin opens a pending agent verification
                       request. 2. Admin reviews submitted documents (ID,
                       company registration, proof of address). 3. Admin
                       performs a Companies House lookup and
                       cross-references registration details. 4. Admin
                       approves the agent --- system triggers welcome email
                       and activates account. 5. Admin opens a reported
                       listing from the Moderation Queue. 6. Admin reviews
                       the listing content, reported reason, and public
                       user complaint. 7. Admin determines the listing
                       violates misleading advertising guidelines. 8. Admin
                       removes the listing, issues a formal warning to the
                       agent, and logs the action. 9. Admin sends an
                       automated notification to the reporting user
                       confirming action taken.

  **Alternative        4a. Documents are insufficient --- Admin sends a
  Flows**              request for re-submission with specific guidance.
                       8a. Listing is not in violation --- Admin dismisses
                       the report and records the decision.

  **Postconditions**   Agent account is active and in good standing.
                       Fraudulent listing is removed from the platform.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**UC-007: User Compares Properties and Makes an Offer**

  -------------------- ----------------------------------------------------
  **Use Case ID**      **UC-007**

  **Title**            Public User Compares Shortlisted Properties and
                       Submits an Offer

  **Actor(s)**         Public User (Primary), Estate Agent (Secondary)

  **Goal**             User systematically narrows down their shortlist and
                       formally registers their offer through the platform.

  **Preconditions**    User is authenticated and has at least 2 properties
                       saved to their shortlist.

  **Trigger**          User accesses \'My Shortlist\' from their dashboard.

  **Main Flow**        1\. User selects up to 4 properties from their
                       shortlist for comparison. 2. System generates a
                       side-by-side comparison table covering price, size,
                       bedrooms, EPC, transport, and school ratings. 3.
                       User annotates properties with personal notes and
                       ratings. 4. User decides on a preferred property and
                       clicks \'Make an Offer\'. 5. User enters their offer
                       amount, indicates buyer status
                       (cash/mortgage-in-principle), and uploads proof of
                       funds. 6. System sends the offer to the agent with a
                       formal offer notification. 7. Agent reviews and
                       responds via the platform: Accept, Counter-offer, or
                       Decline. 8. User is notified of the agent\'s
                       response. 9. If accepted, both parties receive
                       next-step guidance (conveyancing checklist,
                       solicitor recommendations).

  **Alternative        7a. Agent submits a counter-offer --- user can
  Flows**              accept, counter again, or withdraw.

  **Postconditions**   Offer is formally recorded. Listing status is
                       updated to \'Under Offer\'. Both parties have a
                       documented record.

  **Priority**         Must Have
  -------------------- ----------------------------------------------------

**7. High-Level System Architecture**

The platform shall be built on a microservices architecture deployed in
a managed Kubernetes environment (EKS/GKE). The following core services
shall be developed independently:

  -----------------------------------------------------------------------
  **Service**           **Responsibilities**
  --------------------- -------------------------------------------------
  Identity Service      User auth, RBAC, OAuth, MFA, session management

  Listing Service       CRUD for property listings, status management,
                        enrichment pipeline

  Search Service        Elasticsearch-powered full-text, geo, and faceted
                        search with ML ranking

  Media Service         Image/video upload, transcoding, CDN
                        distribution, 3D tour embedding

  Appointment Service   Calendar management, slot availability, booking
                        workflow, cal-sync API

  Messaging Service     Real-time WebSocket messaging, threaded
                        conversations, push delivery

  Notification Service  Email (SES/SendGrid), SMS (Twilio), push
                        notifications (FCM/APNs)

  Analytics Service     Event tracking, listing performance dashboards,
                        admin BI reporting

  Payment Service       Stripe integration for subscription billing,
                        listing boosts, invoicing

  Admin Service         Moderation queue, user management, GDPR tooling,
                        fraud detection
  -----------------------------------------------------------------------

**8. Key Data Entities**

The following core entities form the data model backbone of the
platform:

-   User --- id, role, name, email, phone, avatar, verified, created_at,
    mfa_enabled

-   Agent --- id, user_id, agency_id, licence_number, bio, rating,
    verified_at

-   Agency --- id, name, logo, registration_number, address,
    subscription_tier, created_at

-   Property --- id, agent_id, title, description, address, lat, lng,
    type, tenure, price, bedrooms, bathrooms, epc_rating, status,
    created_at, updated_at

-   Media --- id, property_id, type (photo/video/floorplan/3d_tour),
    url, order, created_at

-   Appointment --- id, property_id, agent_id, user_id, scheduled_at,
    type (in_person/virtual), status, video_link, notes

-   Message --- id, thread_id, sender_id, recipient_id, body,
    attachments\[\], read_at, created_at

-   SavedSearch --- id, user_id, criteria_json, alert_frequency,
    last_alerted_at

-   Shortlist --- id, user_id, property_id, notes, created_at

-   Review --- id, agent_id, reviewer_id, rating, body,
    verified_transaction, created_at

-   Offer --- id, property_id, buyer_id, amount, status
    (pending/accepted/rejected/countered), proof_of_funds_url,
    submitted_at

**9. Integration Requirements**

  ------------------------------------------------------------------------
  **Integration**    **Provider / Standard**  **Purpose**
  ------------------ ------------------------ ----------------------------
  Mapping &          Google Maps Platform /   Address validation, map
  Geocoding          OS Places                display, commute time

  Property Portal    Rightmove RTDF, Zoopla   Syndicating listings to
  Feeds              API, OnTheMarket         partner portals

  Calendar Sync      Google Calendar, MS      Two-way appointment
                     Graph, CalDAV            synchronisation

  Video Calling      WebRTC / Zoom / Teams    Virtual viewing appointments
                     API                      

  Payments           Stripe                   Subscriptions, one-off
                                              boosts, invoicing

  Email Delivery     SendGrid / AWS SES       Transactional and marketing
                                              email

  SMS                Twilio / AWS SNS         Appointment reminders, OTP,
                                              alerts

  ID Verification    Onfido / Yoti            Agent KYC/AML identity
                                              checks

  Analytics          Segment + Mixpanel /     User behaviour and platform
                     Amplitude                analytics

  EPC Data           MHCLG EPC Register API   Auto-populate EPC ratings
                                              from register

  3D Tours           Matterport Embed API     Embedding virtual property
                                              walkthroughs
  ------------------------------------------------------------------------

**10. Delivery Roadmap**

  ------------------------------------------------------------------------
  **Phase**   **Target**       **Deliverables**
  ----------- ---------------- -------------------------------------------
  Phase 0     Month 1--2       Architecture design, tech stack
                               finalisation, design system, CI/CD
                               pipeline, cloud infrastructure setup

  Phase 1     Month 3--5       Core auth, agent registration/verification,
                               property listing CRUD, basic public search,
                               admin panel MVP

  Phase 2     Month 6--8       Advanced search (map, AI), appointment
                               booking, in-app messaging, media upload and
                               CDN, notification service

  Phase 3     Month 9--10      Agent analytics dashboard, public user
                               dashboard, reviews/ratings, property
                               comparison, saved search alerts

  Phase 4     Month 11--12     Native iOS and Android apps,
                               payment/subscription module, portal feed
                               syndication, penetration testing, GDPR
                               audit

  Phase 5     Month 13+        AI chatbot, offer management, commute-time
                               search, internationalisation, mortgage
                               partner integration, performance tuning
  ------------------------------------------------------------------------

**11. Assumptions & Constraints**

**11.1 Assumptions**

1.  Third-party API providers (Google Maps, Stripe, Twilio) will remain
    available at agreed pricing tiers.

2.  Estate agent users will have access to a modern smartphone or
    desktop browser.

3.  Initial market focus is the United Kingdom, with international
    expansion in Phase 5.

4.  The engineering team will consist of a minimum of 8 full-stack
    engineers, 2 QA engineers, 1 DevOps engineer, and 1 UX designer.

**11.2 Constraints**

5.  The platform must comply with UK GDPR and the Data Protection Act
    2018 from day one of public launch.

6.  Agent identity verification must comply with HMRC AML supervision
    requirements.

7.  The platform must not store card data directly --- all payment
    handling must be delegated to PCI-DSS compliant processors (Stripe).

8.  Open-house and virtual viewing functionality is dependent on
    successful third-party video API integration.

**12. Glossary**

  -----------------------------------------------------------------------
  **Term**         **Definition**
  ---------------- ------------------------------------------------------
  Agent            A licensed estate agent or letting agent who lists and
                   manages properties on the platform.

  BRD              Business Requirements Document --- this document.

  EPC              Energy Performance Certificate --- a mandatory rating
                   for properties in England, Wales, and Northern
                   Ireland.

  GDPR             General Data Protection Regulation --- EU/UK data
                   privacy law.

  KYC/AML          Know Your Customer / Anti-Money Laundering ---
                   identity verification and financial crime prevention
                   obligations.

  MFA              Multi-Factor Authentication --- a security mechanism
                   requiring multiple verification steps.

  PropTech         Property Technology --- technology-driven innovation
                   in the real estate industry.

  RBAC             Role-Based Access Control --- restricting system
                   access based on user roles.

  SLA              Service Level Agreement --- a commitment to a
                   measurable standard of service (e.g., uptime, response
                   time).

  Tenure           The legal basis of ownership: Freehold (outright
                   ownership) or Leasehold (ownership for a fixed term).

  WCAG             Web Content Accessibility Guidelines --- international
                   standards for digital accessibility.
  -----------------------------------------------------------------------

*End of Document --- Estate Agent Platform BRD v1.0*
