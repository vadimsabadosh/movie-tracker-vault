---
type: decision
id: ADR-013
aliases: [Контракт профілю вподобань]
tags: [decision, adr, api, recommendation]
status: accepted
date: 2026-10-17
supersedes: []
superseded_by: []
requirements: [FR-6, FR-7, NFR-1, NFR-5, NFR-6]
sources: ["PR7: FR-6 orchestration (docs/orchestration/preference-profile/)"]
created: 2026-10-17
updated: 2026-10-17
---

# ADR-013 Preference Profile Contract

## Контекст
FR-6 вимагає [[Preference Profile]]: улюблені [[Genre]] «за кількістю та оцінками», улюблені [[Director]], середня [[Rating]] за жанром. Профіль — вхід для [[Recommender]] (FR-7), тому його порядок і точність — контракт, від якого залежить детермінованість рекомендацій (NFR-5, [[ADR-003 Deterministic Recommender Behind Interface]]). Vault не визначав формули «улюбленості», правила групування режисерів і точності середніх. Рішення прийнято в оркестрації Planner → Coder → Reviewer (`docs/orchestration/preference-profile/`); відкриті питання Q1–Q5 вирішила людина.

## Рішення
- **Ендпоінт** — `GET /profile` ([[Routes Layer]]), без параметрів. Обчислюється з [[Watch History]] на кожен запит, не зберігається.
- **Форма:** `{ genres: PreferenceItem[], directors: PreferenceItem[] }`, `PreferenceItem = { name, count, averageRating }` (Zod, `src/schemas/profile.ts`, [[ADR-004 Zod as Single Source of Truth]]). Списки повні: усі переглянуті жанри й режисери, без top-N.
- **Порядок (позиція = ранг):** `count` спадно → точне `AVG(rating)` спадно → назва. Для жанрів остання ланка — `name_key` (без регістру, [[ADR-008 Unicode Case-Insensitive Keys in App Layer]]); для режисерів — точний рядок `movies.director` у бінарному порядку (велика кирилиця перед малою). Асиметрія свідома.
- **Точність:** `averageRating = ROUND(AVG(rating), 2)` у SQL; порядок — за неокругленим значенням.
- **Групування:** жанри — за `genres.id` (показується перше написання); режисери — за точним рядком, без `director_key`.
- **Агрегація в SQL** ([[Repositories Layer]], `src/repositories/profile-repository.ts`): два фіксовані літеральні запити без параметрів ([[ADR-011 Sort Selection by Bound Parameters]]), `COUNT(*)` без `DISTINCT` ([[ADR-009 One Watch Entry Per Movie Kept on Delete]]).
- **Порожня історія** — 200 `{ genres: [], directors: [] }`. Підсумки (кількість, загальна середня) — не тут, а у [[Viewing Statistics]] (FR-8).
- **Точка повторного використання для FR-7** — `ProfileService.get()` ([[Services Layer]]), `services.profile` у контейнері.

## Альтернативи
- **Спершу середня оцінка, потім кількість** — один фільм з оцінкою 10 обходить двадцять з оцінкою 8.
- **Скор `count × avg`** (сума оцінок) — вигадана формула, якої немає у вимогах.
- **Tie-break за id** — залежить від порядку вставки: ті самі дані, внесені інакше, дали б інший профіль (порушує NFR-5).
- **Групування режисерів без регістру** (міграція `movies.director_key`) — узгоджено з ADR-008, але потребує зміни схеми (W3) і backfill; відкладено.
- **`?top=N`** — вигадує доменне правило; FR-7 потрібен повний профіль. Можна додати пізніше без зламу контракту.
- **Окремі поля `favouriteGenres` і мапа `averageRatingByGenre`** — дублюють дані, мапа не має порядку.
- **1 знак після коми** — втрачає точність для скорингу FR-7; пояснення FR-7 форматують до 1 знака самі.

## Наслідки
- FR-7 бере профіль із `ProfileService.get()` і може покладатися на порядок як на ранг.
- Режисери «Nolan» і «nolan» — два записи; порядок режисерів при рівності не враховує регістр так, як жанри. Перехід на `director_key` — нова міграція і ADR, що замінить цей пункт.
- NFR-1 виміряно: `test/profile.perf.test.ts` (10 000 переглядів, 20 жанрів, 500 режисерів, p95 < 200 мс).
- Перевіряють тести `test/profile.test.ts`: порядок на кожному рівні tie-break (також кирилиця «Комедія»/«драма»), округлення 7.67, одне написання жанру, детермінованість при іншому порядку вставки, рівність `services.profile.get()` і HTTP-відповіді; `test/app.test.ts` — `/profile` в OpenAPI.
