```md
# 02_PBLab – Problem-Based Learning LMS  
_Product-Requirements Document (PRD)_

## 1. Vision & Overview
PBLab is a lightweight, AI-augmented Learning-Management System that helps **students work in small teams on problem-based learning (PBL) projects** and lets **educators facilitate, observe, and assess** their progress.  
Instead of static assignments, PBLab structures work into the classic PBL cycle:

1. Pre-discussion → Problem statement & learning goals  
2. Self-directed research & artifact creation  
3. Post-discussion → Findings, reflection & report  

PBLab stores all metadata, workflow states and comments in a relational database (Supabase/Postgres) while **leveraging Google Docs/Slides/Sheets links for rich artifacts**. LLM tools are embedded as first-class citizens (e.g. “draft learning goals”, “critique my reflection”) to encourage *responsible* AI use.

The project replaces the original “CRMagic” MVP.  It remains a **multi-user CRUD app with complex but approachable data structures**, fulfilling the same scope while feeling mission-driven and modern.

---

## 2. Personas
| Persona | Goals | Pain Points |
|---------|-------|-------------|
| **Student** | Understand the problem, split tasks, document findings, create cool deliverables | Disjoint tools, unclear expectations, fear of “AI = cheating” |
| **Educator / Facilitator** | Monitor teams, give feedback, grade fairly, promote critical thinking | No visibility into team process, plagiarism worries |
| **Course Admin** | Manage problems, cohorts, permissions | Manual setup each term |

---

## 3. User Stories (Happy-Path)
### 3.1 Students
1. _As a student_ I can **join a team** via invite link so that I can collaborate on a PBL problem.  
2. I can **read the official problem description** and **propose a problem statement** with learning goals (Markdown or Google Doc link).  
3. I can **upload or link artifacts** (images, videos, docs, code repos) under a research phase.  
4. I can **tag teammates on comments** to discuss artifacts asynchronously.  
5. I can **use the “AI Coach”** to brainstorm learning goals or get critique on my reflection, with usage logged for transparency.  
6. I can **submit the final report link** and mark the project “Ready for review”.

### 3.2 Educators
1. _As an educator_ I can **create a new PBL problem**, defining title, description, and rubric.  
2. I can **view dashboards** of teams, their current phase, and pending questions.  
3. I can **leave rubric-based feedback** on the final report and lock the project.

### 3.3 Course Admin
1. _As an admin_ I can **add/remove users, create cohorts**, and assign educators to problems.

---

## 4. Key Features & MVP Cut-Lines
| Feature | MVP? | Notes |
|---------|------|-------|
| Team & Role management | ✅ | Students, Educators, Admins |
| Problem lifecycle states (Pre / Research / Post / Closed) | ✅ | State machine in DB |
| Learning-goal editor + AI “Suggestion” button | ✅ | Calls OpenAI / Gemini function |
| Artifact upload & metadata | ✅ | File to Supabase Storage OR external URL |
| Comment threads & mentions | ✅ | Simple per-artifact thread |
| Google Docs/Slides/Sheets link handling | ✅ | Detect & embed previews |
| Dashboard for educators | ✅ | Table / Kanban view |
| Feedback & basic rubric | ✅ | Per-criterion 1-5 rating + comment |
| Real-time presence / typing | ❌ (stretch) | Could reuse Project 3 code |
| Video/voice chat | ❌ | Out of scope |

---

## 5. Technical Decisions
| Area | Decision | Rationale |
|------|----------|-----------|
| **Stack** | Next.js (App Router) scaffolded by **V0** → edited in **Cursor** | Consistent with course workflow |
| **DB** | **Supabase Postgres** with Prisma (optional) | SQL clarity; built-in auth & storage |
| **Local Dev** | Supabase in **Docker Compose**; `npm run dev` for Next.js | One-command setup |
| **Auth** | Supabase email-link auth; RBAC via RLS policies | Fast, secure |
| **Hosting** | Vercel (app) + Supabase cloud | Matches Project 1 |
| **AI** | OpenAI functions via serverless route `/api/ai/*`; usage logged in `ai_usage` table | Simple, auditable |
| **Integrations** | Google OAuth scopes limited to Drive read-only for file metadata | Avoids storing large files |

---

## 6. Data Model (simplified)
```mermaid
erDiagram
  users ||--o{ teams_users : membership
  teams ||--o{ projects : has
  projects ||--o{ learning_goals : contains
  projects ||--o{ artifacts : upload
  projects ||--o{ reports : write
  artifacts ||--o{ comments : discuss

  users {
    uuid id PK
    text name
    text email
    text role -- 'student' | 'educator' | 'admin'
  }

  teams {
    uuid id PK
    text name
    uuid course_id
  }

  projects {
    uuid id PK
    uuid team_id FK
    text title
    text subject_area
    text phase -- 'pre'|'research'|'post'|'closed'
    timestamptz created_at
  }

  learning_goals {
    uuid id PK
    uuid project_id FK
    text description
    uuid assigned_user
  }

  artifacts {
    uuid id PK
    uuid project_id FK
    text title
    text type -- 'doc'|'image'|'video'|'link'
    text url
    uuid uploaded_by
  }

  reports {
    uuid id PK
    uuid project_id FK
    uuid author_id
    text doc_url
    text summary
  }

  comments {
    uuid id PK
    uuid artifact_id FK
    uuid author_id
    text body
    timestamptz created_at
  }
```

---

## 7. Built-In AI-First Example Problems
### 7.1 “Outbreak Simulator” (Epidemiology)
| Item | Description |
|------|-------------|
| **Problem Desc.** | A mysterious pathogen emerges in a city. Model its spread and propose containment strategies. |
| **Deliverable** | A playable web simulation (SIR model) + report comparing R₀ under interventions. |
| **Learning Goals** (sample) | • Understand SIR equations • Implement basic differential equations in JS • Interpret R₀ • Evaluate vaccination vs. quarantine |
| **Artifacts** | Google Sheet with data, code repo link, demo GIF |
| **AI Coach Prompts** | “Suggest parameter ranges for an airborne virus”, “Explain R₀ to a 12-year-old” |

### 7.2 “EcoBalance” (Predator-Prey Ecology)
| Item | Description |
|------|-------------|
| **Problem Desc.** | Design a simple ecosystem where predators and prey reach dynamic equilibrium. Adjust variables to prevent extinction. |
| **Deliverable** | Interactive Lotka-Volterra simulation + reflective essay on sustainability parallels. |
| **Learning Goals** | • Implement predator-prey equations • Visualize populations over time • Identify tipping points |
| **Artifacts** | Time-series charts, short explainer video, code link |
| **AI Coach Prompts** | “Derive Lotka-Volterra from first principles”, “Suggest UX to show population collapse” |

---

## 8. Tests-to-Pass (Sample Acceptance Criteria)
| ID | Test | How to Verify |
|----|------|---------------|
| **T-01** | Student can create team via UI and invite peer via email. | Cypress E2E: create → invite → peer accepts → team appears in dashboard. |
| **T-02** | Learning-goal AI helper returns ≤ 3 suggestions and logs usage. | Jest unit: mock OpenAI → assert DB `ai_usage` row. |
| **T-03** | Artifact upload enforces file-type whitelist. | API test: upload `.exe` → 400 error. |
| **T-04** | Educator feedback locks project editing. | E2E: add feedback → check inputs disabled. |

---

## 9. Quick-Start Guide for Learners
1. `pnpm create v0-app` → choose **PBLab** template  
2. Clone repo, open in **Cursor** → run `pnpm dev`  
3. `docker compose up supabase` for local DB  
4. Copy `.env.example` → `.env.local`, add Supabase keys & OpenAI key  
5. Push to GitHub → Vercel import → environment variables → **Deploy**  
6. Run seed script `pnpm db:seed` to create sample problems & users  

---

## 10. Focused Learning-Goal Subset  
_The following items from_ `combined_structure.md` _are primarily exercised_:  

**Chapter 2** – Version Control, Package Management, Linting, Template/Boilerplate, Env Variables, Unit & Integration Testing  
**Chapter 3** – Monorepo (optional), Route, API, CRUD, JSON, State Management (Context/Zustand)  
**Chapter 4** – Next.js (App Router)  
**Chapter 5** – React Components, Tailwind, Forms & Validation  
**Chapter 6** – REST, Webhooks (Google Drive), File Upload, Cron Jobs (future), Node.js backend  
**Chapter 7** – SQL Schema, Relationships, Supabase/Postgres, Prisma/Drizzle, Migrations  
**Chapter 8** – OAuth (Google), JWT, RBAC  
**Chapter 9** – Vercel Hosting, Docker, Serverless Functions, CI/CD, IaC (basic), Caching/CDN, Monitoring & Tracing (starter)  
**Chapter 10** – Third-Party API Integration (Google), OpenAI API  
**Chapter 11** – GenAI API, Prompt Engineering, AI Agent basics (Coach helper)

---

## 11. Open Questions
1. Should rubric scoring be numeric, qualitative, or both?  
2. How granular must AI usage logs be for academic integrity?  
3. Do we require offline/real-time sync (CRDT) for simultaneous editing?  
4. Accessibility (a11y) checklist for student-created artifacts?  

---

## 12. Out-of-Scope for MVP
- Full LMS grade-book & attendance  
- Real-time collaborative document editing (delegated to Google Docs)  
- Mobile-native app (responsive web only for MVP)  
- Push notifications (can reuse in Project 3)

---

### 🔑  PBLab delivers a purpose-driven CRUD experience with rich relational data, role-based access, and ethical AI integration—perfectly aligned with the course’s AI-first philosophy.
```