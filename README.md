# LeetPulse 🚀

**AI-powered LeetCode analytics and coaching — built for serious interview prep.**

LeetPulse analyzes your LeetCode performance and uses LLMs to generate personalized insights, identify weak spots, and build you a targeted study plan.

🔗 **Live Demo:** [leetpulse-pearl.vercel.app](https://leetpulse-pearl.vercel.app/)

---

## Features

- 📊 **Performance Analytics** — Breakdown by difficulty (Easy / Medium / Hard) and topic tag
- 🤖 **LLM-Powered Coaching** — AI-generated strengths, weaknesses, and study plans
- 📈 **Interactive Dashboards** — Visual progress tracking with dynamic charts
- ⚡ **Always-On Backend** — Reliable uptime during live interview demos

---

## Tech Stack

| Layer     | Technologies                          |
|-----------|---------------------------------------|
| Frontend  | Next.js (TypeScript), Tailwind CSS, Recharts |
| Backend   | FastAPI, Python                       |
| AI/LLM    | LLM-based insight & study plan generation |

---

## Repo Structure

leetpulse/
├── frontend/       # Next.js app (TypeScript + Tailwind)
└── backend/        # FastAPI server + LLM pipeline

---

## Getting Started

### Prerequisites
- Node.js 18+
- Python 3.10+

### Frontend
```bash
cd frontend
npm install
npm run dev
```

### Backend
```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
```

---

## Roadmap

- [ ] Goal-aware insights (e.g. Meta / Google interview tracks)
- [ ] Personalized problem recommendations
- [ ] Prompt fine-tuning for better coaching accuracy
- [ ] User auth + progress persistence

---

## Author

Built by [Amaan](https://www.amaanelahi.com/) · NYU Tandon '26

