---
type: glossary
aliases: [Ідентифікатор вимоги, FR, NFR]
tags: [glossary]
sources: [github:movie-tracker@dc15f72]
created: 2026-10-16
updated: 2026-10-16
---

# Requirement ID

**Визначення:** стабільний ідентифікатор вимоги з `docs/app-requirements.md` (ПР № 1): `FR-N` — функціональна, `NFR-N` — нефункціональна.

**У проєкті:**
- FR-1…FR-8 — каталог, перегляди й оцінки, історія, пошук, профіль, рекомендації, статистика.
- NFR-1…NFR-7 — продуктивність, валідація Zod, безпека, якість коду, детермінованість, OpenAPI, локальний запуск.
- Нотатки посилаються на вимоги полем `requirements` у frontmatter; [[Vault MCP Server]] відхиляє ID не за цим форматом.
- Таблиця відповідності вимогам — у [[MOC - movie-tracker]].

**Див. також:** [[Recommender]] (FR-7, NFR-5), [[Validation Schemas]] (NFR-2)
