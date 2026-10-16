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
governed_by: ["[[ADR-001 Layered Architecture]]", "[[ADR-008 Unicode Case-Insensitive Keys in App Layer]]", "[[ADR-011 Sort Selection by Bound Parameters]]"]
sources: ["PR4: ontology relations", github:movie-tracker@c239213, github:movie-tracker@2fce061]
---

# Repositories Layer

**Відповідальність:** єдиний шар, що виконує SQL-запити до [[Database]].

## Правила
- Лише параметризовані запити (NFR-3).
- Фільтри, сортування й [[Pagination]] виконуються в SQL, а не в пам'яті (NFR-1).
- Інші шари не звертаються до БД в обхід репозиторіїв — [[ADR-001 Layered Architecture]].
