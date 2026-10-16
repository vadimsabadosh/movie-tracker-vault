---
type: component
aliases: [Шар репозиторіїв, Доступ до даних]
tags: [component, layer]
layer: repositories
source_path: src/repositories/
status: planned
requirements: [FR-1, FR-4, FR-5, FR-8, NFR-1, NFR-3]
created: 2026-10-16
updated: 2026-10-16
depends_on: ["[[Database]]"]
governed_by: ["[[ADR-001 Layered Architecture]]"]
sources: ["PR4: ontology relations"]
---

# Repositories Layer

**Відповідальність:** єдиний шар, що виконує SQL-запити до [[Database]].

## Правила
- Лише параметризовані запити (NFR-3).
- Фільтри, сортування й [[Pagination]] виконуються в SQL, а не в пам'яті (NFR-1).
- Інші шари не звертаються до БД в обхід репозиторіїв — [[ADR-001 Layered Architecture]].
