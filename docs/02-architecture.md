# Codify v2 — Architecture (MVP)

> *This describes the minimum set of pieces required for the v0.1 sync flow.  Everything here should fit on one laptop and a cheap PaaS instance.*

---

## 1. Runtime components

```mermaid
flowchart LR
    subgraph Client
        A[Web UI]
    end
    subgraph API
        B[NestJS App]
        B -->|spawns| R(Sandbox Runner)
        B -->|calls| T[Tutor Module]
    end
    subgraph Data
        D[(PostgreSQL)]
    end

    A <-->|REST + SSE| B
    B <-->|SQL| D
    T -->|OpenAI API| O[(LLM)]
```

* **Web UI** – React app with problem, editor, test, and chat panes.
* **API** – single NestJS service; owns auth, CRUD, runner orchestration, tutor calls.
* **Sandbox Runner** – child process (or Docker side‑car) running `vm2` + Jest test harness.
* **Tutor Module** – pure TS class; builds prompt, hits OpenAI, returns hint text.
* **PostgreSQL** – single DB holding users, problems, submissions, messages, stats.

---

## 2. Sequence – happy‑path solve

```mermaid
sequenceDiagram
    participant UI as Client UI
    participant API as API Service
    participant RUN as Sandbox Runner
    participant DB as Postgres
    participant LLM as Tutor (OpenAI)

    UI->>API: POST /submissions { code }
    API->>DB: INSERT Submission (PENDING)
    API->>RUN: execute tests
    RUN-->>API: verdict JSON
    API->>DB: UPDATE Submission (status, runtime)
    API->>LLM: prompt(code, verdict, skillStats)
    LLM-->>API: hintText
    API->>DB: INSERT Message(role=assistant, text)
    API-->>UI: { verdict, hintText }
    UI->>UI: render tests + chat
```

*Timeouts*: Runner has 2 s wall‑clock; Tutor call budget 1 s.

---

## 3. Data model (ERD)

```mermaid
erDiagram
  USER ||--o{ SUBMISSION : has
  USER {
    int id PK
    string email
    string provider
    datetime created_at
  }

  PROBLEM ||--o{ SUBMISSION : sourced
  PROBLEM {
    int id PK
    string title
    string difficulty
    text prompt_md
    json tests_json
    datetime created_at
  }

  SUBMISSION ||--o| MESSAGE : generates
  SUBMISSION {
    int id PK
    int user_id FK
    int problem_id FK
    text code
    string status
    int runtime_ms
    datetime created_at
  }

  MESSAGE {
    int id PK
    int submission_id FK
    int user_id FK
    string role
    text text
    datetime created_at
  }

  USER ||--o{ SKILL_STAT : tracks
  SKILL_STAT {
    int user_id FK
    string tag
    int solved
    int attempts
    int hints_used
    datetime updated_at
    PK "user_id, tag"
  }
```

* `tests_json` holds the reference test cases; runner parses it into Jest tests.
* `MESSAGE.role` is `user` or `assistant`; chat history per problem is all messages sharing a `problem_id` (via `SUBMISSION`).
* `SKILL_STAT` is optional for v0.1; updated after each submission for adaptive curriculum later.

---

### Stretch‑goal refactor note

If latency or load becomes an issue, break the **Runner** and **Tutor** into queue‑backed worker services and replace sync HTTP with job IDs + SSE polling.  The surface API stays identical.
