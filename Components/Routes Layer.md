---
type: component
aliases: [Шар маршрутів, HTTP-шар]
tags: [component, layer]
layer: routes
source_path: src/routes/
status: planned
requirements: [FR-1, FR-2, FR-3, FR-4, FR-5, FR-6, FR-7, FR-8, NFR-6]
created: 2026-10-16
updated: 2026-10-16
depends_on: ["[[Services Layer]]", "[[Validation Schemas]]", "[[Error Handling]]"]
governed_by: ["[[ADR-001 Layered Architecture]]", "[[ADR-004 Zod as Single Source of Truth]]"]
sources: ["PR4: ontology relations"]
---

# Routes Layer

**Відповідальність:** HTTP-ендпоінти Fastify — прийом запиту, валідація через [[Validation Schemas]], виклик [[Services Layer]], формування відповіді.

## Правила
- Без бізнес-логіки й без доступу до [[Database]] — див. [[ADR-001 Layered Architecture]].
- Схеми ендпоінтів є джерелом OpenAPI-специфікації (NFR-6).
- Помилки — через [[Error Handling]].

## Заплановані групи ендпоінтів
- `/movies` — [[Movie]] (CRUD, пошук).
- `/watches` — [[Watch Entry]], [[Watch History]].
- `/profile` — [[Preference Profile]].
- `/recommendations` — [[Recommendation]].
- `/stats` — [[Viewing Statistics]].
