---
type: component
aliases: [Шар сервісів, Бізнес-логіка]
tags: [component, layer]
layer: services
source_path: src/services/
status: planned
requirements: [FR-2, FR-3, FR-4, FR-6, FR-8]
created: 2026-10-16
updated: 2026-10-16
depends_on: ["[[Repositories Layer]]", "[[Recommender]]"]
implements: ["[[Watch History]]", "[[Preference Profile]]", "[[Viewing Statistics]]"]
governed_by: ["[[ADR-001 Layered Architecture]]", "[[ADR-009 One Watch Entry Per Movie Kept on Delete]]"]
sources: ["PR4: ontology relations", github:movie-tracker@6344406]
---

# Services Layer

**Відповідальність:** бізнес-логіка застосунку. Викликається з [[Routes Layer]], звертається до даних лише через [[Repositories Layer]].

## Заплановані сервіси
- MovieService — каталог [[Movie]].
- WatchService — [[Watch Entry]], [[Rating]], [[Watch History]].
- ProfileService — обчислення [[Preference Profile]].
- StatsService — [[Viewing Statistics]].
- Рекомендації делегуються [[Recommender]].

## Правила
- Не залежить від Fastify (тестується без HTTP) — [[ADR-001 Layered Architecture]].
