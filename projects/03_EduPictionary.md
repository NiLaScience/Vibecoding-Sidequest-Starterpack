# 03_EduPictionary – Real-Time Pictionary-Style Learning Game  
_Product-Requirements Document (PRD)_

> The **third project** is the first truly "hard" build in the course.  
> It introduces real-time collaboration, WebSockets, CRDT-style state sync, and game mechanics.  
> Learners will **prototype UI in V0 or Replit**, then move to **Cursor** for the full-stack build.

---

## 1. Vision & Overview
**EduPictionary** is a browser-based, multiplayer drawing & guessing game that reinforces academic vocabulary.  
Players join a session, one player draws an educational term (e.g. "mitosis"), other players guess in real-time, and points are awarded for speed and accuracy.

Why this project?
1. **Real-time complexity** – live drawing sync, chat, presence, timers.  
2. **Multiple external services** – auth, database, real-time signalling, optional moderation.  
3. **Fun & sticky** – immediate feedback loop keeps learners motivated.

---

## 2. Personas
| Persona | Goals | Pain Points |
|---------|-------|-------------|
| **Student Player** | Play a fun game while learning subject terms | Classroom games are clunky, latency kills fun |
| **Teacher Host** | Set up a custom word list, monitor chat, keep score | Hard to track points, prevent inappropriate words, this should automated |

---

## 3. Core User Stories
### 3.1 Player
1. Join a game via invite URL and see who else is online (presence).  
2. Draw on a shared whiteboard when it's my turn (mouse / touch). Use tools to help me draw.   
3. Submit text guesses; see instantly if I'm correct.
4. View live scoreboard and round timer.

### 3.2 Host / Teacher
1. Create a session with subject + grade-level word list.  
2. Kick or mute disruptive players. Correct wrong scoring.
3. Send messages to all players in the session.
4. End game, store scores for students.  

---

## 4. MVP Features & Cut-Lines
| Feature | MVP? | Notes |
|---------|------|-------|
| Lobby / session creation & invite link | ✅ | UUID room code |
| Supabase email-link auth (or anonymous w/ nickname) | ✅ | Low-friction |
| Real-time presence list | ✅ | Supabase → `realtime.presence()` |
| Shared whiteboard | ✅ | Broadcast strokes; see **§5 Architecture** |
| Guess input & server-side answer check (fuzzy match, or LLM) | ✅ | Levenshtein ≤ 2? LLM judge? |
| Scoring & leaderboard | ✅ | Come up with a scoring system that is fair and incentivises learning. |
| Round timer & next-drawer rotation | ✅ | 90 s default |
| Chat channel | ✅ | Re-use Socket / Supabase channel |
| Content moderation (bad words) | ❌ stretch | e.g. Perspective API |
| Multimodal AI player | ❌ stretch | Use OpenAI's vision API to guess alongside students, extra points if students are faster than the AI |
| Voice chat | ❌ stretch | Only for advanced developers |

---

## 5. Technical Decisions & Real-Time Architecture

You have some experience building apps with AI now, so you should realize that these are mostly suggestions. You can choose a different tech stack, different realtime architecture, and build different features, if you can convince yourself and us that they serve the purpose of the project better than our recommended choices.

| Area | Preferred Choice | Alternatives / Notes |
|------|------------------|----------------------|
| **Stack** | Next.js (App Router) + TypeScript + Tailwind | Matches previous projects |
| **Realtime** | **Supabase Realtime** (Postgres → WebSocket) for presence & events **plus** `sharedb`-style CRDT JSON for strokes | Socket.io server, Liveblocks, Ably |
| **Whiteboard** | `fabric.js` canvas → emit path objects (`[{x,y}...]`) | `p5.js`, `konva` |
| **DB** | Supabase Postgres (rooms, users, scores, strokes snapshot) | Redis for transient strokes (stretch) |
| **Auth** | Supabase email magic-link + anonymous fallback | Google SSO later |
| **Hosting** | Vercel (frontend) / Supabase cloud | Fly.io for low-latency edge |
| **Moderation** | Simple regex in MVP or optional moderation API | — |


> **Why Supabase?** Learners already used it in Project 2; its channels + row-level security let them avoid running a custom WebSocket server.

---

### 5.1 Realtime Flow (high level)

```
Player ↔ Browser   
          ║  draw/guess
          ▼ WebSocket (Supabase channel)
      Supabase Edge —> Postgres "events" table (JSONB)
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
   • In V0 or Replit: "Generate a whiteboard + chat UI for EduPictionary …"  
   • Create UI scaffold and API routes for the MVP in v0
   • Optional: use Supabase integration in v0 to set up auth and basic DB.
   • Export / download repo zip.  

2. **Move to Cursor**  
   • `npm install`, set up Supabase keys (`.env.local`).  
   • Use Cursor AI agent prompts:  
     - "Add supabase-js & implement presence in `/components/Presence.tsx`."  
     - "Sync Fabric.js strokes through Supabase channel named after room code."  

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
   • Write short post / video explaining real-time architecture and demonstrating the app.
   • Optional but recommended: get a group of friends, family, kids, students, etc. to play the game and record a video of them playing. This proof of competence is worth 10x more than a github repo.

---

## 9. Focused Learning Goals
This project will give you hands-on experience with the following core concepts. Check them off in `goals/learning_goals.md` as you master them.

### Chapter 2: Development Workflow, Tooling & QA
- [[learning_notes/What is version control.md|What is version control?]]
- [[learning_notes/What are AI developer tools.md|What are AI developer tools?]]
- [[learning_notes/What is linting.md|What is linting?]]

### Chapter 3: Foundational Concepts & Core Technologies
- [[learning_notes/What is state management.md|What is state management?]]
- [[learning_notes/What is a CRDT (Conflict-free Replicated Data Type).md|What is a CRDT (Conflict-free Replicated Data Type)?]]
- [[learning_notes/What is real-time presence.md|What is real-time presence?]]
- [[learning_notes/What is peer-to-peer state synchronization.md|What is peer-to-peer state synchronization?]]

### Chapter 4: Full-Stack Frameworks
- [[learning_notes/What is Next.js.md|What is Next.js?]]

### Chapter 5: Front-End Development
- [[learning_notes/What is React.md|What is React?]]
- [[learning_notes/What is a router.md|What is a router?]]
- [[learning_notes/What are forms.md|What are forms?]]
- [[learning_notes/What is media streaming.md|What is media streaming?]]

### Chapter 6: Back-End Development
- [[learning_notes/What is REST.md|What is REST?]]
- [[learning_notes/What are WebSockets.md|What are WebSockets?]]
- [[learning_notes/What are real-time updates.md|What are real-time updates?]]
- [[learning_notes/What is rate limiting.md|What is rate limiting?]]

### Chapter 7: Data Storage & Management
- [[learning_notes/What is a database.md|What is a database?]]
- [[learning_notes/What is Postgres.md|What is Postgres?]]
- [[learning_notes/What is an in-memory data store.md|What is an in-memory data store?]]

### Chapter 8: Authentication & Security
- [[learning_notes/What is Auth.md|What is Auth?]]
- [[learning_notes/What is route protection.md|What is route protection?]]
- [[learning_notes/What is role-based access control (RBAC).md|What is role-based access control (RBAC)?]]

### Chapter 9: Infrastructure & Deployment
- [[learning_notes/What is hosting.md|What is hosting?]]
- [[learning_notes/What is Docker.md|What is Docker?]]
- [[learning_notes/What is monitoring (stretch).md|What is monitoring? (stretch)]]

### Chapter 10: Third-Party Integrations & Services
- [[learning_notes/What are third-party services.md|What are third-party services?]]

### Chapter 11: Specialized Development & AI
- [[learning_notes/What is an AI agent.md|What is an AI agent?]]
- [[learning_notes/What is vector search.md|What is vector search?]]

---

## 10. Open Questions
1. Use Canvas stroke diffs or CRDT (Yjs) for drawings?  
2. How to minimise bandwidth on mobile (throttle stroke rate)?  
3. Should we store every stroke (audit) or only final PNG snapshot?  
4. Content moderation strategy for drawings & chat?  

---

## 11. Out-of-Scope for MVP (but go crazy if you want to)
- Voice/video chat  
- AI auto-drawing
- Native mobile app  
- Tournament/league mode  

---

### 🔑  _EduPictionary_ pushes learners into the deep end of **real-time multiplayer web apps**, cementing skills in WebSockets, presence, game state, and rapid AI-assisted development.