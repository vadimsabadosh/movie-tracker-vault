---
type: moc
aliases: [MOC-Застосунок, Головна]
tags: [moc]
created: 2026-10-16
updated: 2026-10-16
---

# MOC - movie-tracker

Точка входу в базу знань застосунку **movie-tracker** — REST API для особистої історії переглядів фільмів із профілем вподобань і детермінованими рекомендаціями.
Конвенції vault — у `README.md`.

## Concepts — предметна область
- Каталог: [[Movie]], [[Genre]], [[Director]]
- Історія: [[Watch Entry]], [[Rating]], [[Watch History]]
- Аналітика: [[Preference Profile]], [[Recommendation]], [[Viewing Statistics]]

## Components — кодова база
- Шари: [[Routes Layer]] → [[Services Layer]] → [[Repositories Layer]]
- Рекомендації: [[Recommender]]
- Інфраструктура: [[Database]], [[Validation Schemas]], [[Error Handling]]
- [[Vault MCP Server]]

## Decisions — ADR
- [[ADR-001 Layered Architecture]]
- [[ADR-002 SQLite with better-sqlite3]]
- [[ADR-003 Deterministic Recommender Behind Interface]]
- [[ADR-004 Zod as Single Source of Truth]]
- [[ADR-005 Separate Vault Repository with MCP Access]]
- [[ADR-006 Read-Only GitHub MCP as Data Source]]
- [[ADR-007 Typed Relations in Frontmatter]]
- [[ADR-008 Unicode Case-Insensitive Keys in App Layer]]
- [[ADR-009 One Watch Entry Per Movie Kept on Delete]]
- [[ADR-010 Watch History Query Contract]]

## Glossary
- Домен: [[Recommendation Score]], [[Pagination]]
- Harness: [[Agent Harness]], [[Guardrail]], [[MCP]], [[Skill]], [[ADR]], [[MOC]]
- [[Requirement ID]]

## Відповідність вимогам
| Вимога | Нотатки |
|--------|---------|
| FR-1 каталог | [[Movie]], [[Genre]], [[Director]] |
| FR-2, FR-3 перегляд і оцінка | [[Watch Entry]], [[Rating]] |
| FR-4 історія | [[Watch History]], [[Pagination]] |
| FR-5 пошук | [[Movie]] |
| FR-6 профіль | [[Preference Profile]] |
| FR-7 рекомендації | [[Recommendation]], [[Recommender]], [[Recommendation Score]] |
| FR-8 статистика | [[Viewing Statistics]] |
