# My Financial Coach

A gamified financial-literacy web app that turns the **MAS Basic Financial Planning Guide** into a strict, sequential accountability journey for young Singaporeans (aged 19–29) entering the workforce.

**Live site:** https://6alphaminds.github.io/MyFinancialCoach/
**Team:** Alpha Minds · Republic Polytechnic C240 (AI in Action) Final Assessment 2026

---

## Problem

Financial literacy is an essential life skill, yet young adults often enter the workforce with knowledge that stays purely theoretical — vulnerable to lifestyle inflation, debt, and years of avoidable financial struggle. Foundational money-management lessons are easily filed away and forgotten once graduates leave the classroom.

**My Financial Coach** bridges that gap. It translates complex MAS guidelines into a strict, sequential accountability system that ensures users build a bulletproof emergency fund and secure baseline insurance *before* attempting riskier investments — turning classroom theory into real-world financial discipline.

## Target Audience

Fresh entrants to the workforce (aged 19–29) who need a guided, step-by-step approach to early-career financial planning.

---

## What it does

The app gamifies the MAS guide into a **four-level journey**, where each level must be fully completed before the next unlocks:

1. **Level 1 — Emergency Fund & Money Management** (build a 3-month safety net, learn core money habits)
2. **Level 2 — Insurance Protection** (Death & TPD, Critical Illness, MediShield Life)
3. **Level 3 — Investing** (investment types, the Rule of 72, setting up a fund)
4. **Level 4 — Home & Retirement** (CPF planning, HDB budgeting, retirement top-ups)

Each level contains a mix of task types:

1. **Attest items** — the user marks them done themselves (e.g. "Open a high-yield savings account").
2. **Auto items** — completed automatically from the user's numbers (e.g. the 3-month emergency-fund milestone completes when savings reach the target).
3. **Learn items** — the user opens the in-app coach to learn a concept, then marks it learned.

A level only unlocks the next once **every** item in it is complete.

---

## Architecture

The founding rule of the build: **the language model never performs arithmetic.** All MAS calculations run deterministically in JavaScript; the AI coach only explains concepts and answers general financial questions. This keeps every financial figure verifiable and consistent across the app, the coach, and the Telegram loop.

1. **Website** — branded, responsive single-page app hosted on **GitHub Pages** (`index.html` landing + `app.html` workspace).
2. **`app.js`** — the deterministic engine: MAS calculations, the level/gatekeeping state machine, the plan generator, `localStorage` persistence (`mfc_state_v1`), and the event-fire that notifies the accountability loop on each completion.
3. **Botpress** — a RAG-grounded coach (29 curated MoneySense / MAS / HDB sources) embedded in the app, answering general financial questions only.
4. **n8n + Telegram + Google Sheets** — an event-driven accountability loop across three workflows (see below).

### Accountability loop (n8n)

1. **Telegram Inbound** — captures the user's chat ID via a one-time `/start`.
2. **Coach Events** — receives completion events from the app and sends per-item encouragement and per-level congratulations.
3. **Weekly Streak & Nudge** — a scheduled cron that maintains a consecutive-week streak and re-engages inactive users.

The Telegram bot token stays server-side in n8n; the app only holds the user's chat ID and webhook URL, both entered as an optional opt-in.

---

## Tech stack

1. **Frontend:** Vanilla HTML, CSS, JavaScript (no framework)
2. **Hosting:** GitHub Pages
3. **AI coach:** Botpress (RAG)
4. **Automation:** n8n (webhook + scheduled workflows)
5. **Data store:** Google Sheets (CoachStore)
6. **Messaging:** Telegram Bot API (via n8n)
7. **Dev environment:** VS Code with Claude Code

> **Note on tool changes from the original proposal:** the proposal specified the Google Gemini API for the conversational coach and Chart.js for visualisations. During the build these were revised — **Botpress** now powers the RAG coach (scoped to general Q&A), and the personalised guidance is delivered by the deterministic Action Plan rather than an LLM. These changes are documented in the final report's change log per the FA Specification's change-management requirements.

---

## MAS benchmarks (source of truth)

All financial targets are grounded strictly in the **MAS Basic Financial Planning Guide**:

1. **Emergency fund:** 3 months of expenses (minimum safety net; 3–6 months per MAS guidance)
2. **Death & TPD cover:** 9× annual income
3. **Critical Illness cover:** 4× annual income
4. **Insurance premiums:** at most 15% of take-home pay
5. **Investing:** at least 10% of take-home pay, after the emergency fund and insurance are in place

---

## Running locally

The site is fully static — no build step required.

1. Clone the repository:
   ```
   git clone https://github.com/tanboonmeng/MyFinancialCoach.git
   ```
2. Open `index.html` in a browser, or serve the folder with any static server (e.g. VS Code Live Server).
3. The app persists your progress in `localStorage`; use **Reset my data** in the workspace to clear it.

### Optional: connect Telegram accountability

1. Message the bot `/start` to receive your personal chat ID.
2. In the app's **Connect Telegram** settings, paste your chat ID and the n8n Coach Events webhook URL.
3. Complete a checklist item — you'll receive an encouragement message in Telegram.

---

## Ethical design

The system does **not** provide regulated financial advice. It guides users strictly within published MAS benchmarks, acting as an **accountability coach, not a financial advisor**. The bot celebrates behaviour ("you completed this step, keep going") and never recommends specific financial products.

---

## Team — Alpha Minds

1. **Tan Boon Meng** (Captain) — architecture, integration, contracts
2. **Ryan Lee** — `app.js`: MAS calculations, gatekeeping, the plan engine
3. **Ezann** — onboarding UI, the action-plan panel, QA
4. **Rainie** — n8n Telegram accountability loop
5. **Sammi** — Botpress RAG coach, prompt engineering, knowledge base

---

*Built for Republic Polytechnic C240 — AI in Action, Final Assessment 2026.*
