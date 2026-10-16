---
type: component
aliases: [MCP-сервер vault, Конектор бази знань]
tags: [component, harness, mcp]
status: implemented
layer: harness
source_path: harness/vault-mcp/
sources: [github:movie-tracker@43a0aa6]
created: 2026-10-16
updated: 2026-10-16
---

# Vault MCP Server

**Відповідальність:** єдиний канал між агентом і цим vault ([[MCP]], stdio). Реалізує рішення [[ADR-005 Separate Vault Repository with MCP Access]].

## Інструменти
| Інструмент | Тип | Що робить |
|------------|-----|-----------|
| `ingest_note` | ingestion | Створює чи оновлює нотатку за конвенціями vault, додає нову в [[MOC]], комітить і пушить |
| `get_note` | retrieval | Нотатка за назвою чи alias: frontmatter, тіло, backlinks |
| `search_notes` | retrieval | Пошук за ключовими словами з вагами (назва, aliases, теги, тіло) |

## Цикл ingestion
1. Валідація входу (назва, тип, ID вимог) — помилка не змінює репозиторій.
2. `git pull --ff-only`.
3. Пошук нотатки за назвою без урахування регістру.
4. Формування frontmatter і тіла; списки об'єднуються як множини.
5. Без змін → `unchanged`, без коміту; інакше запис і додавання в MOC.
6. `git commit` лише своїх файлів → `git push`.

## Правила
- Vault — сусідній клон `../movie-tracker-vault` (змінна `VAULT_PATH`), не тека застосунку.
- Нотатки через конектор не видаляються.
- Не плутати з [[Recommender]]: це частина [[Agent Harness]], не застосунку.

## Код
Node.js 24, `@modelcontextprotocol/sdk`, `zod`, `yaml`. Модулі: `server.ts`, `vault.ts`, `notes.ts`, `git.ts`; 13 тестів на тимчасовому vault.
