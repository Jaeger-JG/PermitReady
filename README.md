# PermitReady

[![Repository](https://img.shields.io/badge/GitHub-Jaeger--JG%2FPermitReady-blue)](https://github.com/Jaeger-JG/PermitReady)

AI-assisted building permit **prescreening** for contractors and permit runners.
Contractors select a city and permit type, upload their submittal documents, and receive a readiness report against that jurisdiction's requirements—so they apply correctly the first time.

## Build Timeline (120 Days)

- **Days 1–30:** Foundation + completeness prescreen
- **Days 31–60:** Textract OCR + blueprint sheet pipeline
- **Days 61–90:** Multimodal LLM vision + RAG code engine
- **Days 91–120:** Stripe, 5 Bay Area cities, public beta

**Blueprint strategy:** Phase 3-lite (Textract + LLM vision APIs, not custom model training)

- **Launch jurisdiction:** City of Vallejo, CA — ADU Building Permit
- **Users:** Contractors and permit expediters

## Documentation

| Document | Description |
|----------|-------------|
| [Architecture & Roadmap](docs/ARCHITECTURE.md) | Full technical architecture, AI design, API spec, database schema, and development roadmap |
| [120-Day Sprint Plan](docs/SPRINT-120-DAY.md) | Month-by-month blueprint-capable build plan |
| [Vallejo ADU Pack](docs/jurisdiction-packs/vallejo-ca/README.md) | Pilot jurisdiction requirement configuration |

## Product Vision

PermitReady is **not** a city permit management system. It is a standalone SaaS tool contractors use **before** submitting through a city's official portal (Accela, OpenCounter, etc.).

## Status

Architecture phase — implementation not yet started.

## License

Proprietary. All rights reserved.
