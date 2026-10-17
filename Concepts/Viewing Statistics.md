---
type: concept
aliases: [Статистика переглядів]
tags: [concept, domain, statistics]
requirements: [FR-8]
created: 2026-10-16
updated: 2026-10-17
governed_by: ["[[ADR-015 Viewing Statistics Contract]]"]
sources: ["PR7: FR-8 orchestration (docs/orchestration/viewing-statistics/)"]
---

# Viewing Statistics

**Визначення:** зведені показники за [[Watch History]].

## Показники (FR-8)
- Кількість переглянутих фільмів.
- Середня [[Rating]].
- Розподіл за [[Genre]] і за роками.

## Реалізація
- Агрегатні SQL-запити в [[Repositories Layer]], оркестрація в [[Services Layer]].
