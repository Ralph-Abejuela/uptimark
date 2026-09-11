# Uptimark — Build Progress Tracker

> **Goal:** Ship a working, deployable, demo-able status page + uptime monitor in 4–6 weeks.
> **Stack:** FastAPI + Celery + TimescaleDB + Redis (backend) · React + TS + Vite (frontend)

---

## 📐 Phase 0: Planning & Setup

### 0.1 Define MVP Scope
- [ ] Write MVP scope in a `SCOPE.md` file
  - [ ] Must-have list finalized
  - [ ] Nice-to-have list finalized
  - [ ] Out-of-scope list finalized (protects you from scope creep)

### 0.2 Repo Structure
- [ ] Create monorepo `uptimark/`
  - [ ] `backend/` folder scaffolded
  - [ ] `frontend/` folder scaffolded
  - [ ] `docker-compose.yml` created
  - [ ] `README.md` initialized
  - [ ] `.gitignore` for Python + Node
  - [ ] `.env.example` with all required vars

### 0.3 Local Dev Environment
- [ ] Docker Compose services defined
  - [ ] Postgres (TimescaleDB image)
  - [ ] Redis
  - [ ] Backend container
  - [ ] Worker container
  - [ ] Frontend container
- [ ] `Makefile` with shortcuts
  - [ ] `make up`
  - [ ] `make down`
  - [ ] `make migrate`
  - [ ] `make test`
  - [ ] `make lint`
- [ ] Verify: `make up` brings everything online

---

## 🐍 Phase 1: Backend Foundation (Week 1)

### 1.1 FastAPI Skeleton
- [ ] Project initialized with `pyproject.toml`
- [ ] Dependencies: `fastapi`, `uvicorn`, `sqlalchemy`, `alembic`, `pydantic-settings`, `httpx`, `celery`, `redis`, `structlog`, `python-jose`, `passlib`
- [ ] `app/main.py` created
  - [ ] CORS configured
  - [ ] Exception handlers registered
  - [ ] `/health` endpoint working
- [ ] Config module (`app/core/config.py`)
  - [ ] All env vars loaded via `pydantic-settings`
- [ ] Structured logging (structlog/loguru)
  - [ ] JSON output in prod
  - [ ] Pretty output in dev

### 1.2 Database Layer
- [ ] TimescaleDB extension enabled on Postgres
- [ ] SQLAlchemy base + session factory
- [ ] Models created
  - [ ] `users`
  - [ ] `monitors`
  - [ ] `check_results` (hypertable)
  - [ ] `incidents`
  - [ ] `alert_channels`
- [ ] Hypertable migration for `check_results`
  - [ ] Retention policy configured (e.g., 90 days)
  - [ ] Compression policy configured

### 1.3 Migrations
- [ ] Alembic initialized
- [ ] Initial migration generates all tables
- [ ] Hypertable creation migration written
- [ ] Seed script for local dev (`scripts/seed.py`)

### 1.4 Auth
- [ ] GitHub OAuth app registered
- [ ] OAuth callback endpoint (`/auth/github/callback`)
- [ ] JWT access + refresh tokens issued
- [ ] `get_current_user` dependency working
- [ ] Rate limiting on auth endpoints
- [ ] Token refresh endpoint

### ✅ Phase 1 Milestone
- [ ] `curl /health` returns 200
- [ ] Migrations run cleanly from scratch
- [ ] OAuth login flow returns valid JWT
- [ ] Protected route rejects requests without token

---

## ⚙️ Phase 2: Monitoring Engine (Week 2)

### 2.1 Checker Worker
- [ ] Celery app configured with Redis broker
- [ ] `check_monitor` task implemented
  - [ ] Uses `httpx.AsyncClient`
  - [ ] Enforces timeout
  - [ ] Measures latency
  - [ ] Captures status + errors
  - [ ] Writes to `check_results`
- [ ] Celery Beat schedule
  - [ ] Runs every 10s
  - [ ] Queries due monitors (`next_check_at <= now()`)
  - [ ] Enqueues tasks
- [ ] Concurrency control
  - [ ] Redis lock per monitor (prevent overlap)
- [ ] Idempotency keys for check jobs

### 2.2 Monitor CRUD API
- [ ] `POST /monitors`
  - [ ] URL validation
  - [ ] Interval validation (≥ 30s)
  - [ ] Ownership assigned to current user
- [ ] `GET /monitors` (list for current user)
- [ ] `GET /monitors/{id}` (ownership checked)
- [ ] `PATCH /monitors/{id}`
- [ ] `DELETE /monitors/{id}`

### 2.3 Results API
- [ ] `GET /monitors/{id}/results`
  - [ ] `from`, `to`, `bucket` query params
  - [ ] TimescaleDB `time_bucket()` aggregation
  - [ ] Returns p50 / p95 / p99 latency
  - [ ] Returns uptime %
- [ ] `GET /monitors/{id}/status`
  - [ ] Current state (up / down / degraded)

### 2.4 Incident Detection
- [ ] State machine service (`services/incidents.py`)
- [ ] Rule: 3 consecutive failures → incident
- [ ] Rule: 1 success after incident → resolve
- [ ] Writes `incident` rows with timestamps
- [ ] Emits events for Phase 3 (alerts + WebSocket)

### ✅ Phase 2 Milestone
- [ ] Register monitor via API
- [ ] Pings land in DB every 30s
- [ ] Incidents auto-created and auto-resolved
- [ ] Results API returns aggregated stats

---

## 🔔 Phase 3: Alerts & Real-Time (Week 3)

### 3.1 Alert Dispatcher
- [ ] Celery task subscribes to incident events
- [ ] Email channel (Resend or SMTP)
- [ ] Discord webhook channel
- [ ] Slack webhook channel
- [ ] Retry with exponential backoff
- [ ] Deduplication window (don't spam)
- [ ] `alert_channels` CRUD API

### 3.2 WebSocket Live Feed
- [ ] `WS /ws/monitors` endpoint
- [ ] JWT auth (query param or first message)
- [ ] Redis Pub/Sub as fan-out bus
- [ ] Worker publishes status changes
- [ ] API relays to connected clients
- [ ] Reconnect strategy documented

### 3.3 Public Status Page API
- [ ] `GET /public/{slug}` (no auth)
  - [ ] Current status of all public monitors
  - [ ] 90-day uptime bar data
  - [ ] Active incidents
  - [ ] Recent incident history
- [ ] Public slug generation on user creation

### ✅ Phase 3 Milestone
- [ ] Break a monitor → alert arrives in Discord
- [ ] WebSocket pushes live update to client
- [ ] Public status page reachable without auth

---

## 🎨 Phase 4: Frontend (Week 3–4)

### 4.1 Setup
- [ ] Vite + React + TS (strict mode) initialized
- [ ] Tailwind CSS configured
- [ ] shadcn/ui installed
- [ ] TanStack Query installed and configured
- [ ] Zustand for auth/UI state
- [ ] React Router v6 configured

### 4.2 Typed API Client
- [ ] `openapi-typescript` wired to backend OpenAPI schema
- [ ] `apiClient` with auth interceptor
- [ ] Automatic refresh on 401
- [ ] Type-safe request/response helpers

### 4.3 Pages
- [ ] `/login` — GitHub OAuth button
- [ ] `/dashboard`
  - [ ] Monitor list
  - [ ] Status pills
  - [ ] Latency sparklines
- [ ] `/monitors/:id`
  - [ ] Latency chart (uPlot or Recharts)
  - [ ] Uptime % display
  - [ ] Incident timeline
  - [ ] Recent checks table
- [ ] `/monitors/new`
  - [ ] Form with zod + react-hook-form
  - [ ] Client-side validation
- [ ] `/status/:slug` (public, no auth)
  - [ ] Shareable status page
  - [ ] 90-day uptime bar
- [ ] `/settings`
  - [ ] Alert channels management
  - [ ] Profile

### 4.4 Live Updates
- [ ] `useWebSocket` hook
- [ ] On push → invalidate TanStack Query cache
- [ ] Optimistic UI for create/delete monitor

### ✅ Phase 4 Milestone
- [ ] Full CRUD from UI works
- [ ] Live latency chart updates in real-time
- [ ] Public status page shareable
- [ ] No TypeScript `any` in critical paths

---

## 🚢 Phase 5: Polish & Differentiation (Week 5)

### 5.1 AI Postmortem (Differentiator)
- [ ] On incident resolve → enqueue `generate_postmortem(incident_id)`
- [ ] Pull last N check results + error messages
- [ ] LLM prompt designed (OpenAI/Anthropic)
- [ ] Structured output → markdown
- [ ] Stored on `incidents.ai_summary`
- [ ] Rendered on incident page
- [ ] Fallback when LLM fails
- [ ] Cost control (rate limit, cache)

### 5.2 Multi-Region Checks (Stretch)
- [ ] Deploy 2–3 worker nodes in different Fly.io regions
- [ ] Add `region` column to `check_results`
- [ ] UI region selector
- [ ] Side-by-side region comparison

### 5.3 Observability of UptiMark Itself
- [ ] Prometheus `/metrics` endpoint
- [ ] Grafana dashboard in docker-compose
- [ ] Monitor UptiMakrk with UptiMark (meta-flex)

### 5.4 SDK
- [ ] `uptimark-sdk` package created
- [ ] `client.monitors.create(url=...)` works
- [ ] Published to PyPI (test PyPI first)
- [ ] Docs with mkdocstrings

### ✅ Phase 5 Milestone
- [ ] AI postmortem renders on a real incident
- [ ] Multi-region (if attempted) shows regional differences
- [ ] Grafana dashboard shows UptiMark metrics
- [ ] SDK installable from PyPI

---

## 🧪 Phase 6: Testing, Deploy, Showcase (Week 6)

### 6.1 Tests
- [ ] Backend: `pytest` + `httpx.AsyncClient`
- [ ] Testcontainers for Postgres + Redis
- [ ] Frontend: Vitest for hooks + utils
- [ ] Playwright E2E: login → create monitor → see result
- [ ] Coverage ≥ 70% on critical paths
  - [ ] Checker worker tested
  - [ ] Alert dispatcher tested
  - [ ] Auth flow tested

### 6.2 CI/CD
- [ ] GitHub Actions workflow
  - [ ] Lint: `ruff`, `mypy`, `eslint`
  - [ ] Test: backend + frontend
  - [ ] Build: Docker images
  - [ ] Deploy: on merge to `main`
- [ ] Preview deployments
  - [ ] Frontend preview (Vercel)
  - [ ] Backend preview (Fly.io)

### 6.3 Deploy
- [ ] Backend API → Fly.io / Railway
- [ ] Worker → Fly.io (separate app)
- [ ] Frontend → Vercel / Cloudflare Pages
- [ ] Postgres → Neon / Supabase (with Timescale)
- [ ] Redis → Upstash
- [ ] Secrets managed in each platform
- [ ] Custom domain configured (optional)

### 6.4 Showcase Assets
- [ ] README with architecture diagram (Excalidraw)
- [ ] GIFs of key flows
- [ ] Live link in README
- [ ] 90-second demo video (Loom)
- [ ] Blog post: "How I built a status page with FastAPI + TimescaleDB"
- [ ] LinkedIn/X thread with lessons learned

### ✅ Phase 6 Milestone
- [ ] CI green on `main`
- [ ] Production URL works end-to-end
- [ ] Demo video recorded
- [ ] Blog post published

---

## 📅 Timeline Overview

- [ ] **Week 1** — Backend skeleton, DB, auth
- [ ] **Week 2** — Checker worker, monitors API, results API
- [ ] **Week 3** — Alerts, WebSocket, frontend scaffold
- [ ] **Week 4** — Full frontend, public status page
- [ ] **Week 5** — AI postmortems, polish, observability
- [ ] **Week 6** — Tests, deploy, showcase assets

> **If only 3 weeks available:** cut AI postmortems, multi-region, and SDK. Ship the core loop (register → check → alert → dashboard → public page).

---

## 🎤 Interview Talking Points (build deliberately)

- [ ] Can explain why TimescaleDB over plain Postgres
  - [ ] time-bucketed aggregation
  - [ ] retention policies
  - [ ] compression
- [ ] Can explain duplicate-check prevention
  - [ ] Redis locks
  - [ ] idempotency keys
- [ ] Can explain scaling to 10k monitors
  - [ ] sharded beat scheduler
  - [ ] worker pools per region
- [ ] Can explain Celery vs ARQ vs asyncio-only tradeoffs
- [ ] Can explain WebSocket fan-out
  - [ ] Redis Pub/Sub
  - [ ] sticky sessions
  - [ ] reconnect strategy
- [ ] Can explain lessons from Upptime, OpenStatus, StatusWise

---

## ⚠️ Traps to Avoid

- [ ] Don't build auth from scratch — use OAuth + JWT lib
- [ ] Don't over-engineer microservices — monolith + workers
- [ ] Don't skip the public status page — it's the best demo surface
- [ ] Don't chase 100% test coverage — cover checker, alerts, auth
- [ ] Don't build the AI feature first — it's garnish, not the meal

---

## 🏁 Final Checklist

- [ ] Live production URL exists
- [ ] Demo video recorded and linked in README
- [ ] Blog post published
- [ ] GitHub repo is public and polished
- [ ] At least one real user (friend, colleague) signed up
- [ ] You can give a 5-minute walkthrough without notes
