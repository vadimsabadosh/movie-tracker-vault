---
type: decision
id: ADR-006
aliases: [GitHub MCP лише для читання]
tags: [decision, adr, harness, mcp]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
sources: [github:movie-tracker@43a0aa6]
created: 2026-10-16
updated: 2026-10-16
---

# ADR-006 Read-Only GitHub MCP as Data Source

## Контекст
Агенту потрібні дані про зміни в застосунку (коміти, PR), щоб фіксувати їх у vault через [[Vault MCP Server]]. Писати власний конектор до GitHub немає сенсу — є офіційний `github/github-mcp-server`. Проте він надає десятки інструментів, зокрема запис.

## Рішення
- Підключити офіційний remote-сервер `https://api.githubcopilot.com/mcp/` у `.mcp.json`.
- Заголовок `X-MCP-Readonly: true` — інструменти запису не показуються агенту.
- `X-MCP-Toolsets: repos,pull_requests` — лише коміти, вміст і PR.
- Fine-grained PAT зі змінної `GITHUB_PAT`: лише репозиторій `movie-tracker`; Contents, Pull requests, Metadata — read-only.

## Альтернативи
- Локальний `git log` через bash — працює, але без типізованого контракту й не покриває PR.
- Classic PAT зі scope `repo` — дає запис у всі репозиторії, надмірно.

## Наслідки
- Обмеження на двох рівнях: токен (GitHub відхилить запис) і заголовки (агент не бачить інструментів запису).
- Токен не зберігається в репозиторії; термін дії — до кінця семестру.
- Див. [[MCP]], [[Agent Harness]], [[ADR-005 Separate Vault Repository with MCP Access]].
