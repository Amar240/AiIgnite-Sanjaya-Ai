## Sanjaya AI – Explainable Career Roadmaps

Sanjaya AI helps students see how their courses add up to a real job (for example, **AI Engineer**) while keeping university rules (prerequisites, credits, degree totals) deterministic. Advisors and admins get visibility into validation issues, role requests, and role readiness before publishing updates.

Repo: `https://github.com/Amar240/AiIgnite-Sanjaya-Ai.git`

---

## 90‑second judge demo (recommended)

1. **Start the app** (local or Docker) using Quick start below.  
2. **Generate an AI Engineer plan**  
   - Open `http://127.0.0.1:3000/` → click **Build my roadmap** → create an **Undergraduate, Core, AI Engineer** plan with **degree total = 128**.  
3. **Run a job posting check**  
   - In **Your path in 5 steps → Reality Check (Job Posting)** click **Load Preset 1** → **Extract & Match**.  
4. **Ask the advisor + open admin views**  
   - In **Advisor Q&A**, ask: “Why did you recommend this role?”  
   - Open `http://127.0.0.1:3000/admin/insights` and `http://127.0.0.1:3000/admin/role-requests`.

---

## Why not just ChatGPT?

- **Chatbots can explain, but can’t reliably verify** prerequisites, credit rules, or catalog constraints.  
- **Sanjaya outputs structured plans with validation codes + citations** so the output is auditable.  
- **Sanjaya includes governance:** advisors can curate roles and publish only when readiness gates pass.

---

## Quick start (choose one)

### Option A — Run locally (Python + Node)

#### Backend (FastAPI)

**Windows (PowerShell)**

```powershell
cd backend
python -m pip install -r requirements.txt
copy .env.example .env
uvicorn app.main:app --reload --port 8000
```

**macOS / Linux**

```bash
cd backend
python3 -m pip install -r requirements.txt
cp .env.example .env
uvicorn app.main:app --reload --port 8000
```

Check:

- Health: `http://127.0.0.1:8000/health`  
- API docs: `http://127.0.0.1:8000/docs`

#### Frontend (Next.js)

Open a second terminal:

**Windows (PowerShell)**

```powershell
cd frontend
copy .env.local.example .env.local
npm install
npm run dev
```

**macOS / Linux**

```bash
cd frontend
cp .env.local.example .env.local
npm install
npm run dev
```

Open:

- App: `http://127.0.0.1:3000/`

### Option B — Run with Docker Compose

From repo root:

```bash
docker compose up --build
```

Open:

- App: `http://localhost:3000`  
- Backend health: `http://localhost:8000/health`

> LLM features are optional. With the default example env files, the planner + validation + roadmap work without API keys.

---

## What is deterministic vs what uses LLM?

### Deterministic (no LLM)

- Planner: semester scheduling, prereq order, credit limits, degree total checks  
- Verifier: structured `PlanError` codes (e.g., `PREREQ_ORDER`, `CREDIT_OVER_MAX`)  
- Evidence linking and citation IDs  
- Admin governance: publish gates + readiness checks

### RAG only (retrieval, not decision)

- Role retrieval from curated market data  
- Evidence retrieval for role/skill claims  
- Course‑purpose linking

### LLM optional (text tasks only)

- Storyboard: rewrite‑only narrative from a structured plan  
- Job text extraction: strict JSON extraction from pasted job postings (with deterministic fallback)

**Important:** LLM never decides courses or feasibility. No job guarantees.

---

## Demo path (2–3 minutes): AI Engineer

1. On `/`, generate an AI Engineer plan (**UG → Core → degree total = 128**).  
2. In **Your path in 5 steps**:
   - Target Reality → tasks/salary (if available)  
   - Skill Gaps → covered/missing + projects  
   - Career Storyboard → narrative summary  
   - Reality Check (Job Posting) → Load Preset 1 → Extract & Match  
3. In **Advisor Q&A**, ask: “Why did you recommend this role?”  
4. Admin views:
   - `/admin/insights`  
   - `/admin/role-requests`  
   - `/admin/drafts/[draftId]` (readiness gates + publish)

---

## Data used (under `data/processed/`)

| File | Purpose |
| --- | --- |
| `courses.json` | course catalog: credits, prerequisites, terms |
| `course_skills*.json` | course → skill mappings (curated + fallback) |
| `roles_market*.json` | roles + required skills + evidence source IDs |
| `skills_market.json` | canonical skill taxonomy |
| `role_skill_evidence.json` + `market_sources.json` | evidence snippets + source metadata |
| `role_reality_usa.json` | tasks + salary bands (USA posture) |
| `project_templates.json` | project templates for missing skills |

---

## Admin auth

Admin pages require an admin token:

- Frontend `frontend/.env.local`: `SANJAYA_ADMIN_TOKEN`  
- Backend `backend/.env`: `SANJAYA_ADMIN_TOKEN`  

Default in Docker: `dev-admin-token`.

Admin routes:

- `/admin/insights`  
- `/admin/role-requests`  
- `/admin/drafts/[draftId]`

---

## Tests (backend)

```bash
cd backend
pytest
```

---

## Troubleshooting (common issues)

- **Ports in use (8000/3000)**: stop existing processes or change ports.  
- **Frontend can’t reach backend**: check `NEXT_PUBLIC_API_BASE_URL` in `frontend/.env.local`, restart frontend.  
- **Admin 401/403**: set matching `SANJAYA_ADMIN_TOKEN` in backend + frontend, restart.  
- **Backend startup data error**: ensure `data/processed/*.json` exists; check `/health` and backend logs.  
- **Docker permissions**: ensure repo `data/` is writable and rerun `docker compose up --build`.  