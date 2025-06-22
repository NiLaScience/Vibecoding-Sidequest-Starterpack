# 05 _VidTutor_ – Generative Video-Tutor  
_Guidance & Research Brief (YOU will turn this into a full PRD)_

Project 5 is intentionally wide open: you will **own every product and technical decision**.  
This document gives you background, mandatory constraints, and an explicit research workflow so you can draft a unique, opinionated PRD.

---

## 0  How to Use This Brief

1. **Clone** this file into `projects/05_VidTutor.md`.  
2. Work through each section; replace ✍️ _placeholders_ and **delete guidance text** when no longer needed.  
3. Commit your completed PRD before writing any code.

---

## 1  Problem & Opportunity (context)

High-quality explanatory videos (think _3Blue1Brown, Kurzgesagt_) are scarce, slow to make, and expensive.  
LLMs + programmatic animation (Manim, SVG, Three.js, Lottie, etc.) can shrink production time from **weeks to minutes**.

> _VidTutor_ will let a learner type a topic (and optional learning goals) and receive a short, accurate, animated video with voice-over.

---

## 2  Minimum Required Outcomes (non-negotiable)

Your MVP **must**:

| # | Capability | Notes |
|---|------------|-------|
| R-1 | Accept user input: subject, grade level, length (min, max) | Form or API |
| R-2 | Produce a complete video file **with audio** and downloadable / shareable link | MP4, WebM, or GIF |
| R-3 | Persist every generated video + metadata in a database / object store | Incl. prompt, script, time-to-render |
| R-4 | Provide a simple UI to review previous videos | List + player |
| R-5 | Expose an **API endpoint** that triggers generation programmatically | For later integrations |
| R-6 | Provide basic observability: logs, metrics and at least one alert on pipeline failure | Sentry, Cloud Logging, or equivalent |
| R-7 | Provision at least one infrastructure component using an IaC script | Terraform or Pulumi |
| R-8 | Implement a simple pay-per-render flow | Stripe or comparable service |
| R-9 | Include contract & load tests for the generation pipeline | Mock external APIs where practical |

All other features (pricing screen, auth, comments, etc.) are optional.

---

## 3  Research-First Workflow

> "Great products start with strong points of view." — A16Z

### 3.1 Competitive Teardown

1. **Find & try** at least three existing AI-to-video tools (e.g. **Manim MCP**, **SlidesAI**, **Pika**, **Lumiere**, etc.).  
2. Record: start-to-finish time, output quality, cost, failure modes.  
3. **Publish** a short comparison on GitHub Issues or a blog post.

### 3.2 Community Engagement

* Star & fork relevant GitHub repos → open one _good_ question / issue.  
* Tweet / post on X a feedback thread; collect at least five responses from creators/educators.

### 3.3 Spiky POV Exercise

Write **five "Spiky POVs"** that intentionally disagree with popular opinions or competitor choices.  
Example:

> 1. "Charging \$20/mo forces devs to pick sub-par models; _VidTutor_ will chase state-of-the-art even if it costs \$5 per render."  
> 2. "Templates kill creativity; we'll expose the raw Manim script for power users."  
> 3. …

These POVs will drive your unique feature set and pricing later.

---

## 4  Suggested Architecture Options (you decide!)

| Layer | Firebase-Centric (🟢 easy) | Container / Cloud-Mix (⚙️ flexible) |
|-------|---------------------------|------------------------------------|
| Front-end | Next.js + Firebase Hosting | Next.js on Vercel / Fly.io |
| Auth | Firebase Auth (email / Google) | Auth0 or Clerk |
| DB | Firestore (video docs) | Postgres for metadata + S3 for videos |
| Object Store | Cloud Storage | S3 / Cloudflare R2 |
| Render Workers | Cloud Functions (Edge) **OR** Cloud Run | Dockerised workers on AWS Fargate |
| AI APIs | Gemini Pro (LLM) + Gemini Vision + Gemini Speech | Mix OpenAI, ElevenLabs, Stable Video |

**Trade-offs to think about**: cold-start time, GPU availability, cost caps, vendor lock-in.

---

## 5  High-Level Generation Pipeline (example)

```
User Prompt
   └──► 1.  Script Generator (LLM)  ──► markdown / pseudo-code slides
            2.  Scene Planner      ──► structured JSON (per slide)
            3.  Animation Engine   ──► Manim CLI renders .mp4 segments
            4.  TTS Voice-over     ──► .wav files
            5.  Composer (FFmpeg)  ──► final.mp4 + caption.vtt
            6.  Upload to Storage
            7.  Write Firestore doc
```

Feel free to collapse / merge steps, or swap Manim for another library.

---

## 6  Acceptance-Test Skeleton (edit/extend)

| ID | What to verify | Method |
|----|----------------|--------|
| V-01 | POST `/api/video` returns `202 Accepted` + job ID | Unit test |
| V-02 | Job status moves from `queued → rendering → complete` | Poll Firestore / DB |
| V-03 | Final video URL streams in browser | Cypress E2E |
| V-04 | Re-rendering identical prompt reuses cached assets (<= 50 % time) | Benchmark script |
| V-05 | Observability dashboard records each render; alert fires on simulated failure | Unit / live test |
| V-06 | `terraform apply` (or `pulumi up`) provisions storage bucket without manual steps | IaC smoke test |
| V-07 | Stripe test payment succeeds and writes usage record | Integration test |

---

## 7  Deliverables Checklist

- [ ] Final **PRD** committed (with filled-out sections, removed guidance).  
- [ ] Research write-up + 5 Spiky POVs (Markdown or blog).  
- [ ] Deployed MVP link + read-only auth account.  
- [ ] 2-minute demo video.  
- [ ] Learning-goal mapping file (`learning_reflection.md`).

---

## 8. Focused Learning Goals
This project will give you hands-on experience with the following core concepts. Check them off in `goals/learning_goals.md` as you master them.

### Chapter 1: Product, Business & Legal
- [[learning_notes/What is a landing page.md|What is a landing page?]]
- [[learning_notes/What is user feedback.md|What is user feedback?]]

### Chapter 2: Development Workflow, Tooling & QA
- [[learning_notes/What is version control.md|What is version control?]]
- [[learning_notes/What is CI CD.md|What is CI/CD?]]
- [[learning_notes/What are AI developer tools.md|What are AI developer tools?]]

### Chapter 3: Foundational Concepts & Core Technologies
- [[learning_notes/What is an API.md|What is an API?]]
- [[learning_notes/What is JSON.md|What is JSON?]]

### Chapter 5: Front-End Development
- [[learning_notes/What is a media player.md|What is a media player?]]
- [[learning_notes/What are forms.md|What are forms?]]

### Chapter 6: Back-End Development
- [[learning_notes/What is file upload.md|What is file upload?]]
- [[learning_notes/What is a job queue.md|What is a job queue?]]
- [[learning_notes/What are background workers.md|What are background workers?]]
- [[learning_notes/What is rate limiting.md|What is rate limiting?]]

### Chapter 7: Data Storage & Management
- [[learning_notes/What is a document store.md|What is a document store?]]
- [[learning_notes/What is an object store.md|What is an object store?]]
- [[learning_notes/What is SQL.md|What is SQL?]]

### Chapter 8: Authentication & Security
- [[learning_notes/What is Auth.md|What is Auth?]]
- [[learning_notes/What are signed URLs.md|What are signed URLs?]]

### Chapter 9: Infrastructure & Deployment
- [[learning_notes/What is hosting.md|What is hosting?]]
- [[learning_notes/What is a container.md|What is a container?]]
- [[learning_notes/What is infrastructure as code (IaC) (stretch).md|What is infrastructure as code (IaC)? (stretch)]]
- [[learning_notes/What is monitoring (stretch).md|What is monitoring? (stretch)]]

### Chapter 10: Third-Party Integrations & Services
- [[learning_notes/What are third-party services.md|What are third-party services?]]
- [[learning_notes/What is payment processing (stretch).md|What is payment processing? (stretch)]]

### Chapter 11: Specialized Development & AI
- [[learning_notes/What is a GenAI API.md|What is a GenAI API?]]
- [[learning_notes/What is prompt engineering.md|What is prompt engineering?]]
- [[learning_notes/What is an AI workflow.md|What is an AI workflow?]]
- [[learning_notes/What is an AI orchestration framework.md|What is an AI orchestration framework?]]
- [[learning_notes/What is an AI agent.md|What is an AI agent?]]
- [[learning_notes/What is context management.md|What is context management?]]

*(Realtime & mobile optional; observability, IaC and basic payments are now **required**.)*

---

## 9  Open Questions for You

1. **Rendering engine** – Manim? Three.js? Dalle-3 frame-by-frame?  
2. **Model choice** – Speed vs. quality trade-off? Cache strategy?  
3. **Pricing** – Free preview + pay-per-render? Education discount?  
4. **Quality guardrails** – Human review? Auto-evaluate script accuracy?  
5. **Accessibility** – Captions, audio-description track?

---

## 10  Out-of-Scope for First Release

- Mobile native app  
- Multi-tenant organisation plans  
- Marketplace of user-generated videos  
- Fine-tuning custom models (beyond default LLM options)

---

### 🚀 Your Next Step

1. Fork this repo, create a new branch `vidtutor-prd`.  
2. Replace all guidance text; commit your *own* PRD & research artefacts.  
3. Open a pull request and tag a mentor for feedback before you start coding.

Good luck—push the frontier! 🚀