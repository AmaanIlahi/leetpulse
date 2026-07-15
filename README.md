# LeetPulse

An AI-powered LeetCode analytics platform that goes beyond solved counts —
it analyzes your coding patterns, identifies company-specific gaps, and
generates a personalized 4-week study plan grounded in real company interview
data.

🔗 **[Live Demo](https://leetpulse-pearl.vercel.app/)**

---

## What it does

Enter any LeetCode username and optionally select a target company (Meta,
Google, Amazon, etc.). LeetPulse fetches your full profile across 8 data
dimensions, computes pattern-level analytics, and uses GPT-4o-mini with a
RAG pipeline grounded in real company-tagged LeetCode problems to generate
coaching insights specific to your target company's interview style.

---

## Features

- **Pattern-level skill breakdown** — not just topic counts, but advanced
  coding patterns (DP on sequences, BFS/DFS, Monotonic Stack, Union-Find etc.)
  grouped into Advanced / Intermediate / Fundamental tiers
- **Difficulty gap analysis** — Easy / Medium / Hard with attempted vs solved
  counts, not just solved
- **Company-targeted study plans** — 4-week plans tailored to Meta, Google,
  Amazon, Microsoft, Apple, Netflix, Uber, Bloomberg; grounded in 1,073 real
  company-tagged problems scraped from LeetCode Premium
- **RAG-grounded recommendations** — problem suggestions verified against a
  real company problem database (ChromaDB), eval harness scores grounding
  accuracy at 73.6% overall across all 8 companies
- **Streak & activity heatmap** — GitHub-style 52-week submission calendar
  with consistency score (0-100)
- **Contest tracking** — rating history chart, global rank, top percentile
- **Language distribution** — SVG donut chart of problems solved per language
- **Recent submissions** — last 20 accepted submissions with status and date,
  linked to LeetCode problem pages
- **Smart caching** — Upstash Redis cache with 24h TTL delivering 99% latency
  reduction on repeat requests (2.2s → 24ms measured in production), with
  "Last updated X minutes ago" display and manual refresh
- **Observability** — PostHog product analytics tracking skill level
  distribution, company targeting trends, cache hit rates, and language
  preferences; Sentry error tracking with structured JSON logging

---

## Tech Stack

**Frontend**
- Next.js (TypeScript)
- Tailwind CSS v4
- Recharts (contest rating chart)
- Custom CSS heatmap and inline SVG donut chart (no extra libraries)

**Backend**
- FastAPI + Python 3.11
- httpx with asyncio.gather (8 parallel API calls per user)
- OpenAI GPT-4o-mini (JSON mode, 2000 token responses)
- Upstash Redis (persistent cache, survives restarts)
- ChromaDB (vector store for RAG, 1,073 problems across 8 companies)
- OpenAI text-embedding-3-small (RAG embeddings, ~$0.0003 per full index)
- APScheduler (weekly eval cron)
- PostHog (product analytics)
- Sentry (error tracking)
- Structured JSON logging

**Data Pipeline**
- Playwright scraper authenticating with LeetCode Premium session cookies
- Fetches company-tagged problems from 8 company URLs
- Tag enrichment via LeetCode GraphQL API (578 cache hits on 1,073 problems)
- Weekly eval harness scoring grounding accuracy, company relevance,
  pattern coverage, and difficulty fit

**Deployment**
- Frontend: Vercel
- Backend: Fly.io (512MB RAM, persistent volume for ChromaDB)
- Cache: Upstash Redis

---

## Architecture

```
User → Next.js Frontend (Vercel)
            ↓
      FastAPI Backend (Fly.io)
       ↓              ↓
alfa-leetcode-api   OpenAI GPT-4o-mini
(8 parallel calls)  + RAG context
       ↓              ↓
  Upstash Redis    ChromaDB (1,073 problems)
  (24h TTL)        (Fly.io persistent volume)
       ↓
  PostHog + Sentry
```

---

## RAG Pipeline

```
Weekly (local):
  Playwright → LeetCode Premium (8 company URLs)
      ↓
  1,073 problems scraped (Meta: 175, Google: 194,
  Amazon: 162, Microsoft: 166, Apple: 76,
  Netflix: 29, Uber: 68, Bloomberg: 203)
      ↓
  Tag enrichment via LeetCode GraphQL
  (495 API calls, 578 cache hits)
      ↓
  POST /admin/rag/ingest → ChromaDB
  (text-embedding-3-small, ~$0.0003 total cost)
      ↓
  Eval harness: overall score 73.6%
  Google: 74.4% | Meta: 74.4%
  Microsoft: 72.5% | Amazon: 71.7%
```

---

## Repo Structure

```
leetpulse/
├── frontend/
│   └── app/
│       ├── page.tsx                    # Landing page
│       └── dashboard/
│           ├── page.tsx                # Suspense wrapper
│           └── DashboardClient.tsx     # All dashboard logic
└── backend/
    └── app/
        ├── api/
        │   └── routes.py               # API endpoints
        ├── services/
        │   ├── leetcode.py             # Parallel data fetching
        │   ├── analytics.py            # Analytics computation
        │   ├── llm.py                  # GPT-4o-mini prompt engine
        │   ├── cache.py                # Upstash Redis cache
        │   └── rag/
        │       ├── scraper.py          # LeetCode Premium scraper
        │       ├── embedder.py         # ChromaDB ingestion
        │       ├── retriever.py        # Semantic search + ranking
        │       └── scheduler.py        # Weekly eval cron
        ├── evals/
        │   ├── harness.py              # Automated eval suite
        │   ├── test_cases.json         # Fixed eval usernames
        │   └── results/                # Dated eval JSON output
        ├── models/                     # Pydantic schemas
        ├── utils/
        │   └── logger.py               # Structured JSON logger
        └── data/
            └── company_profiles.json   # Company interview profiles
    └── scrape_local.py                 # Local Playwright scraper
```

---

## Local Setup

**Backend**
```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env            # Fill in your keys
uvicorn app.main:app --reload
```

**Frontend**
```bash
cd frontend
npm install
cp .env.example .env.local      # Fill in NEXT_PUBLIC_API_BASE_URL
npm run dev
```

**RAG scraper (run locally, pushes to backend):**
```bash
pip install playwright
playwright install chromium
python scrape_local.py                                    # local backend
python scrape_local.py --url https://your-backend.fly.dev # production
```

---

## Environment Variables

**Backend `.env`**
```
OPENAI_API_KEY=your_openai_key
POSTHOG_API_KEY=your_posthog_key
SENTRY_DSN=your_sentry_dsn
UPSTASH_REDIS_URL=your_upstash_url
UPSTASH_REDIS_TOKEN=your_upstash_token
ADMIN_SECRET=your_admin_secret
LEETCODE_SESSION=your_leetcode_session_cookie
LEETCODE_CSRF_TOKEN=your_leetcode_csrf_token
CHROMA_PATH=./chroma_db
```

**Frontend `.env.local`**
```
NEXT_PUBLIC_API_BASE_URL=https://your-backend.fly.dev
```

---

## Key Metrics

| Metric | Value |
|---|---|
| LeetCode endpoints fetched in parallel | 8 |
| Cache latency reduction | 99% (2.2s → 24ms) |
| Problems in RAG database | 1,073 across 8 companies |
| RAG eval overall score | 73.6% |
| Embedding cost per full index rebuild | ~$0.0003 |
| LLM model | GPT-4o-mini |
| Cache TTL | 24 hours |
| Companies supported | 8 |

---

## Roadmap

- [ ] Improve RAG grounding score above 80% with stricter prompt constraints
- [ ] Weekly automated scraper running on CI instead of locally
- [ ] User accounts to track progress over time
- [ ] Direct LeetCode GraphQL migration (remove alfa-leetcode-api dependency)
- [ ] Mobile responsive improvements

---

## Contributing

PRs welcome. Open an issue first for major changes.
