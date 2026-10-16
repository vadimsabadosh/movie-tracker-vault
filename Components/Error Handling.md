---
type: component
aliases: [Обробка помилок]
tags: [component, infrastructure]
layer: infrastructure
source_path: src/errors/
status: planned
requirements: [NFR-2]
created: 2026-10-16
updated: 2026-10-16
---

# Error Handling

**Відповідальність:** єдиний формат помилок API та відповідність HTTP-статусів (NFR-2).

## Відповідність
- Помилка [[Validation Schemas]] → 400.
- Неіснуючий [[Movie]] чи [[Watch Entry]] → 404.
- Конфлікт (напр. повторна позначка перегляду) → 409.
- Непередбачена помилка → 500 без деталей реалізації.

## Реалізація
- Глобальний error handler Fastify у [[Routes Layer]]; доменні помилки кидає [[Services Layer]].
