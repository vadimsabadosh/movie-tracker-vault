# movie-tracker-vault

База знань (knowledge layer agent harness) для застосунку [movie-tracker](https://github.com/vadimsabadosh/movie-tracker).
Vault Obsidian зберігається в окремому репозиторії: агент читає й пише його **лише через MCP-конектор** (ПР № 3), а не напряму.

Точка входу: [[MOC - movie-tracker]].

## Структура

| Папка | Тип нотатки (`type`) | Зміст |
|-------|----------------------|-------|
| `Concepts/` | `concept` | Поняття предметної області (фільм, перегляд, оцінка…) |
| `Components/` | `component` | Модулі й шари кодової бази застосунку |
| `Decisions/` | `decision` | Архітектурні рішення у форматі ADR |
| `Glossary/` | `glossary` | Терміни проєкту та harness |
| `Templates/` | — | Шаблони frontmatter (не є знаннями, не лінкуються) |
| корінь | `moc` | Maps of Content — навігаційні хаби |

## Конвенції

1. **Назви файлів** — англійською, Title Case, повні слова без скорочень (`Watch Entry.md`, не `WE.md`). Українська назва — в `aliases`, тому працюють і `[[Watch Entry]]`, і `[[Перегляд]]`.
   Причина: латиниця в іменах файлів уникає проблем Unicode-нормалізації (NFC/NFD) між macOS, git і MCP-сервером.
2. **ADR** — `ADR-NNN Short Title.md`, номер ніколи не перевикористовується; рішення не видаляються, а позначаються `status: superseded`.
3. **Frontmatter обов'язковий**: щонайменше `type`, `created`, `tags`. Шаблони — у `Templates/`.
4. **Зв'язки — лише через `[[wiki-посилання]]`**, а не текстом. Типізовані зв'язки (`depends_on`, `implements`…) додаються в ПР № 4.
5. **Посилання на вимоги** — через поле `requirements` (ID з `docs/app-requirements.md`: `FR-*`, `NFR-*`).
6. Кожна нотатка має бути досяжна з [[MOC - movie-tracker]].
