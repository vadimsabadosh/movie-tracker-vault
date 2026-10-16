---
type: decision
id: ADR-004
aliases: [Zod як джерело правди]
tags: [decision, adr, validation]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
created: 2026-10-16
---

# ADR-004 Zod as Single Source of Truth

## Контекст
NFR-2 (валідація всіх входів) і NFR-6 (OpenAPI відповідає коду). Дублювання типів TS, схем валідації й OpenAPI призводить до розходжень.

## Рішення
[[Validation Schemas]] на Zod — єдине джерело: з них виводяться TS-типи (`z.infer`), валідація в [[Routes Layer]] і OpenAPI-специфікація.

## Альтернативи
- JSON Schema вручну + окремі TS-типи — дублювання.
- TypeBox — нативний для Fastify, але Zod уже обрано в стеку й він зручніший для доменної валідації.

## Наслідки
- Потрібен адаптер Zod → Fastify schema / OpenAPI.
- Синхронізація документації (задача T4) зводиться до перегенерації.
