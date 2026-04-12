# CV Screening Agent — n8n AI Workflow

> Automated CV screening and candidate ranking using AI. Reads resumes, scores candidates against job requirements, and notifies HR managers — without any manual work.

[![n8n](https://img.shields.io/badge/n8n-v2.6.3-orange)](https://n8n.io)
[![AI](https://img.shields.io/badge/AI-GPT--4o-blue)](https://openai.com)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/status-production--ready-brightgreen)]()

---

## 🎯 What it does

The CV Screening Agent is a 3-workflow automation system that replaces manual resume review. It accepts CVs via web form or API, extracts candidate data, scores against configurable job requirements, and delivers a ranked shortlist to the hiring manager — all in under 60 seconds per candidate.

**Result: 10× faster shortlisting. Zero manual screening.**

---

## ✨ Key Features

- **Multi-format support** — accepts PDF and DOCX resumes
- **Web form intake** — candidates submit directly via browser (no email needed)
- **AI-powered analysis** — extracts skills, experience, education, and red/green flags
- **Configurable scoring** — job requirements and thresholds stored in Google Sheets
- **IF-branching** — automatically separates qualified vs. disqualified candidates
- **Dual notifications** — qualified → Telegram alert to hiring manager; disqualified → auto-response
- **Comparative report** — side-by-side ranking of all candidates for a position
- **Error handling** — dedicated Error Handler workflow with Telegram SOS alerts
- **Google Sheets logging** — full audit trail of all screening decisions

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                  WORKFLOW 1: MAIN AGENT                  │
│                                                         │
│  Web Form → Extract Text → Fetch Job Requirements       │
│       ↓                                                 │
│  LLM Analysis (GPT-4o) → JSON Parser → Score IF        │
│       ↓                    ↓                            │
│  QUALIFIED:           DISQUALIFIED:                     │
│  Google Sheets +      Google Sheets +                   │
│  Telegram Alert       Auto-reply                        │
└─────────────────────────────────────────────────────────┘

┌──────────────────────────────────────┐
│        WORKFLOW 2: ERROR HANDLER      │
│                                      │
│  Error Trigger → Telegram SOS Alert  │
└──────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│          WORKFLOW 3: COMPARATIVE REPORT                   │
│                                                          │
│  Manual Trigger → Read Google Sheets → LLM Ranking      │
│       ↓                                                  │
│  Format Report → Send to Hiring Manager (Telegram)       │
└──────────────────────────────────────────────────────────┘
```

---

## 🔧 Tech Stack

| Tool | Purpose |
|------|---------|
| **n8n** v2.6.3 | Workflow automation engine |
| **GPT-4o** (OpenAI) | CV analysis and scoring |
| **Google Sheets** | Job requirements config + results storage |
| **Telegram Bot** | HR notifications + SOS alerts |
| **n8n Web Form** | Candidate intake (PDF/DOCX upload) |

---

## 📁 Repository Structure

```
cv-screening-agent/
├── workflows/
│   ├── cv-screening-agent-main.json         # Main 11-node workflow
│   ├── cv-screening-error-handler.json      # Error Handler workflow
│   └── cv-screening-comparative-report.json # Comparative Report workflow
├── docs/
│   ├── setup-guide.md                       # Step-by-step installation
│   └── google-sheets-template.md            # Sheets structure
├── screenshots/
│   ├── main-workflow.png
│   ├── web-form.png
│   └── telegram-notification.png
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites

- n8n v2.6.3+ (self-hosted or cloud)
- OpenAI API key (GPT-4o access)
- Google Workspace account (Sheets)
- Telegram Bot (via @BotFather)

### Installation

**1. Import workflows**

In n8n, go to **Workflows → Import from File** and import all 3 JSON files from the `/workflows` folder.

**2. Set up Google Sheets**

Create a spreadsheet with two tabs:

*Tab 1: `job_requirements`*
| job_id | title | required_skills | experience_years | threshold_score |
|--------|-------|----------------|-----------------|-----------------|
| JOB001 | Python Developer | Python, FastAPI, Docker | 3 | 70 |

*Tab 2: `screening_results`*
| timestamp | candidate_name | job_id | score | recommendation | flags | reasoning |
|-----------|---------------|--------|-------|---------------|-------|-----------|

**3. Configure credentials**

In n8n Settings → Credentials, create:
- `OpenAI API` — your OpenAI API key
- `Google Sheets OAuth2` — connect your Google account
- `Telegram API` — your bot token

**4. Update node settings**

In the main workflow, update:
- Google Sheets node → your Spreadsheet ID
- Telegram node → your Chat ID
- Error Handler workflow → set as Error Workflow in main workflow Settings

**5. Activate all workflows**

Toggle all 3 workflows to **Active** in n8n.

**6. Test**

Submit a test CV via the web form URL generated by n8n. Check Google Sheets and Telegram for results.

---

## 💡 How the Scoring Works

The LLM receives:
- Full CV text
- Job title and required skills
- Minimum years of experience
- Threshold score (configurable per job)

It returns a structured JSON:
```json
{
  "candidate_name": "John Smith",
  "score": 78,
  "recommendation": "QUALIFIED",
  "green_flags": ["5 years Python", "FastAPI production experience"],
  "red_flags": ["No Docker mentioned"],
  "reasoning": "Strong Python background exceeds requirements..."
}
```

Candidates scoring above the threshold go to the **qualified branch** (Sheets + Telegram alert). Below threshold → **disqualified branch** (Sheets + auto-reply).

---

## 📊 Results

- ⏱️ **60 seconds** per CV (vs. 10–15 min manual)
- 📉 **10× faster** shortlisting
- 🎯 **Consistent scoring** — no bias, no fatigue
- 📋 **Full audit trail** in Google Sheets
- 🔔 **Instant notification** when a strong candidate appears

---

## 🔒 Privacy & Security

- No CV data is stored in n8n — only metadata goes to Google Sheets
- API credentials stored in n8n's encrypted credential store
- CV files are processed in-memory and not persisted
- Before deploying to production, review your data retention policies

---

## 🛠️ Customization

**Change the scoring model:** Replace the OpenAI node with Anthropic Claude, Google Gemini, or any other LLM supported by n8n.

**Add email notifications:** Replace or supplement Telegram with Gmail/SMTP node.

**Multiple job positions:** The Google Sheets approach supports unlimited positions — just add rows to `job_requirements`.

**Adjust thresholds:** Change the `threshold_score` column per position without touching the workflow.

---

## 📄 License

MIT License — free to use, modify, and deploy for commercial purposes.

---

## 👤 Author

**Yevhenii Nohin** — AI Automation Specialist

- 🌐 [neolithai.netlify.app](https://neolithai.netlify.app)
- 💼 [Upwork Profile](https://www.upwork.com/freelancers/~013cd102dcb30a209f)
- 🐙 [GitHub](https://github.com/Evgeniy1970-ai)

*Need a custom automation? Let's talk.*

---

## 🔗 Related Projects

- [AI Lead Gen Production Workflow](https://github.com/Evgeniy1970-ai/AI-Lead-Gen-Production-Full-Workflow_production) — Autonomous B2B outreach pipeline
