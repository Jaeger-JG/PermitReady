# 120-Day Blueprint-Capable Build

**Goal:** Solo founder ships Cembla-comparable PDF blueprint prescreening in 120 days.  
**Strategy:** Phase 3-lite — Textract + multimodal LLM vision (no custom model training).

## Feasibility (Solo)

| Doable in 120 days | Not doable in 120 days (solo) |
|--------------------|-------------------------------|
| Textract OCR on scanned blueprints | Custom fine-tuned floor plan models |
| Per-sheet LLM vision classification | Native DWG/DXF pipeline |
| Stamp/dimension reading via vision | 50+ jurisdictions |
| RAG + rule engine code checks | Bluebeam-class markup |
| 5 Bay Area cities, Vallejo ADU depth | Full egress path geometry |

## Stack

Next.js 15, Neon + Prisma + pgvector, Clerk, AWS S3, AWS Textract, Gemini 2.5 Pro vision, GPT-5, Inngest, Stripe, Vercel.

## Timeline

### Days 1–30: Foundation
- App, auth, job wizard, S3 upload, PDF.js viewer
- Completeness prescreen v1, readiness report, re-upload

### Days 31–60: Blueprint Pipeline
- PDF → per-page rasterization (300 DPI)
- AWS Textract OCR per sheet
- Inngest async pipeline, sheet metadata in DB

### Days 61–90: Vision + Code Engine
- Multimodal LLM per sheet (classify, stamps, dimensions)
- RAG over CRC/CBC/Vallejo MC
- Rule engine (ADU setbacks, sqft, egress)
- Combined completeness + code prescreen report

### Days 91–120: Launch
- Stripe billing, PDF report export
- 5 Bay Area cities, incremental re-screen
- Public beta, < 5 min prescreen for 20-sheet set

## Effort

~480–570 hours full-time (~40h/week × 12 weeks).

## Day 120 Success Criteria

- [ ] 20-sheet ADU plan set classified sheet-by-sheet
- [ ] Scanned blueprint OCR via Textract
- [ ] Stamp detection on cover sheet
- [ ] Setback dimensions read from site plan
- [ ] RAG-backed code citations in findings
- [ ] 5 cities in picker, Stripe live, 20+ contractor prescreens

## Post-Day 120

Custom CV models, DWG support, floor plan vectorization, 20+ cities.
