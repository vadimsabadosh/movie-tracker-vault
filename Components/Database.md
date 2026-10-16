---
type: component
aliases: [База даних, SQLite]
tags: [component, infrastructure, data]
layer: infrastructure
source_path: src/db/
status: planned
requirements: [NFR-1, NFR-3, NFR-7]
created: 2026-10-16
updated: 2026-10-16
---

# Database

**Відповідальність:** зберігання каталогу й історії у файлі SQLite через better-sqlite3 — [[ADR-002 SQLite with better-sqlite3]].

## Заплановані таблиці
- `movies` — [[Movie]].
- `movie_genres` — зв'язок [[Movie]] ↔ [[Genre]].
- `watches` — [[Watch Entry]] (з [[Rating]] і нотаткою).

## Правила
- Доступ лише через [[Repositories Layer]].
- Зміна схеми — через міграції (потребує підтвердження людини, див. guardrails).
- Файл БД не комітиться в репозиторій.
