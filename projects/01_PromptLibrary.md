```md
# 01_PromptLibrary – Prompt-Management App  
_Product-Requirements Document (PRD)_

> The **first project** in the AI-First Development course must be approachable, quick to deploy, and heavily assisted by an AI coding agent.  
> Learners will build the MVP **entirely inside Replit**, iterate with the Replit AI Agent, then download the code and explore it locally with Cursor’s agent.

---

## 1. Vision & Overview
Large-language-model workflows live and die by good prompts. *PromptLibrary* lets individual users **collect, edit, tag and re-use prompt templates** with dynamic variables.  
The initial release is a **single-user CRUD web app** that supports:

1. Creating a prompt template with placeholder variables  
2. Filling variables to generate a final prompt string  
3. Organising templates by tags/folders  
4. Copy-to-clipboard & basic usage-count tracking  

> Scope deliberately stays small; stretch goals are called out but **not required** for the first build.

---

## 2. Personas
| Persona  | Goals | Pain Points |
|----------|-------|-------------|
| **Early-Career Developer** | Keep a personal vault of prompts, iterate quickly | Prompts scattered across docs & chat histories |
| **Content Creator** | Re-use prompts for social posts & blogs | Hard to remember which prompt worked best |
| **Course Learner** | Understand full-stack basics with minimal friction | Overwhelmed by boilerplate, deployment hurdles |

---

## 3. Core User Stories
1. **Create Prompt** – As a user I can add a new prompt template with a title, description, body text and 🌟 `{{variable}}` place-holders.  
2. **Edit / Delete Prompt** – I can update or remove templates I no longer need.  
3. **Tag Prompts** – I can assign one or more tags to a template and filter by tag.  
4. **Generate Prompt** – I can fill in variables in a small form and copy the final prompt to clipboard.  
5. **View Stats** – Each template shows how many times it was copied (basic effectiveness metric).

---

## 4. MVP Features & Cut-Lines
| Feature | MVP? | Notes |
|---------|------|-------|
| Prompt CRUD (title, body, tags) | ✅ | Required |
| Variable detection (`{{var}}`) | ✅ | Simple regex |
| Prompt generation UI + copy | ✅ | Clipboard API |
| Tag filtering | ✅ | Multi-select dropdown |
| Usage counter | ✅ | Increment on copy |
| User authentication | ❌ | Single-user for now (Replit account acts as owner) |
| Sharing / Public prompts | ❌ stretch | Later via Supabase row-level-security |
| LLM API execution | ❌ stretch | Could call OpenAI for A/B testing |
| Dark-mode theme | ❌ nice-to-have |

---

## 5. Technical Decisions (AI-First on Replit)
| Area | Decision | Rationale |
|------|----------|-----------|
| **IDE** | **Replit** with the new [AI Agent](https://replit.com/site/ai) | One-click cloud dev & deploy |
| **Stack** | React + Vite front-end; Express + SQLite (or Replit DB) back-end | Simple, zero-config on Replit |
| **Persist­ence** | `@replit/database` key-value OR SQLite file | No external signup needed |
| **Styling** | Tailwind CSS (via CDN to avoid build config) | Fast, utility-first |
| **Auth** | Replit’s built-in project owner session; single user | Keep first project friction-free |
| **Deployment** | Replit’s “Deploy” → **Static / Node Web-Server** | Zero-ops |
| **AI Use** | Replit Agent generates boilerplate, adds DB later, refactors | Demonstrates AI-assisted workflow |

---

## 6. Data Model (tiny!)
```sql
-- SQLite schema
CREATE TABLE prompts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  body TEXT NOT NULL,        -- contains {{variables}}
  tags TEXT,                 -- comma-separated
  copy_count INTEGER DEFAULT 0,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## 7. Tests-to-Pass (Acceptance Criteria)
| ID | Test | How to Verify |
|----|------|---------------|
| **P-01** | User can add a prompt with at least one `{{variable}}`. | Cypress: fill form → list shows new prompt. |
| **P-02** | Variable placeholder list auto-detected. | Unit: POST `/api/parse` → returns `["variable"]`. |
| **P-03** | Copy button increases `copy_count`. | E2E: click copy twice → list shows count = 2. |
| **P-04** | Tag filter narrows list. | UI: select tag “blog” → only tagged prompts visible. |

*(Only P-01 is *required* before moving on; others are stretch learning.)*

---

## 8. AI-First Workflow Guide (Learner Steps)

1. **Create Repl** → choose *“Node.js + React”* template.  
2. **Draft a single prompt** to Replit AI Agent:  
   > “Build a minimal CRUD app called PromptLibrary … [copy 80 % of PRD]”  
3. **Iterate and Test**:  
    - Add a database
    - Add remaining features from PRD
    - Add stretch goals
    - Customize the UI
    - Change styling to your liking
    - Work on mobile responsiveness
4. **Deploy** with Replit Deploy. Test in browser.  
5. **Download ZIP** (`⋯ → Download as zip`).  
6. **Open in Cursor** locally:  
   * `pnpm install && pnpm dev`  
   * Ask Cursor Agent: “Explain the project architecture file-by-file.”  
   * Refactor names, add comments, experiment.  
7. **Document learning goals**
    - go through the combined_structure.md file and select which items you encountered in this project.
    - create .md notes for each learning goal covered in this project.
    - if you encountered any other learnings not covered in the combined_structure.md file, create .md notes for them.
8. **Share your Proof-of-Competence**
    - create and share a thread or video of you using the app and sharing your learnings.
    - put your repo on your github profile and share with people.

---

## 9. Focused Learning-Goal Subset  
(from `combined_structure.md`)

| Chapter | Items Exercised |
|---------|-----------------|
| **1** – Product Strategy | MVP, PRD basics |
| **2** – Workflow & Tooling | Version control (GitHub optional), Package management (npm), IDEs (Replit, Cursor), AI dev tools, Linting + Prettier (optional), Template/Boilerplate, `.env` |
| **3** – Foundations | Route, API, CRUD, JSON, HTML/CSS/JS basics, State (React useState) |
| **4** – Frameworks | Vite (lightweight) |
| **5** – Front-End | React components, Tailwind, Forms & Validation, Router (minimal) |
| **6** – Back-End | Node.js, REST endpoints |
| **7** – Data Storage | SQLite / Replit DB, basic schema |
| **9** – Deployment | Cloud hosting (Replit), Build process, CI/CD (Replit auto-deploy) |
| **11** – AI Fundamentals | AI developer tools (Replit Agent), Prompt engineering (meta) |

*(No auth / advanced security yet – saved for later projects.)*


---

### 🔑  *PromptLibrary* gives learners an immediate win: a useful personal tool, a gentle introduction to full-stack concepts, and a safe playground for AI-assisted coding—all in under an hour of guided building.
```