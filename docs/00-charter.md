# Codify v2 — Product Charter

## Purpose

Codify v2 exists to help developers who feel stuck behind Copilot or ChatGPT regain authentic, test‑driven coding skill. The app turns an AI model into a coach that intervenes only after you try, guiding you with incremental, spoiler‑limited hints until you solve problems on your own.

## Target audience

Developers and engineers aspiring to improve their coding capabilities with the use of AI. Those who desire to use AI as a learning tool instead of a shortcut.

## Product concept

Users authenticate with a familiar developer identity provider (e.g., GitHub or Google), select challenges in the initial supported language, and write code in an in‑browser editor that runs safely inside a sandbox. After each genuine attempt they can request a hint; the AI offers incremental guidance—no full solutions—and future problems adapt to their performance. A lightweight dashboard surfaces solved counts and streaks to make progress visible.

## MVP scope (v0.1)

* **Auth** – single provider (GitHub or Google).
* **Problem panel** – displays challenge prompt, examples, and constraints.
* **Code panel** – in‑browser editor for the initial language (TypeScript) with run/submit controls.
* **Test panel** – shows predefined unit‑test results after each submission (pass/fail, error output).
* **Chat panel** – user asks for help; AI returns incremental hints only after a failed submission.
* **Feedback loop** – AI message + test results appear side‑by‑side so users iterate quickly.
* **Progress tracking** – store attempts, hint count, and solves for basic dashboard stats.

Anything mobile, multi‑language, peer social, or advanced analytics is post‑MVP.## Success signals

### System reliability

* **Problem render accuracy** – challenge prompt, starter code, and tests load correctly in ≥ 99 % of sessions (monitored via Sentry/analytics).
* **Submission correctness** – the verdict returned by the runner matches the ground‑truth solution in ≥ 98 % of runs.
* **Round‑trip latency** – compile + test cycle returns in under 4 seconds for 95 th percentile.
* **Editor stability** – no lost‑input or undo‑stack bugs reported in the beta; uptime ≥ 99.5 %.

### User outcomes

* 70 % of new users solve at least three problems with zero reveals within their first 14 days.
* Weekly retention ≥ 40 %.
* 80 % of hints are requested only after a failed submission.

## Timeline

* **Sprint 0** (weeks 1 – 2) – repo & CI setup, seed initial problems.
* **Sprint 1** (weeks 3 – 4) – auth flow and core panels wired end‑to‑end.
* **Sprint 2** (weeks 5 – 6) – hint engine + progress tracking, begin closed beta.
* **Public beta target** – **6 weeks** from project start.

## Constraints & assumptions

* MVP supports **one language** (TypeScript) on Node ≥ 18.
* Sandbox runs inside a container (gVisor/Firecracker) with 2‑sec CPU cap & no network.
* Single OAuth provider at launch (GitHub *or* Google).
* Dev budget ≈ 10 h/week (nights & weekends).

## Key risks & mitigations

| Risk                             | Mitigation                                                |
| -------------------------------- | --------------------------------------------------------- |
| Sandbox escape or resource abuse | Container isolation + strict CPU/memory limits.           |
| AI hints leak full solutions     | Prompt design + automated redaction check before send.    |
| Editor instability               | Stick to stable Monaco, capture client errors via Sentry. |
| Runner verdict mismatch          | Maintain golden‑set tests in CI to validate each deploy.  |


---

*Updated: 2025‑06‑21*
