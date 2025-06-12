```md
# 04_PodBrain – AI-Powered Podcast Explorer  
_Scaffold PRD & Decision-Guide_

> **💡  Purpose of this document**  
> Project 4 is intentionally **open-ended**.  Below is a *scaffold PRD* that illustrates the minimum feature-set, architectural hints, and acceptance tests.  
> **You** (the learner) will **clone this file**, refine every section, and own the final technical decisions.

---

## 0. How to Use This Scaffold

1. Read each section and replace "👉 _fill me_" placeholders with your own details.  
2. Delete rows/features you will not build in your MVP; add new ones you deem essential.  
3. Commit your edited PRD to the root of your repo before coding begins.

---

## 1. Vision & Overview  (✏️ edit freely)
**PodBrain** ingests any podcast RSS feed, transcribes episodes, embeds the text, and lets users **semantic-search** across thousands of hours of audio.  Users can compile matching clips into shareable *supercuts*.

_Key value propositions_ (suggested):  
1. **Time-saving search** – jump directly to parts of an episode.  
2. **Learning & research** – surface diverse viewpoints on a topic.  
3. **Effortless remixing** – auto-stitch clips into one audio/video file.

---

## 2. Personas  (✏️ revise/add)

| Persona | Goals | Pains |
|---------|-------|-------|
| **Researcher** | Find all podcast moments discussing "CRISPR ethics" | Manual scrubbing is slow |
| **Edu-Creator** | Produce 3-min supercut for a lesson | Download/edit workflow is tedious |
| **Casual Listener** | Search episodes for topics of interest | Podcast apps don't support text search |
| **Course Learner (developer)** | Practise RAG, NoSQL, object storage, hosting | Choosing the right cloud stack |

---

## 3. Core User Stories  (✅ = keep, ❌ = cut, 🔄 = change wording)

| ID | User Story | Must-Have? |
|----|------------|-----------|
| U-01 | As a user I can **add an RSS feed** and trigger episode import | ✅ |
| U-02 | The system **transcribes new episodes automatically** | ✅ |
| U-03 | I can **search** by free-text and date range and get ranked clips | ✅ |
| U-04 | I can **select clips** to build a **supercut timeline** | ✅ |
| U-05 | I can **export** the supercut as a shareable link / MP3 / MP4 | 🔄 |
| U-06 | I can **save favourite queries** | ❌ |

---

## 4. MVP Feature Matrix

| Feature | Included in MVP? | Notes / Stretch Ideas |
|---------|------------------|-----------------------|
| RSS crawler & webhook for updates | ✅ | Cloud Function scheduled + on-demand |
| Speech-to-text transcription | ✅ | Gemini Speech API / Whisper via Cloud Run |
| Text chunking & Gemini embeddings | ✅ | Firestore + Vector index extension |
| Semantic search w/ Hybrid ranking | ✅ | Combine keyword & vector scores |
| Supercut builder UI | ✅ | Drag-to-reorder timeline |
| Media concatenation | ⬜ stretch | Use FFmpeg Cloud Run or client-side audio-element playlist |
| Authentication | ✅ | Firebase Auth (email / Google) |
| Billing / quotas | ⬜ stretch | Limit minutes transcribed per user |
| Observability & Alerts | ✅ | Basic logging, error tracking, and at least one alert configured (see Combined Structure Ch.9.6) |
| Payment Processing | ⬜ stretch | Add a minimal pay-per-transcription flow using Stripe or equivalent |
| IaC Deployment Script | ⬜ stretch | Provide a simple Terraform/Pulumi script that provisions at least one resource (e.g., Storage bucket) |
| Contract & Load Tests | ✅ | Include one mocked contract test for the RSS importer and a basic load test |

---

## 5. High-Level Architecture (suggestion)

```
          ┌────────────┐      Cloud Scheduler (cron)      ┌──────────────┐
  RSS  ──►│  FeedPull  ├───── trigger  /import/{feed} ──► │ Cloud Function│
          └────────────┘                                 └─────┬────────┘
                      New episode docs → Firestore  ───────►   │
                                                               ▼
                        Audio to Storage  ───────┐    Transcribe+Chunk
                                                 │    embed & store
                                                 ▼
                                    Vector Index (Firestore + Gemini)
                                                 │
                       Client search query ──────┴─►  Edge Function
```

* **Hosting**: Firebase Hosting (Next.js or React SPA)  
* **Data**: Firestore (`users`, `feeds`, `episodes`, `clips`, `supercuts`)  
* **Storage**: Cloud Storage buckets for raw audio & clip previews  
* **AI**: Gemini Speech-to-Text; Gemini Embedding API (via Vertex AI Extension)  
* **Edge Functions**: TypeScript; 5 MiB / <30 s limits—plan fall-back to Cloud Run for heavy jobs

> You may swap Firebase pieces for AWS, GCP, or a containerised Supabase + Qdrant if you prefer—justify the trade-off!

---

## 6. Minimal Data Model (Firestore collections)

```
users/{uid}
feeds/{feedId}
    meta: { title, imageUrl, rssUrl, lastChecked }
episodes/{epId}
    feedId, title, publishedAt, audioUrl
clips/{clipId}
    epId, startSec, endSec, text, embedding: [floats]
supercuts/{scId}
    ownerUid, title, createdAt, clipIds: [clipId], public: bool
```

_Add custom sub-collections or fields as needed._

---

## 7. Acceptance Tests (draft, edit!)

| Test ID | Success Criteria |
|---------|------------------|
| T-01 | POST `/api/feeds` with RSS URL stores feed & schedules crawler |
| T-02 | New episode triggers transcription; Firestore `clips` ≥ 1 |
| T-03 | `GET /api/search?q=protein folding` returns list sorted by relevance |
| T-04 | Creating a supercut with ≥ 2 clips stores `supercuts` doc & playable preview |
| T-05 | Observability dashboard shows error-free import run and raises alert on failure |
| T-06 | IaC script can be applied and destroyed without manual console steps |

---

## 8. Quick-Start Guide (editable)

1. `npx create-next-app@latest podbrain --ts`  
2. `firebase init` (Hosting, Functions, Firestore, Storage)  
3. Enable **Gen AI Extensions** in Firebase console (Speech, Embeddings).  
4. Copy `.env.example` → `.env.local` (`FIREBASE_API_KEY`, `PROJECT_ID`, etc.).  
5. Prompt Gemini / Replit agent:  
   > "Generate Firestore schema & search Edge Function for PodBrain per attached PRD."  
6. Deploy: `firebase deploy --only hosting,functions,extensions`.  

---

## 9. Focused Learning-Goal Subset

| Chapter | Key Items Practised |
|---------|--------------------|
| **2** Workflow & Tooling | Git branches, CI/CD via Firebase CLI, AI agents in Cursor |
| **3** Foundations | API routes, CRUD, JSON |
| **5** Front-End | React components, media player, forms |
| **6** Back-End | REST/Edge Functions, Webhooks for RSS updates |
| **7** Data | Firestore NoSQL, Object Storage, Embedding index |
| **8** Auth & Security | Firebase Auth, Rules, RBAC (read/write access) |
| **9** Infra & Deployment | Firebase Hosting, Cloud Functions, optional Docker |
| **10** Integrations | RSS parsing, Gemini APIs, FFmpeg (optional) |
| **11** AI Advanced | Embeddings, RAG search, summarisation pipeline |

---

## 10. Open Questions for *You* to Decide

1. **Embeddings store** – Firestore vector extension vs. external DB (Pinecone)?  
2. **Where to run heavy FFmpeg jobs?** Edge Functions vs. Cloud Run.  
3. **Clip sharing UX** – Public short-link, or download MP3/MP4?  
4. **Scaling transcriptions** – On-demand vs. pre-processing whole backlog.  
5. **Cost controls** – How will you cap minutes transcribed per user?

---

## 11. Out-of-Scope (for your first release)

- Live streaming of podcasts  
- Multi-tenant organisation plans  
- Mobile native app (PWA responsiveness encouraged)  
- Advanced subscription management (basic pay-per-use payment may be included)

---

### 🗝️  **Deliverable**

A runnable, deployed **PodBrain MVP** that ingests at least one public RSS feed, supports semantic search, and builds a playable supercut.  You will:

1. Finalise this PRD  
2. Commit it (`projects/04_PodBrain.md`)  
3. Implement & deploy  
4. Record a 2-minute demo video and list which learning goals you tackled

Happy building!
```