---
type: component
aliases: [Схеми валідації, Zod-схеми]
tags: [component, infrastructure, validation]
layer: infrastructure
source_path: src/schemas/
status: planned
requirements: [NFR-2, NFR-6]
created: 2026-10-16
updated: 2026-10-16
---

# Validation Schemas

**Відповідальність:** схеми Zod для всіх вхідних даних і відповідей; з них виводяться TypeScript-типи та OpenAPI — [[ADR-004 Zod as Single Source of Truth]].

## Приклади правил
- [[Rating]] — ціле 1–10.
- [[Movie]] — непорожня назва, щонайменше один [[Genre]].
- Параметри [[Pagination]] — додатні цілі з верхньою межею.

## Використання
- [[Routes Layer]] валідує запити; помилки — у форматі [[Error Handling]].
