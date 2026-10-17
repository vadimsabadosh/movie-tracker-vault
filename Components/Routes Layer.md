---
type: component
aliases: [Шар маршрутів, HTTP-шар]
tags: [component, layer]
layer: routes
source_path: src/routes/
status: implemented
requirements: [FR-1, FR-2, FR-3, FR-4, FR-5, FR-6, FR-7, FR-8, NFR-6]
created: 2026-10-16
updated: 2026-10-17
depends_on: ["[[Services Layer]]", "[[Validation Schemas]]", "[[Error Handling]]"]
governed_by: ["[[ADR-001 Layered Architecture]]", "[[ADR-004 Zod as Single Source of Truth]]", "[[ADR-010 Watch History Query Contract]]"]
sources: ["PR4: ontology relations", github:movie-tracker@2fce061, "PR7: FR-6–FR-8 orchestrations"]
---

# Routes Layer

**Відповідальність:** HTTP-ендпоінти Fastify — прийом запиту, валідація через [[Validation Schemas]], виклик [[Services Layer]], формування відповіді.

## Правила
- Без бізнес-логіки й без доступу до [[Database]] — див. [[ADR-001 Layered Architecture]].
- Схеми ендпоінтів є джерелом OpenAPI-специфікації (NFR-6, `/openapi.json`).
- Помилки — через [[Error Handling]].

## Групи ендпоінтів
- `/movies` — [[Movie]] (CRUD, пошук); `/movies/:id/watch` — [[Watch Entry]].
- `/watches` — [[Watch History]] ([[ADR-010 Watch History Query Contract]]).
- `/profile` — [[Preference Profile]] ([[ADR-013 Preference Profile Contract]]).
- `/recommendations` — [[Recommendation]] ([[ADR-014 Recommendation Score Formula]]).
- `/stats` — [[Viewing Statistics]] ([[ADR-015 Viewing Statistics Contract]]).
- `/health`.
