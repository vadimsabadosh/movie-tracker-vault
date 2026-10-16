---
type: concept
aliases: [Профіль вподобань]
tags: [concept, domain, recommendation]
requirements: [FR-6]
created: 2026-10-16
updated: 2026-10-16
---

# Preference Profile

**Визначення:** агреговане представлення смаків користувача, обчислене з [[Watch History]].

## Складові (FR-6)
- Улюблені [[Genre]] (за кількістю та оцінками).
- Улюблені [[Director]].
- Середня [[Rating]] за жанром.

## Використання
- Вхід для [[Recommender]], що формує [[Recommendation]].
- Профіль не зберігається окремо — обчислюється з історії (одне джерело правди).
