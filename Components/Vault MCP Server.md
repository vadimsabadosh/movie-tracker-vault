---
type: component
aliases: [MCP-сервер vault, Конектор бази знань]
tags: [component, harness, mcp]
status: implemented
layer: harness
source_path: harness/vault-mcp/
sources: [github:movie-tracker@43a0aa6, "PR4: ontology relations", github:movie-tracker@e802f51]
created: 2026-10-16
updated: 2026-10-17
implements: ["[[ADR-005 Separate Vault Repository with MCP Access]]", "[[ADR-007 Typed Relations in Frontmatter]]", "[[ADR-012 Semantic Search with Embedded LanceDB Index]]"]
---

# Vault MCP Server

**Відповідальність:** єдиний канал між агентом і цим vault ([[MCP]], stdio). Реалізує рішення [[ADR-005 Separate Vault Repository with MCP Access]].

## Інструменти
| Інструмент | Тип | Що робить |
|------------|-----|-----------|
| `ingest_note` | ingestion | Створює чи оновлює нотатку за конвенціями vault, додає нову в [[MOC]], комітить і пушить |
| `get_note` | retrieval | Нотатка за назвою чи alias: frontmatter, тіло, backlinks, типізовані зв'язки |
| `search_notes` | retrieval | Семантичний пошук ([[ADR-012 Semantic Search with Embedded LanceDB Index]]): N найближчих за змістом розділів нотаток із текстом; резерв — пошук за ключовими словами |

## Цикл ingestion
1. Валідація входу (назва, тип, ID вимог) — помилка не змінює репозиторій.
2. `git pull --ff-only`.
3. Пошук нотатки за назвою без урахування регістру.
4. Формування frontmatter і тіла; списки об'єднуються як множини.
5. Без змін → `unchanged`, без коміту; інакше запис і додавання в MOC.
6. `git commit` лише своїх файлів → `git push`.

## Цикл retrieval (ПР № 7)
1. Перед пошуком — синхронізація індексу: повторно вбудовуються лише нотатки зі зміненим sha1 тексту.
2. Embedding запиту (`multilingual-e5-small`, локально).
3. Векторний пошук у LanceDB (embedded, `harness/vault-mcp/.index/`) з фільтрами `type` / `tag`.
4. N фрагментів-розділів → JSON у контекст агента.

## Правила
- Vault — сусідній клон `../movie-tracker-vault` (змінна `VAULT_PATH`), не тека застосунку.
- Векторний індекс — похідний артефакт поза git; джерело істини — markdown vault. `npm run reindex` перебудовує його.
- Нотатки через конектор не видаляються.
- Не плутати з [[Recommender]]: це частина [[Agent Harness]], не застосунку.

## Код
Node.js 24, `@modelcontextprotocol/sdk`, `zod`, `yaml`, `@lancedb/lancedb`, `@huggingface/transformers`. Модулі: `server.ts`, `vault.ts`, `notes.ts`, `git.ts`, `semantic.ts`, `config.ts`, `reindex.ts`; порівняльне тестування — `eval/`. 24 тести на тимчасовому vault та індексі.
