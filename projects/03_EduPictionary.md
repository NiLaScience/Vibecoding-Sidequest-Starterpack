# 03_EduPictionary – Real-Time Pictionary-Style Learning Game  
_Product-Requirements Document (PRD)_

> The **third project** is the first truly “hard” build in the course.  
> It introduces real-time collaboration, WebSockets, CRDT-style state sync, and game mechanics.  
> Learners will **prototype UI in V0 or Replit**, then move to **Cursor** for the full-stack build.

---

## 1. Vision & Overview
**EduPictionary** is a browser-based, multiplayer drawing & guessing game that reinforces academic vocabulary.  
Players join a session, one player draws an educational term (e.g. “mitosis”), other players guess in real-time, and points are awarded for speed and accuracy.

Why this project?
1. **Real-time complexity** – live drawing sync, chat, presence, timers.  
2. **Multiple external services** – auth, database, real-time signalling, optional moderation.  
3. **Fun & sticky** – immediate feedback loop keeps learners motivated.

---

## 2. Personas
| Persona | Goals | Pain Points |
|---------|-------|-------------|
| **Student Player** | Play a fun game while learning subject terms | Classroom games are clunky, latency kills fun |
| **Teacher Host** | Set up a custom word list, monitor chat, keep score | Hard to track points, prevent inappropriate words |
| **Course Learner (developer)** | Master WebSockets & state sync | Overwhelmed by infra choices, race conditions |

---

## 3. Core User Stories
### 3.1 Player
1. Join a game via invite URL and see who else is online (presence).  
2. Draw on a shared whiteboard when it’s my turn (mouse / touch).  
3. Submit unlimited text guesses; see instantly if I’m correct.  
4. View live scoreboard and round timer.  

### 3.2 Host / Teacher
1. Create a session with subject + grade-level word list.  
2. Kick or mute disruptive players.  
3. End game and export scores (CSV).  

---

## 4. MVP Features & Cut-Lines
| Feature | MVP? | Notes |
|---------|------|-------|
| Lobby / session creation & invite link | ✅ | UUID room code |
| Supabase email-link auth (or anonymous w/ nickname) | ✅ | Low-friction |
| Real-time presence list | ✅ | Supabase → `realtime.presence()` |
| Shared whiteboard | ✅ | Broadcast strokes; see **§5 Architecture** |
| Guess input & server-side answer check (fuzzy match) | ✅ | Levenshtein ≤ 2 |
| Scoring & leaderboard | ✅ | +5 for first correct, −1 per wrong |
| Round timer & next-drawer rotation | ✅ | 60 s default |
| Chat channel | ✅ | Re-use Socket / Supabase channel |
| Content moderation (bad words) | ❌ stretch | e.g. Perspective API |
| Voice chat | ❌ | Out-of-scope |

---

## 5. Technical Decisions & Real-Time Architecture
| Area | Preferred Choice | Alternatives / Notes |
|------|------------------|----------------------|
| **Stack** | Next.js (App Router) + TypeScript + Tailwind | Matches previous projects |
| **Realtime** | **Supabase Realtime** (Postgres → WebSocket) for presence & events **plus** `sharedb`-style CRDT JSON for strokes | Socket.io server, Liveblocks, Ably |
| **Whiteboard** | `fabric.js` canvas → emit path objects (`[{x,y}...]`) | `p5.js`, `konva` |
| **DB** | Supabase Postgres (rooms, users, scores, strokes snapshot) | Redis for transient strokes (stretch) |
| **Auth** | Supabase email magic-link + anonymous fallback | Google SSO later |
| **Hosting** | Vercel (frontend) / Supabase cloud | Fly.io for low-latency edge |
| **Moderation** | Simple regex in MVP; optional Perspective API | — |
| **Testing** | Vitest + Cypress component & E2E | — |

> **Why Supabase?** Learners already used it in Project 2; its channels + row-level security let them avoid running a custom WebSocket server.

---

### 5.1 Realtime Flow (high level)

```
Player ↔ Browser   
          ║  draw/guess
          ▼ WebSocket (Supabase channel)
      Supabase Edge —> Postgres “events” table (JSONB)
          ▲                       │Triggers leaderboard update
          ║                       ▼
 Other Browsers <— Broadcast —— Row & presence changes
```

---

## 6. Data Model (simplified)

```mermaid
erDiagram
  users ||--o{ sessions_users : plays
  sessions ||--o{ rounds : has
  rounds ||--o{ strokes : draw
  rounds ||--o{ guesses : submit

  users {
    uuid id PK
    text nickname
    text email
  }

  sessions {
    uuid id PK
    text subject
    int grade_level
    timestamptz created_at
    text status -- 'lobby'|'active'|'finished'
  }

  sessions_users {
    uuid session_id FK
    uuid user_id FK
    int score
    bool is_host
  }

  rounds {
    uuid id PK
    uuid session_id FK
    uuid drawer_id
    text word
    int round_number
    timestamptz started_at
    timestamptz ended_at
  }

  strokes {
    uuid id PK
    uuid round_id FK
    jsonb path   -- [{x,y,timestamp}, …]
    text color
    int width
  }

  guesses {
    uuid id PK
    uuid round_id FK
    uuid user_id
    text guess
    bool is_correct
    timestamptz created_at
  }
```

---

## 7. Tests-to-Pass (Acceptance Criteria)
| ID | Test | How to Verify |
|----|------|---------------|
| **E-01** | Two browsers see each other in presence list within < 1 s. | Cypress multi-tab + assert DOM. |
| **E-02** | Drawer stroke appears on other client in < 100 ms (local). | Simulated pointer events. |
| **E-03** | Correct guess awards points & ends round. | API test: POST `/api/guess` → round status `ended`. |
| **E-04** | Host kick removes player from session in real-time. | Socket disconnect, DOM update. |

---

## 8. AI-First Workflow Guide (Learner Steps)

1. **Prototype UI**  
   • In V0 or Replit: “Generate a whiteboard + chat UI for EduPictionary …”  
   • Export / download repo zip.  

2. **Move to Cursor**  
   • `pnpm i`, set up Supabase keys (`.env.local`).  
   • Use Cursor AI agent prompts:  
     - “Add supabase-js & implement presence in `/components/Presence.tsx`.”  
     - “Sync Fabric.js strokes through Supabase channel named after room code.”  

3. **Iterate**  
   • Implement endpoints `/api/guess`, `/api/next-round`.  
   • Add Levenshtein fuzzy check (`fastest-levenshtein`).  
   • Persist scores in `sessions_users`.  

4. **Deploy & Test**  
   • Vercel → link GitHub repo.  
   • Add Supabase URL/anon-key to Vercel env vars.  
   • Open two tabs, play a round, record 60-second GIF for proof.  

5. **Document Learning**  
   • Map covered items from *learning_goals.md*.  
   • Write short blog / video explaining real-time architecture.  

---

## 9. Focused Learning-Goal Subset  
(from `learning_goals.md`)

| Chapter | Items Exercised |
|---------|-----------------|
| **2** – Workflow & Tooling | GitHub branches/PRs, IDEs (V0/Replit, Cursor), AI agent refactors, ESLint/Prettier |
| **3** – Foundations | CRDT (optional), Real-time presence, Peer-to-peer sync concepts |
| **4** – Next.js Framework | App Router, API routes |
| **5** – Front-End | React canvas, Tailwind, Forms, Router, Media streaming (canvas) |
| **6** – Back-End | Node.js, REST, WebSockets, Webhooks (game events), Rate limiting |
| **7** – Data | Postgres schema, Relationships, Supabase Realtime, Redis (stretch) |
| **8** – Auth & Security | Supabase auth, RBAC policies, Route protection |
| **9** – Infra & Deployment | Vercel hosting, Supabase cloud, Docker (optional local), Monitoring/Tracing basics |
| **10** – Integrations | Third-party bad-word API (stretch) |
| **11** – Specialized | Real-time systems, optional vector search for drawing recognition (future) |

---

## 10. Open Questions
1. Use Canvas stroke diffs or CRDT (Yjs) for drawings?  
2. How to minimise bandwidth on mobile (throttle stroke rate)?  
3. Should we store every stroke (audit) or only final PNG snapshot?  
4. Content moderation strategy for drawings & chat?  

---

## 11. Out-of-Scope for MVP
- Voice/video chat  
- AI auto-drawing or recognition  
- Native mobile app  
- Tournament/league mode  

---

### 🔑  _EduPictionary_ pushes learners into the deep end of **real-time multiplayer web apps**, cementing skills in WebSockets, presence, game state, and rapid AI-assisted development.