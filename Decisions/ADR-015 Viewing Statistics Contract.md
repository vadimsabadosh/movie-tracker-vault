---
type: decision
id: ADR-015
aliases: [Контракт статистики переглядів]
tags: [decision, adr, api, statistics]
status: accepted
date: 2026-10-17
supersedes: []
superseded_by: []
requirements: [FR-8, NFR-1, NFR-5, NFR-6]
sources: ["PR7: FR-8 orchestration (docs/orchestration/viewing-statistics/)"]
created: 2026-10-17
updated: 2026-10-17
---

# ADR-015 Viewing Statistics Contract

## Контекст
FR-8 вимагає [[Viewing Statistics]]: кількість переглянутих фільмів, середню [[Rating]], розподіл за [[Genre]] і «за роками». Неоднозначним було, що таке «рік» (випуску чи перегляду), як впорядкувати розподіли (NFR-5), що повертати для порожньої історії і як статистика стосується `/profile`, який уже рахує жанри ([[ADR-013 Preference Profile Contract]]). Рішення прийнято в оркестрації Planner → Coder → Reviewer (`docs/orchestration/viewing-statistics/`); Q1–Q5 вирішила людина.

## Рішення
- **Ендпоінт** — `GET /stats` ([[Routes Layer]]), без параметрів; обчислюється з [[Watch History]] на кожен запит, не зберігається.
- **Форма:** `{ watchedCount, averageRating, byGenre: [{ name, count }], byYear: [{ year, count }] }` (Zod, `src/schemas/stats.ts`).
- **«Рік» = рік випуску** фільму (`movies.year`) — те саме значення, що у фільтрах історії ([[ADR-010 Watch History Query Contract]]).
- **`averageRating`** = `ROUND(AVG(rating), 2)`; `null`, якщо нічого не переглянуто. Порожня історія — 200 `{ watchedCount: 0, averageRating: null, byGenre: [], byYear: [] }`.
- **Порядок:** `byGenre` — кількість спадно → `name_key` (без регістру, [[ADR-008 Unicode Case-Insensitive Keys in App Layer]]), показується перше написання; `byYear` — рік зростаюче, лише роки з переглядами.
- **Підрахунок:** рядки `watches` без `DISTINCT` ([[ADR-009 One Watch Entry Per Movie Kept on Delete]]); фільм рахується один раз у `byYear` і один раз у кожному своєму жанрі, тож сума `byGenre` може перевищувати `watchedCount`.
- **Незалежність від `/profile`:** власні три фіксовані SQL-запити в [[Repositories Layer]] ([[ADR-011 Sort Selection by Bound Parameters]]); `StatsService` ([[Services Layer]]) не використовує `ProfileService`, середні за жанром лишаються лише в `/profile`.
- Без відсотків і без фільтрів.

## Альтернативи
- **Рік перегляду** (`substr(watched_at, 1, 4)`) або обидва розподіли — дає «року» друге значення, якого ADR-010 уникнув; людина обрала рік випуску.
- **Будувати `byGenre` з `ProfileService`** — порядок містив би невидимий у `/stats` tie-break за середньою, а зміна контракту профілю мовчки змінювала б статистику.
- **`averageRating` у `byGenre`** — дублює `/profile`.
- **`0` або 404 для порожньої історії** — `0` поза шкалою 1–10, 404 — статистика є, просто нульова.
- **Мапи `{ "Drama": 3 }`** — без гарантованого порядку (NFR-5).
- **Заповнення пропущених років нулями** — довільні межі діапазону.
- **Відсотки** — клієнт рахує сам; частки жанрів не дають 100 %.

## Наслідки
- Фільтри (наприклад, період) можна додати пізніше як необов'язкові параметри без зламу контракту; зміна наявних полів — новий ADR.
- NFR-1 виміряно: `test/stats.perf.test.ts` (10 000 переглядів; також `watchEvery: 3`), без нових індексів і міграцій.
- Перевіряють тести `test/stats.test.ts`: порожня статистика, 8.5 і 7.67, кирилиця «драма»/«Комедія», злиття «Sci-Fi»/«sci-fi», рік випуску проти дати перегляду, детермінованість при іншому порядку вставки, рівність `services.stats.get()` і HTTP-відповіді; `test/app.test.ts` — чотири поля в OpenAPI.
