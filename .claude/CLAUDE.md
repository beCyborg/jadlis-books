# jadlis-books — конвенции репо

Репозиторий одного плагина: корень репо = плагин `jadlis-books` (`.claude-plugin/plugin.json`, скилл в `skills/books/`). Дом репо — GitHub `https://github.com/beCyborg/jadlis-books` (remote `origin`). Путь установки у получателя — хаб `jadlis` (`https://github.com/beCyborg/jadlis-hub.git`), который тянет этот репо записью `source: url` с пином `ref` + `sha`. Своего маркетплейса в репо больше нет (до 3.0.0 был `becyborg-library` с плагином в `plugins/books`). Правила ниже читает агент, который правит и коммитит.

## Коммиты

- Тема — Conventional Commits на английском: `type(scope): subject`, ≤72 символа. `scope` = `books`, `docs` или `repo`.
- Тело двухслойное: `Что изменилось:` — 1–3 предложения по-русски для человека; `Details (for agents):` — буллеты `Added / Changed / Removed / Migration / Refs` с путями.
- Без строк атрибуции (`Co-Authored-By` и подобных).

## Релизы

- Версия живёт только в `.claude-plugin/plugin.json`; бамп — в том же коммите, что и изменение (версия = ключ кеша обновлений).
- Тег `jadlis-books--v{X.Y.Z}`: `claude plugin tag . --push`. Заголовок и тело релиза — `python3 ~/jadlis-hub/tools/release-notes.py . [--title]`.
- `CHANGELOG.md` в корне: `## [X.Y.Z] — YYYY-MM-DD — <кратко по-русски> / <short EN>`, затем `### Для человека` (≤3 буллета) и `### For agents` (`Added / Changed / Removed / Migration / Breaking`, с путями).
- После релиза обновить пин в хабе: `python3 ~/jadlis-hub/tools/bump-pin.py jadlis-books jadlis-books--v{X.Y.Z}`.
- Только patch-forward: никаких force-push и переписывания тегов.

## README и доки

- Одна пара `README.md` (RU) + `README.en.md` (EN) в корне; первая строка — переключатель языка. Одинаковое число и порядок H2, без Mermaid. Проверка: `python3 ~/jadlis-hub/tools/readme-parity.py --no-mermaid .`
- Пять смысловых блоков документа инструмента: **Зачем / Как выглядит / Как поставить / Как пользоваться / Границы и стоимость**; дополнительные H2 — только одинаково в обеих языковых версиях.
- Иллюстрации — `docs/img/*.webp`, промпты к ним — `docs/img/prompts/`; картинки только относительными путями, которые существуют.
- Числа и механику сверять со скриптами (`aa_search.py`, `aa_download.py`). Примеры вывода — синтетические.
- Оценок времени в доках не давать: вместо «за 10 минут» — что именно нужно сделать.

## Приватность и ключи

- В файлах репо нет ключей, почт, телефонов, путей владельца (`/Users/<имя>` → `~`) и имени владельца. Перед пушем: `gitleaks git . --no-banner --redact` и `python3 ~/jadlis-hub/tools/privacy-grep.py .` (оба гоняет CI хаба).
- Ключ `ANNAS_ARCHIVE_KEY` живёт только у получателя — в блоке `env` файла `~/.claude/settings.json` либо в профиле шелла. В репо, в примерах и в логах его быть не может: скрипты читают ключ из окружения и вычищают из текстов ошибок. Никогда не печатать значение ключа в вывод скилла.
- Домашний стандарт хранит ключи в Связке ключей macOS; у этого плагина своего читателя Связки нет — если он появится, доки правятся вместе с кодом, а не раньше.
- Плагин ходит только на зеркала Архива Анны и на выданный API адрес загрузки; новых доменов в скриптах не заводить без явной просьбы.

## Разработка

- Правки только в рабочем клоне `~/jadlis-books`, никогда в `~/.claude/plugins/marketplaces/` или `~/.claude/plugins/cache/` (фоновый рефреш стирает правки).
- Перед коммитом: `claude plugin validate . --strict`, `readme-parity.py --no-mermaid .`, `privacy-grep.py .`, `gitleaks git .`.
- CI — `.github/workflows/ci.yml`, reusable workflow `beCyborg/jadlis-hub/.github/workflows/plugin-ci.yml`; своих job'ов не заводить.
- Скрипты — stdlib + `curl`, без внешних зависимостей: HTTP через `curl`, потому что WAF отдаёт 403 на `urllib`. Зависимости не добавлять. Синтаксис-проверка: `python3 -m py_compile skills/books/scripts/*.py`.
- Имя плагина — `jadlis-books`, менять нельзя. Короткая команда `/books` задаётся `name:` во frontmatter скилла; полная форма — `/jadlis-books:books`.
- Язык доков — русский (RU-файл первичен) + английская пара; код, идентификаторы и имена файлов — английский.
