# SCOPE.md — DevPulse MVP Scope

> **Project:** DevPulse — Real-Time API Health & Incident Dashboard
> **Author:** [Your Name]
> **Created:** [Date]
> **Status:** Draft v1.0
> **Target Ship Date:** [Date + 6 weeks]

---

## 🎯 Project Vision

A self-hostable platform where developers register their APIs and get:
- Continuous uptime + latency monitoring
- Real-time incident detection with alerts
- A public status page they can share with users
- AI-generated incident postmortems

**Elevator pitch:** *"UptimeRobot + statuspage.io, but with AI postmortems and a Python-first stack."*

---

## 👤 Target User

**Primary persona:** Indie developer or small SaaS team (2–10 people) who:
- Ships APIs and needs uptime visibility
- Wants a public status page but doesn't want to pay $29+/month for Statuspage.io
- Is comfortable self-hosting or using a lightweight SaaS
- Values a Python-first stack for extensibility

**Secondary persona:** Recruiters and hiring managers evaluating my fullstack skills (this project is also a portfolio piece).

---

## ✅ In Scope (MVP)

Everything below ships in v1.0.

### Authentication & Users
- [ ] GitHub OAuth login
- [ ] JWT access + refresh tokens
- [ ] Single-user accounts (no teams in MVP)
- [ ] User profile (email, name, avatar from GitHub)

### Monitors
- [ ] Create a monitor with: name, URL, HTTP method, expected status code, check interval, timeout
- [ ] Supported methods: GET, HEAD, POST
- [ ] Minimum interval: 30 seconds
- [ ] Edit and delete monitors
- [ ] Enable / disable a monitor
- [ ] Per-monitor "public" toggle (shown on status page or not)

### Checker Engine
- [ ] Background worker pings monitors on schedule
- [ ] Records: timestamp, status code, latency (ms), error message, region
- [ ] Single region (primary) in MVP
- [ ] Retries once on timeout before marking failure
- [ ] Consecutive-failure and consecutive-success thresholds for incident state

### Incident Detection
- [ ] Auto-create incident after 3 consecutive failures
- [ ] Auto-resolve incident after 1 successful check
- [ ] Incident stores: start, end, duration, cause, affected monitor
- [ ] Manual incident creation (for planned maintenance)

### Results & Analytics
- [ ] Time-series storage of all check results
- [ ] Latency percentiles (p50, p95, p99) via time buckets
- [ ] Uptime percentage per monitor (24h, 7d, 30d, 90d)
- [ ] 90-day uptime bar (like statuspage.io)
- [ ] Configurable retention (default 90 days)

### Alerts
- [ ] Email alerts (via Resend or SMTP)
- [ ] Discord webhook alerts
- [ ] Slack webhook alerts
- [ ] Alert on incident open + incident resolve
- [ ] Deduplication window (don't spam)
- [ ] Retry with exponential backoff

### Real-Time
- [ ] WebSocket feed of status changes + new checks
- [ ] Live dashboard updates without polling

### Public Status Page
- [ ] Public URL per user: `devpulse.app/status/{slug}`
- [ ] Shows: current status, active incidents, 90-day uptime bar, incident history
- [ ] No authentication required
- [ ] Mobile-responsive
- [ ] Custom slug

### Frontend
- [ ] Login page (GitHub OAuth)
- [ ] Dashboard (list monitors, status pills, sparklines)
- [ ] Monitor detail page (latency chart, incident timeline, recent checks)
- [ ] Create / edit monitor form
- [ ] Settings (profile, alert channels)
- [ ] Public status page (shareable)
- [ ] Live updates via WebSocket

### Observability (of DevPulse itself)
- [ ] `/health` endpoint
- [ ] Structured JSON logs
- [ ] Basic error tracking (Sentry or similar)

### Deployment
- [ ] Dockerized (backend, worker, frontend)
- [ ] Deployed to Fly.io (backend + worker) + Vercel (frontend)
- [ ] Managed Postgres (Neon/Supabase) + Redis (Upstash)
- [ ] CI/CD via GitHub Actions

---

## 🎁 Nice to Have (Post-MVP — v1.1+)

These are explicitly *not* in v1.0. Do not start them until MVP ships.

- [ ] AI-generated incident postmortems (first post-MVP feature)
- [ ] Multi-region checks (2–3 regions)
- [ ] Team / organization accounts
- [ ] Role-based access control (owner, admin, viewer)
- [ ] Custom domains for status pages
- [ ] Status page themes / branding (logo, colors)
- [ ] Python SDK published to PyPI
- [ ] Monitoring-as-code (YAML / Terraform)
- [ ] Prometheus metrics endpoint
- [ ] Grafana dashboard for DevPulse itself
- [ ] SMS alerts (Twilio)
- [ ] PagerDuty integration
- [ ] Public API for third-party integrations
- [ ] Webhooks (outbound)
- [ ] Billing / Stripe integration

---

## ❌ Out of Scope (Explicitly Not Doing)

Saying "no" to these protects the MVP timeline.

- ❌ Building auth from scratch (using OAuth + JWT libs)
- ❌ Microservices architecture (monolith + workers is right)
- ❌ Kubernetes (Docker Compose + Fly.io is enough)
- ❌ Mobile native apps (responsive web only)
- ❌ Browser / synthetic user monitoring (only HTTP checks)
- ❌ Log aggregation / APM (that's a different product)
- ❌ On-prem / self-hosted distribution packaging (Docker only, no Helm)
- ❌ Multi-tenant billing
- ❌ Custom AI model training (use hosted LLM APIs only)
- ❌ Support for non-HTTP protocols (TCP, ICMP, gRPC) in v1.0

---

## 📊 Success Criteria

MVP is considered complete when **all** of these are true:

- [ ] A new user can sign up via GitHub in under 30 seconds
- [ ] A user can register a monitor and see the first check within 60 seconds
- [ ] Breaking a monitored endpoint triggers an alert in Discord within 90 seconds
- [ ] Public status page loads in under 2 seconds and is shareable
- [ ] WebSocket pushes updates without full page refresh
- [ ] 100% of the "In Scope" checklist items are done
- [ ] Deployed and reachable at a public URL
- [ ] README with architecture diagram, demo video, and live link
- [ ] At least one real external user (friend / colleague) has signed up
- [ ] I can give a 5-minute walkthrough without notes

---

## 🚫 Anti-Goals

Things I will actively avoid, even if tempting:

- Chasing feature parity with UptimeRobot / Better Stack
- Perfecting UI before the core loop works
- Building features no user has asked for
- Rewriting working code for "cleanliness"
- Adding dependencies without a clear need
- Spending more than 1 day on any single blocker without asking for help

---

## 🛠 Constraints

| Constraint | Detail |
|-----------|--------|
| **Time budget** | ~15–20 hrs/week for 6 weeks |
| **Money budget** | < $20/month on infra (free tiers preferred) |
| **Team size** | Solo |
| **Must-use stack** | Python (backend), TypeScript (frontend) |
| **Deployment** | Cloud only, no on-prem in MVP |

---

## 🧱 Tech Stack (Locked for MVP)

**Backend**
- Python 3.11+
- FastAPI
- Celery + Redis
- SQLAlchemy + Alembic
- PostgreSQL + TimescaleDB
- httpx (async HTTP client)
- Pydantic v2

**Frontend**
- TypeScript (strict)
- React 18 + Vite
- TanStack Query
- Zustand
- Tailwind CSS + shadcn/ui
- React Router v6
- Recharts or uPlot

**Infra**
- Docker + Docker Compose (local)
- Fly.io (backend + worker)
- Vercel (frontend)
- Neon or Supabase (Postgres)
- Upstash (Redis)

> **Rule:** No stack changes after Week 1 unless something is fundamentally broken.

---

## 📅 Milestones

| Week | Milestone | Definition of Done |
|------|-----------|-------------------|
| 1 | Backend foundation | Auth works, migrations run, `/health` returns 200 |
| 2 | Monitoring engine | Monitors ping on schedule, incidents auto-created |
| 3 | Alerts + real-time | Discord alert fires, WebSocket pushes updates |
| 4 | Full frontend | CRUD works from UI, public status page shareable |
| 5 | Differentiation + polish | AI postmortems, observability, SDK |
| 6 | Test, deploy, showcase | Production URL, demo video, blog post |

**Fallback if time is short:** cut AI postmortems, multi-region, and SDK. Ship the core loop.

---

## ⚠️ Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| Scope creep | High | High | This SCOPE.md. Weekly review. |
| TimescaleDB setup issues | Medium | Medium | Fall back to plain Postgres + manual bucketing |
| Celery complexity | Medium | Medium | Fall back to ARQ (async-native) if blocked > 1 day |
| LLM cost overrun (post-MVP) | Low | Medium | Rate limits + caching + small model |
| Losing motivation | Medium | High | Public commit log; weekly demo to a friend |
| Deployment issues | Low | Medium | Deploy to staging in Week 3, not Week 6 |

---

## 🧭 Guiding Principles

1. **Ship the loop, then polish.** A working ugly thing beats a beautiful non-working thing.
2. **One feature per branch.** Keep PRs small and reviewable.
3. **Demo every Friday.** Even if it's just to yourself.
4. **Don't build what you can install.** Auth, UI components, LLMs — use libraries.
5. **Read the docs of existing tools** (Upptime, OpenStatus, StatusWise) before reinventing.
6. **If blocked > 1 day, ask for help or pivot.** Don't grind.

---

## 📝 Change Log

| Date | Change | Reason |
|------|--------|--------|
| [Date] | Initial scope | Project kickoff |
|  |  |  |
|  |  |  |

> **Rule:** Any change to "In Scope" requires a change log entry + re-estimation of timeline.
