---
type: concept
aliases: [Перегляд, Позначка перегляду]
tags: [concept, domain, history]
requirements: [FR-2, FR-3]
created: 2026-10-16
updated: 2026-10-16
part_of: ["[[Watch History]]"]
sources: ["PR4: ontology relations", github:movie-tracker@6344406]
governed_by: ["[[ADR-009 One Watch Entry Per Movie Kept on Delete]]"]
---

# Watch Entry

**Визначення:** факт того, що користувач переглянув конкретний [[Movie]]: дата перегляду, особиста [[Rating]] і необов'язкова нотатка.

## Атрибути
- `movie_id` — посилання на [[Movie]].
- `watched_at` — дата перегляду.
- `rating` — [[Rating]] (1–10).
- `note` — текстова нотатка (необов'язкова).

## Правила
- Позначку можна скасувати (FR-2) — тоді фільм знову стає кандидатом для [[Recommendation]].
- Сукупність усіх записів утворює [[Watch History]].
