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
implements: ["[[ADR-002 SQLite with better-sqlite3]]"]
sources: ["PR4: ontology relations", github:movie-tracker@c239213, github:movie-tracker@6344406]
governed_by: ["[[ADR-008 Unicode Case-Insensitive Keys in App Layer]]", "[[ADR-009 One Watch Entry Per Movie Kept on Delete]]"]
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
