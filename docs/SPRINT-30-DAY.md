# 30-Day Pilot Beta Sprint

**Goal:** Invite-only Vallejo ADU completeness prescreen for 5–10 contractors.  
**Builder:** Solo founder + AI-assisted development (Cursor).

## Stack (Pilot)

| Layer | Choice |
|-------|--------|
| App | Next.js 15 (App Router + API routes) |
| DB | Neon PostgreSQL + Prisma |
| Auth | Clerk (invite-only) |
| Storage | Vercel Blob |
| PDF text | pdf-parse + LLM |
| Deploy | Vercel |

## Week-by-Week

### Week 1 — Foundation
- Next.js + Tailwind + shadcn/ui scaffold
- Clerk auth (email + Google)
- Prisma schema: users, jobs, documents, prescreen_runs, prescreen_findings
- Seed Vallejo ADU jurisdiction pack
- Job creation flow (city/permit hardcoded to Vallejo ADU)

### Week 2 — Uploads
- Vercel Blob upload by document type (plans, calcs, energy, site plan)
- Background job: extract text from PDFs
- Job detail page + dashboard

### Week 3 — Prescreen
- Completeness engine (required docs + LLM sheet-index check)
- Readiness score + findings with fix guidance
- Re-upload + re-screen flow

### Week 4 — Pilot Launch
- Vercel production deploy
- Invite-only access + legal disclaimer
- Onboard 5 contractors; fix critical bugs

## Success Criteria (Day 30)

- [ ] Contractor can create a Vallejo ADU job and upload 4 document types
- [ ] Prescreen completes in under 3 minutes
- [ ] Report shows missing docs/sheets with actionable fix text
- [ ] Re-screen after re-upload shows improved score
- [ ] 5 contractors complete at least 1 full prescreen cycle
- [ ] Zero critical bugs blocking core flow

## Effort Estimate

~95–120 hours total (~25–30h/week full-time).
