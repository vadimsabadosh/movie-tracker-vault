---
type: concept
aliases: [Фільм]
tags: [concept, domain, catalog]
requirements: [FR-1, FR-5]
created: 2026-10-16
updated: 2026-10-16
---

# Movie

**Визначення:** запис каталогу, що описує один фільм. Каталог — сукупність усіх фільмів, з якої будуються [[Watch History]] і [[Recommendation]].

## Атрибути
- `title` — назва (обов'язкова; пошук за частиною назви, FR-5).
- `year` — рік випуску.
- `genres` — один чи більше [[Genre]].
- `director` — [[Director]].

## Правила
- CRUD над каталогом (FR-1). Фільм може бути переглянутим ([[Watch Entry]]) або ні — непереглянуті є кандидатами для [[Recommendation]].

## Реалізація
- Зберігання: [[Repositories Layer]], таблиця в [[Database]].
- Валідація: [[Validation Schemas]].
