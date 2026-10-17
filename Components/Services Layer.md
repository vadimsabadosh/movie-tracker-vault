---
type: component
aliases: [Шар сервісів, Бізнес-логіка]
tags: [component, layer]
layer: services
source_path: src/services/
status: implemented
requirements: [FR-2, FR-3, FR-4, FR-6, FR-8]
created: 2026-10-16
updated: 2026-10-17
depends_on: ["[[Repositories Layer]]", "[[Recommender]]"]
implements: ["[[Watch History]]", "[[Preference Profile]]", "[[Viewing Statistics]]"]
governed_by: ["[[ADR-001 Layered Architecture]]", "[[ADR-009 One Watch Entry Per Movie Kept on Delete]]"]
sources: ["PR4: ontology relations", github:movie-tracker@6344406, "PR7: FR-6–FR-8 orchestrations"]
---

# Services Layer

**Відповідальність:** бізнес-логіка застосунку. Викликається з [[Routes Layer]], звертається до даних лише через [[Repositories Layer]].

## Сервіси
- MovieService — каталог [[Movie]].
- WatchService — [[Watch Entry]], [[Rating]], [[Watch History]].
- ProfileService — обчислення [[Preference Profile]] ([[ADR-013 Preference Profile Contract]]); точка входу для рекомендацій.
- RecommendationService — бере профіль і непереглянуті фільми, делегує [[Recommender]] ([[ADR-014 Recommendation Score Formula]]).
- StatsService — [[Viewing Statistics]] ([[ADR-015 Viewing Statistics Contract]]).
- Усі збираються в `src/container.ts` (`createServices`); інший Recommender підставляється третім параметром.

## Правила
- Не залежить від Fastify (тестується без HTTP) — [[ADR-001 Layered Architecture]].
