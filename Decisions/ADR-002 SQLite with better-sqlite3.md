---
type: decision
id: ADR-002
aliases: [Вибір SQLite]
tags: [decision, adr, data]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
created: 2026-10-16
---

# ADR-002 SQLite with better-sqlite3

## Контекст
NFR-7: запуск однією командою, без зовнішніх сервісів. Однокористувацький сценарій, каталог до 10 000 фільмів (NFR-1).

## Рішення
[[Database]] — файл SQLite, драйвер better-sqlite3 (синхронний API).

## Альтернативи
- PostgreSQL — потребує окремого сервера.
- JSON-файли — немає індексів і агрегатних запитів для [[Viewing Statistics]].

## Наслідки
- Тести створюють БД у пам'яті (`:memory:`) — швидко й ізольовано.
- Синхронний API спрощує код [[Repositories Layer]]; для одного користувача блокування не критичне.
