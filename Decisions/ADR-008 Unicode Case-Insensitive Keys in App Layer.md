---
type: decision
id: ADR-008
aliases: [Ключі без урахування регістру]
tags: [decision, adr, data]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
requirements: [FR-1, FR-5, NFR-1]
sources: [github:movie-tracker@c239213]
created: 2026-10-16
updated: 2026-10-16
---

# ADR-008 Unicode Case-Insensitive Keys in App Layer

## Контекст
FR-5 вимагає пошуку [[Movie]] за частиною назви без урахування регістру, а каталог унікальний за парою (назва, рік) — теж без урахування регістру. Назви бувають українською. У SQLite ([[ADR-002 SQLite with better-sqlite3]]) `COLLATE NOCASE` і `LIKE` згортають регістр лише для ASCII: «Тіні» ≠ «тіні», тож дублікат кирилицею пройшов би, а пошук «ЗАБУТИХ» не знайшов би «Тіні забутих предків».

## Рішення
- Для кожного тексту, що порівнюється без регістру, зберігається окремий стовпець `*_key` — значення в нижньому регістрі, обчислене в [[Repositories Layer]] функцією `textKey()` (`trim().toLocaleLowerCase('uk')`, `src/repositories/movie-repository.ts`).
- Унікальність — обмеженням на ключі: `UNIQUE (title_key, year)` у `movies`, `name_key UNIQUE` у `genres` ([[Database]], міграція 1).
- Пошук — `instr(title_key, @q) > 0` з ключем запиту, а не `LIKE`: без екранування `%` і `_`.
- Відображається оригінальне написання (`title`, `name`); у жанрів зберігається перше написання.

## Альтернативи
- `COLLATE NOCASE` / `LIKE` — лише ASCII, кирилиця не згортається.
- Розширення ICU для SQLite (`icu_load_collation`) — потребує нативної збірки й ламає запуск однією командою (NFR-7).
- Власна функція SQLite (`db.function('lower_uk', …)`) у кожному запиті — індекс не використовується, повний перебір таблиці (NFR-1).

## Наслідки
- Кожне нове текстове поле з пошуком чи унікальністю отримує стовпець `*_key`, який репозиторій оновлює разом з оригіналом. Скіл `new-module` вимагає цього явно.
- Ключі та оригінали можуть розійтися лише при записі в обхід репозиторію, що заборонено [[ADR-001 Layered Architecture]].
- Пошук `instr` не використовує індекс. Для 10 000 фільмів це прийнятно (NFR-1); якщо ні — розглянути FTS5 з власним токенізатором.
- Перевіряють тести `test/movies.test.ts`: «returns 409 … Cyrillic included», «finds by part of the title … also for Cyrillic», «treats % and _ in the query literally».
