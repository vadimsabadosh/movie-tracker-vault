---
type: decision
id: ADR-007
aliases: [Типізовані зв'язки]
tags: [decision, adr, ontology]
status: accepted
date: 2026-10-16
supersedes: []
superseded_by: []
sources: ["PR4: movie-tracker docs/ontology.md"]
created: 2026-10-16
updated: 2026-10-16
---

# ADR-007 Typed Relations in Frontmatter

## Контекст
Wiki-посилання фіксує лише факт згадування: з `[[Database]]` у тексті [[Routes Layer]] не видно, що це заборона, а не залежність. Агенту та скриптам потрібен тип зв'язку.

## Рішення
- Онтологія = типи нотаток (папки) + теги + типізовані зв'язки у frontmatter.
- П'ять зв'язків (домен → діапазон):
  - `depends_on`: component → component (обернений `used_by`)
  - `implements`: component → concept, decision (`implemented_by`)
  - `governed_by`: component, concept → decision (`governs`)
  - `part_of`: concept → concept (`has_part`)
  - `supersedes`: decision → decision (`superseded_by`)
- Запис: `depends_on: ["[[Services Layer]]"]` — список wiki-посилань у лапках; Obsidian показує їх як посилання.
- Обернені зв'язки не зберігаються, їх обчислює `get_note`.
- Пишуться лише через `ingest_note(relations)`; [[Vault MCP Server]] відхиляє зв'язок із неправильним доменом, діапазоном чи неіснуючою ціллю.

## Альтернативи
- Inline-поля Dataview (`depends_on:: [[X]]`) — потребують плагіна, гірше парсяться.
- OWL/RDF — зайва складність для vault на ~40 нотаток.
- Речення в тексті («залежить від») — не перевіряються машинно.

## Наслідки
- Граф залежностей шарів перевіряється автоматично (ПР № 6–7).
- Видалення зв'язку — лише вручну, як і видалення нотаток.
- Див. [[ADR-001 Layered Architecture]], [[MOC]].
