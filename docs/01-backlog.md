# Codify v2 — MVP Backlog

> Scope: everything needed to deliver the v0.1 flow defined in the charter. Sprints map roughly to IDs (A‑, B‑ …).

## Epic A – Auth & Session

| ID     | Story                                                                                    | Acceptance criteria                                                                                                                                    |
| ------ | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **A1** | *As a user I can sign in and out using a single OAuth provider so my progress is saved.* | • Successful OAuth round‑trip sets secure cookie / JWT.<br>• `GET /me` returns id + email.<br>• Hitting a protected endpoint without auth returns 401. |

## Epic B – Problem Catalogue

| ID     | Story                                                                  | Acceptance criteria                                                          |
| ------ | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **B1** | *I can see a paginated list of problems with title and difficulty.*    | `/problems` API returns array; UI renders table; links work.                 |
| **B2** | *I can open a problem and read its prompt, examples, and constraints.* | `/problems/:id` returns full prompt\_md; markdown renders identically to DB. |

## Epic C – Solve & Test

| ID     | Story                                                                      | Acceptance criteria                                                               |
| ------ | -------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| **C1** | *I can write TypeScript in the editor, click Run, and see test verdicts.*  | Code sends to `POST /submissions`; runner executes; UI shows PASS/FAIL + runtime. |
| **C2** | *I can view individual test‑case results and error output when they fail.* | API returns per‑case status; panel lists each; failing cases show stderr diff.    |

## Epic D – Chat & AI Feedback

| ID     | Story                                                                                                           | Acceptance criteria                                                                                                                          |
| ------ | --------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **D1** | *After every code submission (pass **or** fail) I automatically receive an AI message calibrated to my result.* | `POST /submissions` triggers tutor service; first response appears in chat within 2 s; message contains ≤ 30 % of reference solution tokens. |
| **D2** | *I can ask follow‑up questions in the chat and get incremental, no‑spoiler guidance.*                           | `POST /chat` with prompt returns answer that references current problem context and respects the code‑reveal ceiling.                        |
| **D3** | *Chat history for the current problem stays visible while I work.*                                              | Reloading the problem restores prior AI + user messages from the DB.                                                                         |

## Epic E – Progress Tracking – Progress Tracking

| ID     | Story                                                              | Acceptance criteria                                                                    |
| ------ | ------------------------------------------------------------------ | -------------------------------------------------------------------------------------- |
| **E1** | *My solved count, streak, and hint usage update in the dashboard.* | Submitting PASS stores row; dashboard query shows correct aggregates; reload persists. |

---

*Updated: 2025‑06‑21*