# 01_PromptLibrary – Prompt-Management App  
_Product-Requirements Document (PRD)_

> The **first project** in the AI-First Development course must be approachable, quick to deploy, and heavily assisted by an AI coding agent.  
> Learners will build the MVP **entirely inside Replit**, iterate with the Replit AI Agent, then download the code and explore it locally with Cursor's agent.

---

## 1. Vision & Overview
Large-language-model workflows live and die by good prompts. *PromptLibrary* lets individual users **collect, edit, tag and re-use prompt templates** with dynamic variables.
The initial release is a **single-user CRUD web app** that supports:

1. Creating a prompt template with placeholder variables  
2. Filling variables to generate a final prompt string  
3. Organising templates by tags/folders
4. Copy-to-clipboard & basic usage-count tracking  
5. Up/down voting on prompts

---

## 2. Personas
| Persona  | Goals | Pain Points |
|----------|-------|-------------|
| **AI-first Developer** | Keep a personal vault of prompts, iterate quickly | Prompts scattered across docs & chat histories |
| **Content Creator** | Re-use prompts for social posts & blogs | Hard to remember which prompt worked best |
| **LLM Power User** | Wants to have the perfect prompt for every use case and every model | Comparing prompts performance for different models |

---

## 3. Core User Stories
1. **Create Prompt** – As a user I can add a new prompt template with a title, description, body text and 🌟 `{{variable}}` place-holders.  
2. **Edit / Delete Prompt** – I can update or remove templates I no longer need.  
3. **Tag Prompts** – I can assign one or more tags to a template and filter by tag.  
4. **Generate Prompt** – I can fill in variables in a small form and copy the final prompt to clipboard.  
5. **View Stats** – Each template shows how many times it was copied (basic effectiveness metric).
6. **Up/Down Vote** – Each template can be up/down voted by the user. We can rank prompts in each category by rating. We can rate prompt performance for different models.

---

## 4. MVP Features & Cut-Lines
| Feature | MVP? | Notes |
|---------|------|-------|
| Prompt CRUD (title, body, tags) | ✅ | Required |
| Variable detection (`{{var}}`) | ✅ | Simple regex |
| Prompt generation UI + copy | ✅ | Clipboard API |
| Tag filtering | ✅ | Multi-select dropdown |
| Folder Organization | ✅ | Create folders to organize prompts, view prompts in folders |
| Usage counter | ✅ | Increment on copy |
| Up/Down voting per prompt & model | ✅ | Increment on vote |
| User authentication | ❌ | Single-user for now (Replit account acts as owner) |
| Sharing / Public prompts | ❌ stretch | Later via Supabase row-level-security |
| LLM API execution | ❌ stretch | Could call OpenAI/Claude/Gemini for A/B testing |


---

## 5. Technical Decisions (AI-First on Replit)
| Area | Decision | Rationale |
|------|----------|-----------|
| **IDE** | **Replit** with the [Replit Agent](https://replit.com/new) | One-click cloud dev & deploy |
| **Stack** | React + Vite front-end; Express + SQLite (or Replit DB) back-end | Simple, zero-config on Replit |
| **Persist­ence** | `@replit/database` key-value OR SQLite file | No external signup needed |
| **Styling** | Tailwind CSS (via CDN to avoid build config) | Fast, utility-first |
| **Auth** | Replit's built-in project owner session; single user | Keep first project friction-free |
| **Deployment** | Replit's "Deploy" → **Static / Node Web-Server** | Zero-ops |
| **AI Use** | Replit Agent generates boilerplate, adds DB later, can integrate LLM APIs | Demonstrates AI-assisted workflow |

---

## 6. Data Model (tiny!)
```sql
-- SQLite schema
CREATE TABLE folders (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL UNIQUE,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE prompts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  description TEXT,          -- optional description
  body TEXT NOT NULL,        -- contains {{variables}}
  tags TEXT,                 -- comma-separated
  folder_id INTEGER,         -- optional folder assignment
  copy_count INTEGER DEFAULT 0,
  up_votes INTEGER DEFAULT 0,
  down_votes INTEGER DEFAULT 0,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (folder_id) REFERENCES folders(id) ON DELETE SET NULL
);


```

---

## 7. Tests-to-Pass (Acceptance Criteria)
| ID | Test | How to Verify |
|----|------|---------------|
| **P-01** | User can add a prompt with at least one `{{variable}}`. | Cypress: fill form → list shows new prompt. |
| **P-02** | Variable placeholder list auto-detected. | Unit: POST `/api/parse` → returns `["variable"]`. |
| **P-03** | Copy button increases `copy_count`. | E2E: click copy twice → list shows count = 2. |
| **P-04** | Tag filter narrows list. | UI: select tag "blog" → only tagged prompts visible. |
| **P-05** | Folder filter narrows list. | UI: select folder "blog" → only prompts in folder visible. |
| **P-06** | Up/Down voting per prompt & model. | UI: click up/down vote button → rating percentage changes. |


---

## 8. AI-First Workflow Guide (Learner Steps)

1. **Create new App in Replit** → choose *"Node.js + React"* template.  
2. **Draft a single prompt** to Replit AI Agent with reasoning model:  
   > "Build a minimal CRUD app called PromptLibrary … [copy 80 % of PRD]"  
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
   * `npm install && npm run dev`  
   * Ask Cursor Agent: "Explain the project architecture file-by-file."  
   * Ask more questions to the agent to understand the project and the code.
7. **Document learning goals**
    - go through the learning_goals.md file and select which items you encountered in this project.
    - create .md notes for each learning goal covered in this project. Use AI to help you with this.
    - if you encountered any other learnings not covered in the learning_goals.md file, create .md notes for them.
8. **Share your Proof-of-Competence**
    - add project to your public github.
    - create and share a thread or video of you using the app and sharing your learnings.
    - put your repo on your github profile and share with people.

---

## 9. Focused Learning Goals

This project will give you hands-on experience with the following core concepts. Check them off in `goals/learning_goals.md` as you master them.

### Chapter 1: Product, Business & Legal
- [[learning_notes/What is an MVP.md|What is an MVP?]]
- [[learning_notes/What is a product requirements document (PRD).md|What is a product requirements document (PRD)?]]

### Chapter 2: Development Workflow, Tooling & QA
- [[learning_notes/What is version control.md|What is version control?]]
- [[learning_notes/What are package managers.md|What are package managers?]]
- [[learning_notes/What is an IDE.md|What is an IDE?]]
- [[learning_notes/What are AI developer tools.md|What are AI developer tools?]]
- [[learning_notes/What is a template boilerplate.md|What is a template / boilerplate?]]
- [[learning_notes/What are environment variables.md|What are environment variables?]]

### Chapter 3: Foundational Concepts & Core Technologies
- [[learning_notes/What is a route.md|What is a route?]]
- [[learning_notes/What is an API.md|What is an API?]]
- [[learning_notes/What is CRUD.md|What is CRUD?]]
- [[learning_notes/What is JSON.md|What is JSON?]]
- [[learning_notes/What is HTML.md|What is HTML?]]
- [[learning_notes/What is CSS.md|What is CSS?]]
- [[learning_notes/What is JavaScript.md|What is JavaScript?]]
- [[learning_notes/What is a state.md|What is a state?]]

### Chapter 5: Front-End Development
- [[learning_notes/What is React.md|What is React?]]
- [[learning_notes/What is a component.md|What is a component?]]
- [[learning_notes/What is Tailwind CSS.md|What is Tailwind CSS?]]
- [[learning_notes/What is a router.md|What is a router?]]
- [[learning_notes/What are forms.md|What are forms?]]
- [[learning_notes/What is form validation.md|What is form validation?]]
- [[learning_notes/What is debouncing.md|What is debouncing?]]

### Chapter 6: Back-End Development
- [[learning_notes/What is Node.js.md|What is Node.js?]]
- [[learning_notes/What is REST.md|What is REST?]]

### Chapter 7: Data Storage & Management
- [[learning_notes/What is a database.md|What is a database?]]
- [[learning_notes/What is a schema.md|What is a schema?]]

### Chapter 9: Infrastructure & Deployment
- [[learning_notes/What is hosting.md|What is hosting?]]
- [[learning_notes/What is a build process.md|What is a build process?]]
- [[learning_notes/What is CI CD.md|What is CI/CD?]]

### Chapter 11: Specialized Development & AI
- [[learning_notes/What are AI developer tools.md|What are AI developer tools?]]
- [[learning_notes/What is a GenAI API.md|What is a GenAI API?]]

---

### 🔑  *PromptLibrary* gives learners an immediate win: a useful personal tool, a gentle introduction to full-stack concepts, and a safe playground for AI-assisted coding—all in under two hours of guided building.