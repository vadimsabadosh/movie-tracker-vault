---
type: decision
id: ADR-014
aliases: [Формула скору рекомендацій]
tags: [decision, adr, recommendation, api]
status: accepted
date: 2026-10-17
supersedes: []
superseded_by: []
requirements: [FR-7, NFR-1, NFR-5, NFR-6]
sources: ["PR7: FR-7 orchestration (docs/orchestration/recommendations/)"]
created: 2026-10-17
updated: 2026-10-17
---

# ADR-014 Recommendation Score Formula

## Контекст
[[ADR-003 Deterministic Recommender Behind Interface]] зафіксував, що [[Recommender]] — інтерфейс, а базова реалізація рахує [[Recommendation Score]] з [[Preference Profile]] за фіксованою формулою, рівні скори — за id фільму, ваги фіксуються в коді й тестах під час реалізації. Самої формули vault не мав. Вхід — профіль за [[ADR-013 Preference Profile Contract]]: для кожного [[Genre]] і [[Director]] — `count` і `averageRating` (2 знаки). Рішення прийнято в оркестрації Planner → Coder → Reviewer (`docs/orchestration/recommendations/`); формулу, фільтр, шаблон пояснення й ліміт обрала людина (Q1–Q4).

## Рішення
- **Спорідненість** елемента профілю (згладжене середнє з однією уявною нейтральною оцінкою 5.5): `affinity = count × (averageRating − 5.5) / (count + 1)`.
- **Скор** фільму: `score = round2(2 × affinity(режисер) + Σ 1 × affinity(жанр))` — лише ті жанри й режисер, що є в профілі; константи `NEUTRAL_RATING = 5.5`, `GENRE_WEIGHT = 1`, `DIRECTOR_WEIGHT = 2` (`src/recommendation/profile-score-recommender.ts`). `round2(x) = Math.round(x·100)/100` — половини округлюються в бік +∞.
- **Кандидати** — лише непереглянуті [[Movie]] (`MovieRepository.listUnwatched()`); повертаються лише `score > 0` (за округленим значенням). Порожня історія → `{ items: [] }`.
- **Порядок:** округлений `score` спадно → `movie.id` зростаюче; потім `limit` (типово 20, 1–100).
- **Причини** (`reasons`): `{ kind: director|genre, name, count, averageRating, points }`, де `points = round2(вага × affinity)`; включно з негативними; порядок — points спадно → режисер перед жанром → порядок жанрів фільму.
- **Пояснення** (англійською, з тих самих причин): `Director Nolan: 2 watched, average 9.0 (+4.67); Genre Drama: 4 watched, average 8.5 (+2.40)`.
- **Збіг:** жанри — за `genres.name` (без регістру завдяки [[ADR-008 Unicode Case-Insensitive Keys in App Layer]]); режисери — за точним рядком, як у ADR-013.
- Ендпоінт `GET /recommendations` ([[Routes Layer]]) → `RecommendationService` ([[Services Layer]]) → `Recommender.recommend(profile, candidates, limit)`. Інша реалізація підставляється третім параметром `createServices(db, today, recommender)`.

## Альтернативи
- **`count × avg`** — вигадана формула (відхилена ще в ADR-013), до того ж винагороджує часто переглянуті, але нелюбі жанри.
- **Просте середнє** — один фільм з оцінкою 10 переважає стабільні 8.5 на чотирьох.
- **Бали за позицією в профілі** — ігнорують, наскільки улюблене краще, і зсуваються від сторонніх даних.
- **Середнє по жанрах фільму замість суми** — карає фільм за невідомі жанри.
- **Вага режисера 1 або сильніше згладжування `(count + 2)`** — розглянуті людиною, не обрані.
- **Показувати фільми зі скором ≤ 0** — пояснення «немає збігу» не є рекомендацією.
- **Пояснення українською** (як приклад у [[Recommendation]]) — решта тексту API англійською.
- **Сортування за неокругленим скором** — візуально рівні скори впорядковувались би за невидимим шумом float.

## Наслідки
- Тести можуть перевіряти точний порядок і рядки пояснень (NFR-5): `test/recommender.test.ts` (чисті функції, константи, tie «режисер перед жанром»), `test/recommendations.test.ts` (фікстура: id `[10, 12, 11, 16, 17, 13]`, скори `[9.4, 2.4, 2.33, 2.33, 2.25, 1.73]`, підміна Recommender), `test/recommendations.perf.test.ts` (NFR-1: 6 667 кандидатів, p95 < 200 мс).
- Будь-яка зміна формули — W5 і новий ADR, що замінить цей.
- Через `Math.round` малі від'ємні бали, що округлюються до −0, показуються як `(+0.00)`; детерміновано, але половини несиметричні (−0.665 → −0.66).
- Рекомендації успадковують чутливий до регістру збіг режисерів (ADR-013): «nolan» не збігається з «Nolan», доки немає `director_key`.
