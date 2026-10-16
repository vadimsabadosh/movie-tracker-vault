---
type: decision
id: ADR-010
aliases: [Контракт запиту історії переглядів]
tags: [decision, adr, api]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
requirements: [FR-4, NFR-2, NFR-6]
sources: [github:movie-tracker@2fce061]
created: 2026-10-16
updated: 2026-10-16
---

# ADR-010 Watch History Query Contract

## Контекст
FR-4 вимагає [[Watch History]] з фільтрами за [[Genre]], роком випуску, діапазоном [[Rating]] і періодом перегляду, із сортуванням і [[Pagination]]. Імена параметрів, межі діапазонів, поведінка на помилкових діапазонах і форма відповіді — публічний контракт API (NFR-6): клієнти на нього покладаються, змінити його дорого. Рішення прийнято в оркестрації Planner → Coder → Reviewer (ПР № 6, `docs/orchestration/watch-history/`); відкриті питання плану вирішила людина.

## Рішення
- **Ендпоінт** — наявний `GET /watches` ([[Routes Layer]]), без окремого `/history`.
- **Параметри** (усі необов'язкові): `genre` (один жанр, 1–50 символів), `yearFrom`/`yearTo` (рік випуску фільму, 1888…наступний рік), `ratingMin`/`ratingMax` (1–10), `watchedFrom`/`watchedTo` (дата `YYYY-MM-DD`), `sort` = `watchedAt` (типово) | `rating` | `year` | `title`, `order` = `asc` | `desc`, `limit` 1–100 (типово 20), `offset` ≥ 0.
- **Діапазони включні.** Точний рік — `yearFrom=yearTo`.
- **Перевернутий діапазон** (`ratingMin > ratingMax` тощо) — 400 `VALIDATION_ERROR`, `details[0].path` вказує на верхню межу.
- **Жанр** порівнюється через `name_key` без урахування регістру, також кирилиця ([[ADR-008 Unicode Case-Insensitive Keys in App Layer]]). Невідомий жанр — 200 з порожнім списком, а не 404.
- **Типовий напрям залежить від поля:** `title` → `asc`, решта → `desc` (за замовчуванням — найновіші перегляди першими, як і до FR-4).
- **Стабільний порядок** (tie-break): `watchedAt` → `movieId`; `rating`/`year` → `watchedAt desc`, `movieId`; `title` → рік випуску, `movieId`.
- **Елемент відповіді** містить фільм: `{movieId, watchedAt, rating, note, movie: {id, title, year, genres, director}}` — поле додано, наявні поля не змінено.
- `total` — кількість записів після фільтрів, без `DISTINCT` (один запис на фільм, [[ADR-009 One Watch Entry Per Movie Kept on Delete]]).

## Альтернативи
- **Окремий `GET /history`** — дублює список; завдання FR-4 стосується `/watches`.
- **Один `year` замість діапазону** — діапазон покриває обидва випадки.
- **Кілька жанрів** (`genre=a&genre=b`) — семантика AND/OR не визначена у vault; додати пізніше можна без зламу контракту.
- **Порожній результат на перевернутий діапазон** — приховує помилку клієнта.
- **Елемент без фільму** — клієнт робив би N запитів `/movies/:id`, щоб показати назви, за якими щойно фільтрував.

## Наслідки
- Схеми: `WatchListQuery`, `WatchHistoryItem`, `WatchSort`, `SortOrder` у `src/schemas/watch.ts` (Zod, [[ADR-004 Zod as Single Source of Truth]]); OpenAPI показує всі 11 параметрів.
- Розширення (кілька жанрів, фільтр за режисером) — нові необов'язкові параметри; зміна наявних — новий ADR.
- Сортування реалізовано без складання SQL — [[ADR-011 Sort Selection by Bound Parameters]].
- Перевіряють тести `test/watches.test.ts` («watch history (FR-4)»), `test/app.test.ts` (11 параметрів в OpenAPI), `test/watch-history.perf.test.ts` (NFR-1).
