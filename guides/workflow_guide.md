# Vibecoding Workflow Cheat-Sheet

While **vibecoding** invites you to follow the vibe — letting your AI pair-programmer take you wherever inspiration strikes — seasoned builders know that a *tiny bit* of process goes a long way. A lightweight structure lets you explore quickly **without painting yourself into a corner**.

The projects in this course all have certain requirements and guidelines, with decreasing levels of guidance as you progress. The **PRD** of a project is an important anchor for both you and your AI collaborators. It gives you a big-picture view of the project, lets you deconstruct it into phases of development, keeps the user's experience and pains in mind, and — most importantly — lets you keep track of your learnings. You can also use the PRD to guide your AI agents and LLMs.

---

## Suggested High-Level Workflow

```
PRD → Project Phases → Checklists → Agentic Development → Debugging → Deployment
```

---

### 1. Break the PRD into Phases

Typical phases might be

1. **UI scaffolding**
2. **Database schema and setup** *(or pick a managed NoSQL DB like Firestore if you expect lots of early schema churn)*
3. **Connect frontend to database** (CRUD)
4. **Add auth**
5. **User profile management**
6. **Backend features** (queues, cron, etc.)
7. **Security** (route protection, RBAC)

You can start backend-first, frontend-first, or clone a starter template that already includes auth and basic plumbing.

> Tip: If you already have a favourite boiler-plate stack (e.g. Next.js + Supabase + shadcn/ui), clone it first. Forkable templates dramatically reduce early scaffolding time and keep the "vibe" focused on features, not plumbing.

---

### 2. The Mini-Cycle (Recursive)

For **every phase, feature, or bug** repeat a tight loop:

1. **Plan**  → ask an LLM for a checklist (see next section).
2. **Develop**  → let the agent implement.
3. **Test & debug**  → run quick checks, logs, or unit tests.
4. **Deploy**  → even to a preview or "staging" URL; surface prod-only issues early.

This cycle is recursive — you'll bounce between phases often. Frequent commits, branches, and roll-backs are essential.

Think of this as an *inner game loop*. Advanced devs sometimes automate steps 2–4 with CLI agents (Claude Code, Copilot Agent), but the pattern stays the same.

---

### 3. Checklists as the Agent's Compass

Create plain-text checklists (`.md`) for each new chunk of work.

1. Feed the model:
   * **PRD**
   * **Current state** (key files / previous checklists)
   * **Goal** (new feature, bug details, tech decisions)
   * **Logs / errors**
1. Ask for ~20 clear items (no code snippets). Keep it simple and atomic — the agent should finish each in <5 min.
2. Use (or adapt) the following **planning prompt**:

```
We are in the process of building the project described in @PRD.md
We are doing this by going through the phases described in @PhasePlan.md
We are currently in stage X and have recently implemented the changes in @ChecklistX.md

Please create a new plan for implementing the missing elements of phase X.
Look around the repo and past checklists to understand the current project.
Then create a .md checklist with ~20 items that will allow us to make the most progress towards finishing the current phase. Keep the items simple and use max 2 layers of hierarchy. Do not put in any code snippets.
```


3. Use (or adapt) the following **workflow prompt**:

```
For each unchecked item in the checklist:
1. Read the item.
2. Gather context (PRD, checklists, relevant files).
3. Plan the edits.
4. Execute.
5. If backend-related, run a quick terminal test.
6. Mark the item complete & note decisions/bugs.
7. Repeat until checklist is done.
```

Agents vary in how strictly they follow steps; **your job is orchestration** — intervene, test, commit. When an agent drifts, *re-prompt* with the current checklist state and remind it of the workflow.

---

### 4. Deployment Mindset

Deploy **early & often**. Treat deployment like saving in a video game — the more checkpoints, the less painful any mis-step. Feeling nervous pushing "real" code live the first time is normal; frequent preview deploys make production feel routine fast. Platforms like Vercel, Netlify, or Supabase App Hosting can auto-deploy preview URLs from every PR so testers can click rather than clone.

---

### 5. Security Basics (bake-in from the start)

1. **Never hard-code secrets** — use env vars.
2. **`.gitignore` your secrets** — keep the repo private until ready.
3. Ask the agent for **security audits**, but avoid premature over-engineering — "MVP-level secure" beats "perfect but shipped next year."

---

### 6. Prompting Tips

Templates beat ad-hoc chat:

* **Planning prompt** (above) – converts a fuzzy goal into a clear checklist.
* **Workflow prompt** (above) – drives the checklist loop.
* **Debugging prompt** – asks the agent to locate, reproduce, and patch a bug.

```
We are currently in stage X and have recently implemented the changes in @ChecklistX.md

While implementing {checklist_item} we have encountered the following error/problem:

{paste error message/description/logs}

We have already tried fixing it with the following approaches:

{Ask agent to describe what it already tried and why it didn't work}

Please look at all the relevant context before proposing a plan how to fix this issue.

There is a high likelyhood that part of the problem is that we have overcomplicated this. There is probably a very simple fix that any requires taking a step back to find it.

Propose 3 approaches to fix the issue, and ask for any additional context, logs, or experiments that we can run to improve your guess.

Then choose the most likely reason for our problem and put it on top of your plan.
```

* **Testing prompt** – scaffolds unit or e2e tests before writing code.

```
We are currently in stage X and have recently implemented the changes in @ChecklistX.md

I want to make sure everything works properly.

Please conduct a simple set of tests, either by creating a testing skript or simply by testing API routes and backend features via your terminal.

I am looking for the simplest, most straightforward confirmation that everything works. We are building an MVP here, not Google core infrastructure.

Execute the tests right away, and report back to me with any issues.
```

Feel free to improvise, but a consistent core set reduces variance.

---

### 7. Context Management

* **Start new chats** often — long threads degrade model performance (context windows saturate and the model halluc-splains).
* Use `@tags` (docs, markdown, code) to control context and keep prompts <20 k tokens.
* Tools like **MCP servers**, **gitingest**, or **repoprompt** help load large codebases or external docs.
* Keep the repo clean; remove stale files to shrink context.

---

### 8. Model Selection

Trade-offs: **quality × speed × cost**.

* Some models plan better, others execute or debug better — mix & match (e.g. Claude 3.5 for planning, GPT-4o for codegen).
* Fast models fail faster → more iterations; slow "frontier" models may be worth the wait for hairy algorithmic tasks.

A/B test routinely and watch benchmarks like **SWE-bench**.

---

### 9. Continuous Learning

* Try new **models, tools, stacks, workflows**; log what feels 10× and what feels 10× slower.
* Document learnings in a **vault of `.md` files**.
* Share insights with the community — that's your portfolio and signal of expertise.

---

> **Key takeaway:** Your main value as an AI-first developer is **orchestrating**. Keep the loop tight, test often, commit frequently, and let your agents vibe — within guardrails you set.