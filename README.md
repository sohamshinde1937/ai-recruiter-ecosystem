# AI Recruitment Ecosystem

> Predictive, automated candidate-to-job matching with full hiring-pipeline orchestration  wrapped in a single dark, console-style interface.

![status](https://img.shields.io/badge/status-MVP-7DD3C0?style=flat-square) ![backend](https://img.shields.io/badge/backend-FastAPI%20%2B%20PostgreSQL-13161B?style=flat-square&labelColor=2A2F36) ![frontend](https://img.shields.io/badge/frontend-vanilla%20JS-13161B?style=flat-square&labelColor=2A2F36)

---

## Overview

This project pairs a weighted ranking engine with a single-page recruiter console. Paste a resume against a job description for an instant semantic score, or go further: post jobs, add candidates, rank everyone automatically, and drag applications through the hiring pipeline all from one screen.

| | |
|---|---|
| 🎯 **Match Engine** | Semantic JD ↔ resume similarity, scored 0–100 |
| 🧠 **Predictive Ranking** | 50% semantic similarity + 35% skill coverage + 15% experience fit |
| 🪄 **Auto-extraction** | Skills & years of experience parsed from raw text no manual tagging |
| 🚦 **Pipeline Automation** | Strong matches (≥75) are auto-advanced to *screened* |
| 🗂️ **Kanban Pipeline** | `applied → screened → shortlisted → interview → offer → hired` (+ `rejected`) |

---

## Tech Stack

- **Backend** — FastAPI, SQLAlchemy, PostgreSQL, sentence-transformer embeddings
- **Frontend** — Single-file HTML/CSS/JS, no build step, no framework
- **Design language** — Fraunces (serif display) + IBM Plex Sans/Mono, dark console theme with teal/amber/coral score tiers

---

## Quick Start

### 1. Backend

```bash
cd Backend
pip install -r requirements.txt --break-system-packages

# start postgres, then:
createdb recruiter_db
export DATABASE_URL="postgresql://<user>:<password>@localhost:5432/recruiter_db"

uvicorn main:app --reload
```

The API will be live at `http://127.0.0.1:8000` (docs at `/docs`). Tables are created automatically on first run.

### 2. Frontend

No build step — just open the file:

```bash
open index.html
```

The app talks to `http://127.0.0.1:8000` (CORS is open to `*`). Make sure the backend is running first.

---

## Using the App

The console has five tabs, all sharing one visual identity:

### 🔍 Match
The original pairwise tool. Paste a job description and a resume — get back a live-animated score gauge, a recommendation tier (*Highly Recommended / Potential Match / Low Match*), and a signal-bar visualization.

### 💼 Jobs
Create job postings with just a title and description. Required skills and minimum experience are **auto-extracted** server-side if you don't supply them — both are shown (and editable) on the job detail panel.

### 👤 Candidates
Add candidates with raw resume text. Skills and years of experience are auto-parsed the same way.

### 🏆 Ranking — *the centerpiece*
Pick a job, hit **Rank Candidates**, and every candidate is scored against it. Each row shows:

- An overall score gauge
- The semantic / skill / experience breakdown
- Matched skills (teal) vs. missing skills (coral)
- A plain-language recommendation
- Current pipeline status — with an **Auto-screened** badge if the ranking engine advanced them automatically

Re-running ranking is safe it updates existing scores rather than duplicating applications.

### 📋 Pipeline
A kanban board across all seven stages. Move any candidate forward (or to *rejected*) with a dropdown — changes are saved instantly via the API.

---

## Scoring Formula

```
overall_score = 0.50 × semantic_similarity
              + 0.35 × skill_coverage
              + 0.15 × experience_fit
```

| Score | Tier |
|---|---|
| ≥ 75 | 🟢 Highly Recommended — *auto-advances to "screened"* |
| 50–74 | 🟡 Potential Match |
| < 50 | 🔴 Low Match |

Weights live in `Backend/ml_engine.py` and can be tuned without touching the API contract.

---

## API Reference

Full endpoint documentation lives in [`Backend/README.md`](./Backend/README.md). Summary:

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/v1/match` | Legacy single JD ↔ resume match |
| `POST` / `GET` / `DELETE` | `/api/v1/jobs` | Job management |
| `POST` / `GET` | `/api/v1/candidates` | Candidate management |
| `POST` | `/api/v1/jobs/{id}/rank` | Run the ranking engine for a job |
| `GET` | `/api/v1/jobs/{id}/applications` | Ranked application list |
| `PATCH` | `/api/v1/applications/{id}/status` | Move an application through the pipeline |

---

## Design System

| Token | Value | Use |
|---|---|---|
| `--bg` | `#0B0D10` | Page background |
| `--panel` | `#13161B` | Cards / panels |
| `--signal` | `#7DD3C0` | High scores, primary actions |
| `--mid` | `#E8B85C` | Mid scores |
| `--warn` | `#FF6B4A` | Low scores, destructive actions |
| `Fraunces` | serif | Headings, scores |
| `IBM Plex Mono` | mono | Labels, badges, metadata |
| `IBM Plex Sans` | sans | Body text |

---

## Roadmap

- 📄 Resume upload (PDF/DOCX → text extraction)
- 🔔 Notifications on stage changes
- 🔐 Auth & multi-tenant support
- 📅 Interview scheduling
- 🔁 Learning loop — retune scoring weights from hiring outcomes
