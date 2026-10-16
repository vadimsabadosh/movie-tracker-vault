---
type: decision
id: ADR-005
aliases: [Окремий репозиторій vault]
tags: [decision, adr, harness]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
created: 2026-10-16
---

# ADR-005 Separate Vault Repository with MCP Access

## Контекст
Агент (Claude Code) має доступ до файлової системи робочої директорії. Якщо база знань лежить усередині репозиторію застосунку, агент читає й змінює її в обхід контрольованого шару.

## Рішення
- Vault — окремий git-репозиторій `movie-tracker-vault`, склонований поза директорією застосунку.
- Доступ агента — лише через [[MCP]]-сервер конектора (ingestion + retrieval, ПР № 3).
- Прямий доступ до vault — block-правило [[Guardrail]].

## Альтернативи
- Папка `docs/vault` у репозиторії застосунку — простіше, але без контролю доступу.
- Neo4j / Chroma як сервер — зайва інфраструктура.

## Наслідки
- Синхронізація двох репозиторіїв — відповідальність MCP-сервера (git pull / commit / push).
- Ідемпотентність ingestion обов'язкова. Див. [[Agent Harness]], [[MOC]].
