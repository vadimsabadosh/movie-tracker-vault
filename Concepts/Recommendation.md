---
type: concept
aliases: [Рекомендація]
tags: [concept, domain, recommendation]
requirements: [FR-7, NFR-5]
created: 2026-10-16
updated: 2026-10-16
---

# Recommendation

**Визначення:** непереглянутий [[Movie]] з каталогу, запропонований користувачу на основі [[Preference Profile]], разом зі скором і поясненням.

## Правила
- Лише фільми без [[Watch Entry]].
- Порядок — за [[Recommendation Score]]; рівні скори впорядковуються стабільно (NFR-5).
- Кожна рекомендація містить пояснення, напр. «жанр Drama — ваш улюблений (середня оцінка 8.4)».

## Реалізація
- [[Recommender]]; рішення — [[ADR-003 Deterministic Recommender Behind Interface]].
