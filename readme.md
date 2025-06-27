# Vibecoding Starter Pack  
*AI‑First Development, distilled to the essentials*

---

Go from vibecoder to AI-first developer. Guaranteed results.

Top project submission gets paid $250 each month on Sidequests.gg

## 0  Why This Course Exists  

The \#1 reason why other courses fail is because they waste your time with theoretical lessons and exercises, instead of spending 90% of your time building.

This course is different!

The goal of this course is simple: to get you paid as fast as possible. Be that by getting a job in a company, joining a startup, or turning a side project into a real business.

This course is by far the fastest way to go from some coding knowledge but little to no development experience to a full portfolio of deployed projects with actual users. 

This will serve as proof-of-competence to potential employers, cofounders, investors, and your online audience.

It's designed to turn the gap between tutorials and shipping into a sprint:

* **Five progressively harder sidequests** (PromptLibrary → VidTutor) that build real‑world products.
* **Guides** that show how to orchestrate AI agents instead of writing every line yourself.
* **A learning‑goal matrix** to help you build **proof of competence** you can actually show employers.

Ship → reflect → ship again. That's the loop.  

---

## 1  Repo at a Glance  

| Path | What You'll Find |
|-----|------------------|
| `projects/` | 01‑05 PRDs that drive each build |
| `guides/` | *Learning*, *Tools*, *Workflow* cheat‑sheets |
| `goals/learning_goals.md` | 11‑chapter checklist of everything you're expected to touch |
| `learning_notes/` | *You* add reflection docs here as you learn (empty by default) |

---

## 2  What You Need Before You Start  

| Account / Tool | Why |
|---------------|-----|
| **Replit** | Zero‑to‑CRUD cloud IDE + Agent (Project 01) |
| **Vercel v0** | Prompt‑to‑UI generator (Projects 02‑03) |
| **Cursor *or* Windsurf IDE** | Local AI‑powered editor for serious refactors |
| **GitHub** | Repo hosting, PRs, portfolio proof |
| **OpenAI / Gemini / Anthropic API keys** | The projects call GenAI endpoints |
| *(Optional)* **Supabase** cloud org | Postgres + storage used in Projects 02‑03 |

*Tip:* Keep all keys in a `.env.local` and **never** commit them.

---

## 3  How to Get Started (10‑Minute Sprint)  

1. **Fork** this repo → `gh repo fork ...`  
2. **Open Project 01**: `projects/01_PromptLibrary.md` (skim scope)  
3. Paste the PRD into an LLM and discuss the project. Then build a prompt of 30 lines or less that describes 80% of UI features.
4. Spin up Replit Agent→ Paste your prompt, watch the agent build.
5. Iterate a few times until you are happy with the first draft, then hit deploy.
6. Download the code, open the repo in Cursor and ask: "Explain the folder structure."  
7. Create `notes/learning_reflection.md` and log what you just learned.

That's the pattern you'll repeat—only the tech and complexity change.

---

## 4  Course Roadmap (Ultra‑Compact)

|  #  | Sidequest         | New Hard Thing                               |
| :-: | ----------------- | -------------------------------------------- |
| 01  | **PromptLibrary** | First AI‑built CRUD                          |
| 02  | **PBLab**         | Relational schema + RBAC                     |
| 03  | **EduPictionary** | Real‑time WebSockets & game state            |
| 04  | **PodBrain**      | RAG search, embeddings, serverless pipelines |
| 05  | **VidTutor**      | Full AI video generation, payments, IaC      |

The hand‑holding fades; your autonomy grows.

---
## 5  The Vibecoding Loop  

1. **Absorb the PRD**  
   *Skim the vision, user stories & cut‑lines.*  
2. **Phase the Work**  
   Split the PRD into 2‑to‑4 hour "mini‑milestones" (UI scaffold, DB schema, auth, etc.).  
3. **Checklist It**  
   Drop a *Planning Prompt* into your agent:  
4. **Agentic Execution**  
Let the agent tackle each unchecked box. Intervene, test, commit.  
5. **Deploy Early**  
Replit "Deploy", Vercel Preview, Firebase Hosting—whatever ships fastest.  
6. **Reflect & Map**  
After each milestone open `learning_goals.md` and mark which bullets you *actually* touched. Jot a quick note or screenshot.  
7. **Rinse & Repeat**  

```

PRD → Phase → Checklist → Agent → Test/Deploy → Reflect

```

---

## 6  Tooling Cheat‑Sheet (Super Condensed)  

| Need                            | Tool             | 1‑Line Tip                                            |
| ------------------------------- | ---------------- | ----------------------------------------------------- |
| Zero‑to‑CRUD in the browser     | **Replit Agent** | Start Project 01 entirely here.                       |
| Local deep dives & git wizardry | **Cursor IDE**   | Import repo, ask "Explain folder structure."          |
| Semantic codebase edits         | **Windsurf**     | Great for "rename model, update everywhere".          |
| Autonomous terminal hacker      | **Claude Code**  | Run inside Cursor's terminal for observability.       |
| Design‑heavy UI                 | **Vercel v0**    | Prompt "Dashboard with sidebar & dark mode" → export. |
| Postgres + Realtime + Vec       | **Supabase**     | Projects 02 & 03 default backend.                     |
| NoSQL + Edge Functions + Gemini | **Firebase**     | Projects 04 & 05 showcase AI pipelines.               |

👉  Full details live in `guides/tools_synthesized.md`.
## 7  Community & Resources  

| Resource             | Link                                     |
| -------------------- | ---------------------------------------- |
| **Discord**          | `[https://discord.gg/4ymrJF7z]`            |
| **Program website**  | `[https://sidequests.gg/challenges]`         |
| **YouTube playlist** | `[https://youtube.com/playlist?list=PL4O-7Ed28SRoEtvcIqHtcv5MothPz7sUF&si=H9vylA5V1VoPnlDw]` |

Come say hi, show off builds, ask for code reviews, or drop PRs to improve the guides.

---
