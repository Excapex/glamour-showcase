<div align="center">

# Glamour

**Booking and operations SaaS for salons. It refills cancelled slots and heads off no-shows automatically.**

Multi-tenant · WhatsApp-native · Serbian & English · embeddable booking widget · built solo by one developer, now a two-person team

![Java](https://img.shields.io/badge/Java-21-ED8B00?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5-6DB33F?logo=springboot&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-4169E1?logo=postgresql&logoColor=white)
![Flyway](https://img.shields.io/badge/Flyway-51%20migrations-CC0200?logo=flyway&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind-4-06B6D4?logo=tailwindcss&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)
![Testcontainers](https://img.shields.io/badge/Testcontainers-integration-2B7BB9)
![Playwright](https://img.shields.io/badge/Playwright-E2E-2EAD33?logo=playwright&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)
![WhatsApp](https://img.shields.io/badge/WhatsApp-Cloud%20API-25D366?logo=whatsapp&logoColor=white)
![Stripe](https://img.shields.io/badge/Stripe-integration-635BFF?logo=stripe&logoColor=white)

**Status:** MVP-complete, pre-launch · **Source:** private (commercial). Code walkthrough and live demo available on request.

</div>

![Dashboard](screenshots/01-dashboard.png)

---

## What it does

Small salons in Serbia and the wider region still take bookings through paper diaries, Instagram DMs and WhatsApp. When a client cancels late or doesn't show up, the chair usually sits empty. Glamour handles bookings and also works to get that revenue back:

| Problem | What Glamour does | Outcome |
|---|---|---|
| Late cancellations leave empty chairs | **Slot recovery**: when a booking is cancelled, the system offers the slot over WhatsApp to the best-matching clients (waitlist first, then regulars whose usual pattern fits). The first client to claim it gets the slot. | Cancelled slots are **re-sold, not lost** |
| No-shows | **Attendance Protection**: every new booking gets an explainable risk score. Only risky bookings get a one-tap WhatsApp confirmation. Trusted regulars and first-time clients are never pestered. | Fewer no-shows, with revenue protected and measured |
| Regulars drift away | **Win-Back**: learns each client's visit cadence and invites them back once they're overdue | Lapsed clients reactivated |
| Booking friction | A branded public booking page per salon and **natural-language WhatsApp booking** ("šišanje prekosutra u 15h") | Clients can book 24/7 without an account |
| Salons already have a website | **Embeddable booking widget**: a direct link, an iFrame snippet or an auto-resizing script, for the whole menu or one service, pasted into WordPress, Wix or any HTML block | Booking happens on the salon's own site |
| Running the business | Calendar (day/week/month), CRM, services and per-staff pricing, staff hours and exceptions, analytics, PDF reports | One tool instead of five |

Every automation reports its **outcome in money** ("30,000 RSD protected this month"), not just "messages sent."

---

## Screenshots

> All data shown is fictional seed data for a demo salon ("Studio Aurora"). Phone numbers are placeholders and emails use `@example.com`.

| | |
|---|---|
| **Calendar: week view**<br>![Calendar week](screenshots/02-calendar-week.png) | **Calendar: day view**<br>![Calendar day](screenshots/03-calendar-day.png) |
| **Public booking page (per-salon branding)**<br>![Public booking](screenshots/04-public-booking-services.png) | **Real availability, computed server-side**<br>![Availability](screenshots/05-public-booking-availability.png) |
| **Automations: revenue framed as outcomes**<br>![Automations](screenshots/07-automations-revenue-recovery.png) | **Attendance Protection: explainable rules and guardrails**<br>![Attendance protection](screenshots/08-attendance-protection-detail.png) |
| **WhatsApp inbox (bot and human in one thread)**<br>![WhatsApp inbox](screenshots/10-whatsapp-inbox.png) | **Clients / CRM**<br>![Clients](screenshots/06-clients-crm.png) |
| **Analytics**<br>![Analytics](screenshots/09-analytics.png) | **Waitlist that feeds slot recovery**<br>![Waitlist](screenshots/11-waitlist.png) |
| **SUPERADMIN: provision a whole salon from one form**<br>![Tenant provisioning](screenshots/13-superadmin-tenant-provisioning.png) | **Integrations (WhatsApp, Stripe, Google Calendar)**<br>![Integrations](screenshots/12-settings-integrations.png) |
| **Fully localized: Serbian UI**<br>![Serbian dashboard](screenshots/16-dashboard-serbian.png) | **Service menu with categories and staff assignment**<br>![Services](screenshots/15-services.png) |
| **New: "Embed booking" on the service menu (whole salon or one service)**<br>![Embed entry](screenshots/19-services-embed-entry.png) | **Copy-paste embed code: link, iFrame or auto-resizing script**<br>![Embed code dialog](screenshots/20-embed-code-dialog.png) |

<p align="center">
  <img src="screenshots/21-embedded-widget-on-salon-site.png" width="820" alt="Glamour booking widget embedded on a fictional salon website" />
  <br/><em>The widget pasted into a fictional salon website. The embed pre-selects the service (Balayage), so the client lands straight on choosing a stylist.</em>
</p>

<p align="center">
  <img src="screenshots/17-mobile-public-booking.png" width="260" alt="Mobile public booking: services" />
  &nbsp;&nbsp;
  <img src="screenshots/18-mobile-public-booking-time.png" width="260" alt="Mobile public booking: time picker" />
  <br/><em>Public booking on mobile (375 px)</em>
</p>

---

## Architecture

```mermaid
flowchart LR
    subgraph Clients
        B[Salon staff<br/>browser]
        P[End client<br/>public booking page]
        W[End client<br/>WhatsApp]
    end

    subgraph Frontend["Next.js 16 · React 19"]
        APP[Admin app<br/>calendar · CRM · analytics]
        PUB[Public booking<br/>/book/:slug]
    end

    subgraph Backend["Spring Boot 3.5 · Java 21"]
        SEC[JWT auth filter<br/>tenant context · RBAC · rate limiting]
        API[REST API<br/>31 controllers]
        BOOK[Booking orchestrator<br/>availability · concurrency guard]
        WA[WhatsApp pipeline<br/>deterministic parser → LLM fallback]
        AUTO[Automation engine<br/>event → signal → decision → action → outcome]
        REC[Slot-recovery orchestrator<br/>tiered offers · claim tokens]
        JOBS[Schedulers<br/>outbox · reminders · reconciliation]
    end

    DB[(PostgreSQL 18<br/>Flyway-managed schema)]
    META[Meta WhatsApp<br/>Cloud API]
    EXT[Stripe · Google Calendar<br/>SMTP · Sentry]
    LLM[OpenAI-compatible LLM<br/>strict JSON schema]

    B --> APP
    P --> PUB
    APP & PUB -->|HTTPS / JSON| SEC --> API
    W <-->|webhooks · HMAC-verified| META <--> WA
    API --> BOOK & AUTO & REC
    WA --> BOOK
    WA -.->|low-confidence only| LLM
    BOOK & AUTO & REC & JOBS --> DB
    JOBS -->|transactional outbox| META
    API --> EXT
```

### The outcome-automation engine

Newer automation is built on one pipeline. Every step is persisted, idempotent and auditable, so the product can show *why* it acted and *how much money* the action saved.

```mermaid
flowchart LR
    E["Domain event<br/>BOOKING_CREATED · BOOKING_CANCELED<br/>CLIENT_LAPSED · WHATSAPP_REPLY"] --> S["Signals<br/>no-show risk · client lapse score"]
    S --> D["Decision engine<br/>playbook rules + guardrails<br/>(quiet hours, 1 msg/client/day,<br/>never first-timers)"]
    D --> A["Actions<br/>WhatsApp confirmation / win-back<br/>scheduled · retried"]
    A --> O["Outcomes<br/>CONFIRMED_BY_REPLY · REVENUE_PROTECTED<br/>CLIENT_REACTIVATED"]
    O -->|attribution| R["Owner dashboard<br/>'30,000 RSD protected'"]
```

A simplified illustration of the shape of that contract (not production code):

```java
// Illustrative only: simplified from the real design.
interface Playbook {
    String type();                                   // e.g. ATTENDANCE_PROTECTION
    Optional<CandidateDecision> evaluate(DomainEvent event, SignalSnapshot signals);
}

record CandidateDecision(UUID clientId, UUID bookingId, int score, Map<String, Object> reasons) {}

// Guardrails run before any action is queued. They can veto, but never create, a decision.
interface Guardrail {
    boolean allow(CandidateDecision decision, ClientProfile client);
}
```

---

## Tech stack

| Layer | Technology |
|---|---|
| **Backend** | Java 21, Spring Boot 3.5 (Web, Data JPA, Security, Validation, Actuator, Mail), Gradle |
| **Database** | PostgreSQL 18, **Flyway-only** schema changes (Hibernate `validate`), GiST exclusion constraints, advisory locks |
| **Frontend** | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS 4, shadcn/ui (Radix), TanStack Query, Zustand, react-hook-form + Zod, Recharts |
| **i18n** | next-intl (EN / SR, 1,133 keys each) + backend message bundles |
| **Messaging** | Meta WhatsApp Cloud API (interactive lists and buttons, templates), SMTP |
| **AI** | OpenAI-compatible LLM as a *fallback* intent parser (strict JSON schema, per-tenant metered) |
| **Integrations** | Stripe, Google Calendar (OAuth2), JasperReports (PDF) |
| **Security** | JWT, role-based `@PreAuthorize`, Bucket4j rate limiting, AES-256-GCM credential vault, HMAC-SHA256 webhook verification |
| **Testing** | JUnit 5, Spring Security Test, **Testcontainers** (real Postgres 18), Vitest + Testing Library, **Playwright** E2E, k6 load script |
| **Ops** | Docker (multi-stage, non-root images), Docker Compose, GitHub Actions CI, Spring Actuator health, Sentry |

---

## Engineering highlights

**Multi-tenancy with isolation that is actually tested**
- Tenant identity comes only from the verified JWT and flows through a request-scoped tenant context. Every repository query is tenant-scoped.
- Composite `(tenant_id, id)` foreign keys at the database level, so a row can't reference another tenant's client, staff or location even if application code is wrong.
- Dedicated cross-tenant access tests and a **multi-tenant simulation** (5 salons, 17 staff, 130 clients, ~255 concurrent booking attempts), with **0 isolation breaks, 0 duplicate bookings, 0 mistargeted messages**.

**Correctness under concurrency**
- Double-booking is blocked **in the database** by a PostgreSQL **GiST exclusion constraint** on `(tenant, staff, time range)`. The app layer also takes per-staff **advisory locks** before it checks availability.
- The slot-recovery orchestrator uses `FOR UPDATE SKIP LOCKED` polling, so multiple instances can run safely. Claim tokens are **stored hashed (SHA-256)**, expire after a short window, and a partial unique index allows only one active claim per slot.

**RBAC and platform administration**
- Four roles (SUPERADMIN / ADMIN / RECEPTION / STAFF) enforced by 112 method-level `@PreAuthorize` checks across 31 controllers, and mirrored in the UI.
- One SUPERADMIN form provisions a full salon in a single transaction: tenant, plan features, location, working hours, admin user and branding.

**Schema discipline**
- **51 versioned Flyway migrations** (plus a prod-only cleanup migration), with Hibernate in `validate` mode. The whole chain applies to an empty database in about 1.2 s.
- A migration integration test runs the full chain against real Postgres on every CI run.

**Pragmatic AI**
- WhatsApp booking uses **deterministic parsing first**. The LLM is called only when confidence is low, and its output must match a strict JSON schema and is tenant-scoped.
- Catalog questions ("koliko košta…", "ko radi…") are answered from the database without calling the AI.
- No-show risk is an **explainable score** (the owner can see *why* a booking was flagged), not a black box.

**Reliable messaging**
- A transactional **outbox** for WhatsApp with retries and exponential backoff. Usage is metered only after confirmed delivery.
- Inbound webhooks are **HMAC-verified** and de-duplicated. Tenant integration credentials are **encrypted at rest (AES-256-GCM)**.

**Embeddable booking widget**
- Each salon (or a single service) gets a copy-paste embed: a direct link, a plain iFrame, or a script that creates the iFrame and resizes it to the content.
- The booking page reports its height to the host page with `postMessage` (watched by a `ResizeObserver`), so the widget never shows a nested scrollbar.
- A `?service=` parameter pre-selects the service and skips ahead in the flow; multi-location salons still pick a location first, without losing the pre-selected service.

**Performance work, measured**
- Appointment pagination uses a two-query pattern (page the IDs, then hydrate them) to avoid Hibernate's in-memory pagination trap. On 10k rows it was **~13× faster** at the SQL layer (~1.7 ms vs ~23 ms).
- A k6 load script covers the public booking path, with p95 thresholds encoded.

**Testing and CI**
- **~250 backend test methods** in 32 test classes: unit tests for the decision engine, parsers and schedulers, plus Testcontainers integration tests (booking overlap, role authorization, Flyway, pagination, multi-tenant simulation).
- Frontend: Vitest (permissions, API contract, automation UI) and Playwright E2E (role logins, public booking, branding propagation).
- GitHub Actions runs a backend build with unit tests, an integration suite on Testcontainers, frontend lint and build, npm audit, and Docker image builds.

**Observability and ops**
- Actuator health checks wired into Docker healthchecks, Sentry on both backend and frontend, and an audit log.
- Fail-fast startup when a required production secret is missing. Backup and restore scripts have been tested with a full round trip.

---

## By the numbers

| | |
|---|---|
| Lines of code (non-blank, non-comment) | **~59k** in total: ~23k Java (main), ~6.6k Java (tests), ~22.6k TypeScript/TSX, ~2.3k SQL |
| Backend | 31 REST controllers · 139 endpoints · 47 JPA entities · 42 repositories · 14 scheduled jobs |
| Database | 51 Flyway migrations (V1–V51) + 1 prod-only |
| Frontend | 20 routes · 83 components · 23 data hooks · 2 languages |
| Tests | 32 backend test classes (~250 test methods) · 7 frontend test/spec files (42 cases) |
| Delivery | ~155 commits · 15 PRs · 57 tracked issues (as of Oct 2026) |

---

## My role

I designed and built Glamour **solo, end to end**, from zero to MVP:

- **Product:** problem discovery with salon owners, positioning ("the booking system that gives you your money back"), feature scope, MVP freeze and a launch-readiness plan.
- **Design:** UX and UI for the admin app and the public booking flow, the branding system (per-salon presets and colors), and the bilingual copy.
- **Backend:** domain model, multi-tenancy, security, booking engine, WhatsApp pipeline, automation engine, integrations.
- **Frontend:** Next.js app, data layer, i18n, E2E tests.
- **Infrastructure:** Docker, CI, migrations strategy, backups, production hardening and runbooks.

**Now a two-person team.** In mid-2026 **Uroš Vasiljević** joined. We work through issues, branches and cross-reviewed pull requests: the reviewer is never the author, and the reviewer merges. Uroš covers UX polish, localization, documentation and frontend tests. He built the embeddable booking widget and repaired the automations-page test suite, and he is working on a dashboard light/dark theme and a staff-column calendar day view. I own infrastructure, security, the database and migrations, and I review his work. My recent work includes fixing the CI toolchain (npm 10 lockfile sync, the Postgres test profile), a local reproduction of the CI pipeline, and revoking issued JWTs when a password changes or an account is deactivated or deleted.

---

## Status

**MVP-complete and pre-launch.** The core flow works end to end: a salon is provisioned, the owner configures services, staff and hours, a client books on the web or over WhatsApp, the booking appears on the calendar, and reminders and recovery run automatically. The remaining work is deployment (still in progress: there is no public deployment or live booking page yet), legal pages and onboarding the first beta salons. Since the MVP, work has gone mostly into hardening, CI and tests, plus one new feature: the embeddable booking widget.

---

## Why is the code private?

Glamour is a **commercial product** preparing for its first paying customers, so the repository stays private. This page shows the product, the architecture and the engineering decisions without publishing the implementation.

**Happy to walk through the code live, share a private demo, or discuss any design decision in depth.** Just ask.

---

## Contact

**Sergej Stojanović**. Full-stack developer and 4th-year Information Technologies student at the Faculty of Sciences (PMF), University of Novi Sad.

- GitHub: [@Excapex](https://github.com/Excapex)
- LinkedIn: [sergej-stojanovic-4a875836a](https://www.linkedin.com/in/sergej-stojanovic-4a875836a/)
- Email: sergej.stojanovic.04.ns@gmail.com
