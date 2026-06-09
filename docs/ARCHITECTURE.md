# PermitReady — Contractor Prescreening SaaS Architecture

**Repository:** [github.com/Jaeger-JG/PermitReady](https://github.com/Jaeger-JG/PermitReady)  
**Working name:** PermitReady  
**Product model:** Standalone B2B SaaS — prescreening only (not a permit system of record)  
**Primary users:** General contractors, ADU builders, permit expediters/runners  
**Pilot jurisdiction:** City of Vallejo, CA — **ADU Building Permit only** (30-day scope)  
**Code scope:** Completeness/submittal requirements only for pilot; code compliance AI deferred to Month 2+

---

## 0. 30-Day Pilot Beta — Feasibility & Sprint Plan

### Can it be ready in one month?

**Yes — for a pilot beta with 5–10 contractors testing Vallejo ADU completeness prescreen.**  
**No — for the full Phase 1 MVP** (OCR pipeline, Stripe, multiple permit types, PDF exports, AWS production infra, code compliance RAG).

With a **solo founder + AI-assisted development (Cursor)**, a focused pilot is realistic if you aggressively cut scope and simplify the stack.

### What "Ready" Means (Day 30)

| Included in pilot | Deferred post-pilot |
|-------------------|-------------------|
| Invite-only contractor signup (Clerk) | Public self-serve + Stripe billing |
| Vallejo only, ADU permit type only | City picker with multiple jurisdictions |
| Upload PDFs by document type (plans, calcs, energy, site plan) | DWG/CAD conversion |
| Completeness prescreen (required docs + sheet index via PDF text + LLM) | Textract OCR, custom CV |
| Readiness report with checklist + fix guidance | PDF export, shareable links |
| Re-upload + re-screen | Version diff / incremental processing |
| Deployed on Vercel (production URL) | AWS ECS / Terraform |
| "Not city approval" disclaimer | Code compliance RAG |

### Stack Simplification (Solo 30-Day)

| Layer | 30-Day Choice | Why |
|-------|---------------|-----|
| App | **Next.js 15** (App Router, API routes) | One codebase; no separate NestJS service |
| DB | **Neon PostgreSQL** + Prisma | Managed, free tier, no RDS ops |
| Auth | **Clerk** | Fastest auth for solo |
| Storage | **Vercel Blob** or Supabase Storage | No S3 presign/IAM setup |
| Background jobs | **Inngest** or Next.js `after()` + queue table | Async PDF processing without Redis/ECS |
| PDF text | **pdf-parse** + LLM | Skip Textract for pilot |
| Prescreen logic | **Deterministic rules + 1 LLM call** | Check doc slots; LLM parses sheet index from extracted text |
| Deploy | **Vercel** | Push-to-deploy; no Terraform |
| Monorepo | **Deferred** — single `permitready/` app | Add Turborepo when you hire |

See [SPRINT-30-DAY.md](SPRINT-30-DAY.md) for the week-by-week breakdown.

---

## 1. Executive Summary

### Vision

PermitReady helps **contractors and permit runners submit correct permit packages the first time**. Before applying through a city's official portal (Accela, OpenCounter, paper, etc.), the user selects the target city, picks the exact permit type that city offers, uploads their plans and supporting documents, and receives an AI-powered **readiness report** against that jurisdiction's requirements.

**Example flow:** A contractor has an ADU job at 123 Main St in Vallejo. They open PermitReady → select **City of Vallejo** → select **ADU Building Permit** → upload architectural plans, structural calcs, Title 24 energy report, and site plan → receive a prescreen score with missing sheets, stamp issues, and code flags → fix and re-screen until **Ready to Apply** → then submit to Vallejo's official system with confidence.

### Strategic Goals

| Goal | Mechanism |
|------|-----------|
| First-time application success | City-specific required-doc matrix + completeness checks before city submission |
| Reduce city rework cycles | Catch missing sheets, wrong permit type, unsigned plans early |
| Contractor time savings | Instant prescreen (minutes, not weeks of correction letters) |
| Jurisdiction accuracy | Per-city permit catalog + local checklist ingestion (Vallejo first) |
| Trust & transparency | Separate rule-based vs. AI findings; clear "not a city approval" disclaimer |

### What This Product Is NOT

- Not a city permit management system (no reviewer queues, no official approvals)
- Not a replacement for Accela/OpenGov/EnerGov city portals
- Not legal/code approval — prescreening is **decision support** for the contractor

### Differentiation vs. Cembla

Cembla sells primarily to **governments** as a pre-screen gate before staff review. PermitReady sells to **contractors** as a self-service tool they use **before** touching the city portal — no city procurement required, faster GTM, subscription-based.

---

## 2. Core Modules

### 2.1 Contractor Portal (Primary Product)

| Feature | Implementation |
|---------|----------------|
| Account creation | Clerk auth; individual or company accounts |
| City selection | Searchable jurisdiction picker; Vallejo pilot |
| Permit type selection | Dynamic list from `JurisdictionPermitType` |
| Job creation | Job = address + APN (optional) + city + permit type |
| Document upload | PDF primary; Vercel Blob for pilot |
| Prescreen trigger | One-click "Run Prescreen" |
| Readiness report | Score, checklist, violations, fix guidance |
| Re-screen loop | Upload revised docs → re-check → version history |
| Ready to Apply badge | Threshold-based (e.g., ≥85% completeness, 0 critical errors) |
| City submission link | Deep link to Vallejo's official permit portal |

### 2.2 Jurisdiction Catalog

Structured knowledge of what each city requires — the product's core moat. See [jurisdiction pack example](jurisdiction-packs/vallejo-ca/README.md).

### 2.3 Document Processing Engine

**Pilot:** PDF upload → text extraction (`pdf-parse`) → sheet index parsing → LLM-assisted classification.

**Post-pilot:** AWS Textract, custom CV sheet classifiers.

### 2.4 Prescreen Engine

1. **Completeness Checking** (MVP) — required sheets, docs, stamps, title block fields
2. **Code & Compliance Checking** (Phase 2) — ADU setbacks, egress, Vallejo zoning

**Output:** `PrescreenResult` with `readinessScore`, `completeness`, `codeFindings`, `fixGuidance`, `readyToApply`.

### 2.5 Out of Scope (MVP)

- Review Dashboard (city staff)
- Plan Markup Engine
- Official permit submission to city
- Multi-department routing

---

## 3. AI Architecture

### LLM Model Assignment

| Task | Primary Model |
|------|---------------|
| Structured JSON prescreen output | GPT-5 |
| Prescreen finding explanations | Claude 4.6 |
| Sheet classification (Phase 2) | Gemini 2.5 Pro |
| Code citation from RAG | GPT-5 |
| Permit wizard / help chat | Gemini 2.5 Flash |

### RAG System (Phase 2+)

- California Residential Code, Building Code, ADU state law
- City of Vallejo municipal code + submittal checklists
- pgvector in PostgreSQL; hybrid retrieval post-pilot

### Rule Engine vs. AI

- **Rule engine:** Numeric thresholds, required sheet matching, document presence
- **AI reasoning:** Architectural notes, ambiguous scope, exception interpretation
- Every finding labeled `rule_engine` vs. `ai_inference`

---

## 4. System Architecture

### Pilot Stack

Next.js 15, Neon PostgreSQL, Prisma, Clerk, Vercel Blob, Vercel deploy.

### Production Stack (Post-Pilot)

Next.js, NestJS, PostgreSQL + pgvector, Redis + BullMQ, AWS S3, Stripe, AWS ECS.

---

## 5. Database Design

**Core tables:** `contractor_orgs`, `users`, `jurisdictions`, `jurisdiction_permit_types`, `jobs`, `documents`, `document_versions`, `plan_sheets`, `prescreen_runs`, `prescreen_findings`, `code_embeddings`.

**Multi-tenancy:** Contractor data isolated by `org_id` (RLS). Jurisdiction packs are shared platform content.

---

## 6. API Design

**Base URL:** `https://api.permitready.com/v1`

Key endpoint groups:

- `GET /jurisdictions` — list cities
- `POST /jobs` — create prescreen job
- `POST /jobs/:id/documents` — upload documents
- `POST /jobs/:id/prescreen` — run prescreen (async)
- `GET /jobs/:id/prescreen` — latest readiness report

---

## 7. Multi-Tenant SaaS Design

- **Customer tenant:** ContractorOrg (pays subscription, owns jobs)
- **Platform content:** Jurisdiction packs (curated by PermitReady)
- **Roles:** Owner, Member, Internal Admin

### Business Model (Post-Pilot)

| Tier | Price | Includes |
|------|-------|----------|
| Starter | $49/mo | 5 prescreens/mo, Vallejo |
| Pro | $149/mo | 25 prescreens/mo, CA cities |
| Per-run | $15 | Pay-as-you-go |

---

## 8. Development Roadmap

| Phase | Timeline | Focus |
|-------|----------|-------|
| **0 — Pilot** | 30 days | Vallejo ADU completeness prescreen |
| **1 — MVP** | Months 2–4 | Stripe, PDF export, multi permit types, Textract |
| **2 — AI Code** | Months 4–6 | RAG, rule engine, Bay Area cities |
| **3 — CV + Scale** | Months 7–12 | Sheet classifier, 50+ CA jurisdictions |
| **4 — Extensions** | Months 13–18 | City partnerships, PMS integrations |

---

## 9. Competitive Analysis

| Dimension | Cembla | PermitReady |
|-----------|--------|-------------|
| Buyer | City government | Contractor / permit runner |
| When used | Before staff review | Before city portal submission |
| Sales cycle | Gov procurement | Self-serve signup |
| Weakness | Contractors can't buy directly | — |

---

## 10. Monorepo Structure (Post-Pilot)

```
permitready/
├── apps/web/              # Next.js contractor portal
├── apps/api/              # NestJS API (Phase 1+)
├── apps/worker-doc/       # Document processing
├── apps/worker-prescreen/ # AI prescreen (Phase 2)
├── packages/database/     # Prisma schema
├── packages/jurisdiction-packs/
└── infrastructure/terraform/
```

**Pilot:** Single Next.js app only — no monorepo until post-pilot.

---

## Implementation Priorities (30-Day Pilot)

1. Single Next.js app + Prisma + Neon
2. Clerk auth with invite-only access
3. Vallejo ADU jurisdiction pack
4. Job creation flow
5. Vercel Blob upload (4 document-type slots)
6. PDF text extraction + LLM sheet-index check
7. Completeness prescreen + readiness report UI
8. Vercel deploy + onboard 5 pilot contractors

---

## Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Vallejo requirements change | Versioned jurisdiction packs |
| AI hallucinated code citations | RAG-only citations; label AI vs. rule findings |
| Contractors expect city approval | Prominent disclaimer on every report |
| Jurisdiction pack accuracy | Manual QA; pilot with 5 contractors |
