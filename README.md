# LeetPulse

An AI-powered LeetCode analytics platform that goes beyond solved counts —
it analyzes your coding patterns, identifies company-specific gaps, and
generates a personalized 4-week study plan tailored to your target company.

🔗 **[Live Demo](https://leetpulse-pearl.vercel.app/)**

---

## What it does

Enter any LeetCode username and optionally select a target company (Meta,
Google, Amazon, etc.). LeetPulse fetches your full profile across 8 data
dimensions, computes pattern-level analytics, and uses GPT-4o-mini to
generate coaching insights specific to your target company's interview style.

---

## Features

- **Pattern-level skill breakdown** — not just topic counts, but advanced
  coding patterns (DP on sequences, BFS/DFS, Monotonic Stack, Union-Find etc.)
  grouped into Advanced / Intermediate / Fundamental tiers
- **Difficulty gap analysis** — Easy / Medium / Hard with attempted vs solved
  counts, not just solved
- **Company-targeted study plans** — 4-week plans tailored to Meta, Google,
  Amazon, Microsoft, Apple, Netflix, Uber, Bloomberg with real Blind 75 /
  NeetCode 150 problem recommendations
- **Streak & activity heatmap** — GitHub-style 52-week submission calendar
  with consistency score
- **Contest tracking** — rating history, global rank, top percentile
- **Language distribution** — donut chart of problems solved per language
- **Recent submissions** — last 20 accepted submissions with status and date
- **Smart caching** — 24h in-memory cache with 99% latency reduction
  (2.2s → 24ms on repeat requests), with "Last updated X minutes ago" display
  and manual refresh
- **Observability** — PostHog analytics tracking unique users, skill levels,
  company targeting trends, and cache efficiency

---

## Tech Stack

**Frontend**
- Next.js (TypeScript)
- Tailwind CSS v4
- Recharts (contest rating chart)
- Custom CSS heatmap and SVG donut chart (no extra libraries)

**Backend**
- FastAPI + Python 3.11
- httpx with asyncio.gather (8 parallel API calls per user)
- OpenAI GPT-4o-mini via JSON mode
- In-memory cache with 24h TTL
- PostHog (product analytics)

**Deployment**
- Frontend: Vercel
- Backend: Fly.io (always-on, no cold starts)

---

## Architecture

User → Next.js Frontend (Vercel)
↓
FastAPI Backend (Fly.io)
↓              ↓
alfa-leetcode-api   OpenAI GPT-4o-mini
(8 parallel calls)  (insights + study plan)
↓
In-memory cache (24h TTL)
↓
PostHog (usage analytics)

---

## Repo Structure

```
leetpulse/
├── frontend/
│   └── app/
│       ├── page.tsx                   # Landing page
│       └── dashboard/
│           ├── page.tsx               # Suspense wrapper
│           └── DashboardClient.tsx    # All dashboard logic
└── backend/
    └── app/
        ├── api/
        │   └── routes.py              # API endpoints
        ├── services/
        │   ├── leetcode.py            # Parallel data fetching
        │   ├── analytics.py           # Analytics computation
        │   ├── llm.py                 # GPT-4o-mini prompt engine
        │   └── cache.py               # In-memory cache
        ├── models/                    # Pydantic schemas
        └── data/
            └── company_profiles.json  # Company interview profiles
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

---

## Environment Variables

**Backend `.env`**

OPENAI_API_KEY=your_openai_key
POSTHOG_API_KEY=your_posthog_key

**Frontend `.env.local`**

NEXT_PUBLIC_API_BASE_URL=https://leetpulse-backend.fly.dev

---

## Roadmap

- [ ] RAG-based problem recommendations
- [ ] Company-specific curated problem sets
- [ ] User accounts to track progress over time
- [ ] Redis cache for multi-worker deployment
- [ ] Direct LeetCode GraphQL integration

---

## Contributing

PRs welcome. Open an issue first for major changes.

