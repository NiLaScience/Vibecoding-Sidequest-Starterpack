# Vibecoding Starter Pack  
*AI‑First Development, distilled to the essentials*

---

## 0  Why This Repo Exists  

You're here because you know how to code but haven't shipped a full product.  
Or maybe you have, but you see the AI wave coming and know that **competence > credentials**.

The **vibecoding** starter pack is your **golden bridge to impact**.
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

| # | Sidequest | New Hard Thing |
|:-:|---------|----------------|
| 01 | **PromptLibrary** | First AI‑built CRUD |
| 02 | **PBLab** | Relational schema + RBAC |
| 03 | **EduPictionary** | Real‑time WebSockets & game state |
| 04 | **PodBrain** | RAG search, embeddings, serverless pipelines |
| 05 | **VidTutor** | Full AI video generation, payments, IaC |

The hand‑holding fades; your autonomy grows.

---

## 5  Community & Resources  

| Resource             | Link                                     |
| -------------------- | ---------------------------------------- |
| **Discord**          | `[placeholder – invite link]`            |
| **Program website**  | `[placeholder – vibecoding.dev]`         |
| **YouTube playlist** | `[placeholder – full series]`            |
| **Blog**             | `[placeholder – medium.com/@vibecoding]` |
| **Monthly Demo Day** | `[placeholder – Typeform]`               |

Come say hi, show off builds, ask for code reviews, or drop PRs to improve the guides.

---

## 6  Keeping It Viby  

* **Checklist > spec.** Ask an LLM to turn any fuzzy goal into 8‑10 atomic tasks.  
* Learn the basics: Git for version control, Markdown in Obsidian for learning documentation, deploymen on Vercel or Replit.
* **Deploy early**—every commit that hits prod boosts confidence.  
* **Document the stumble.** Red errors become fossils of learning; write them down.  
* **Share the win.** A 30‑sec screen‑record is marketing gold *and* portfolio ammo.

Happy vibecoding!