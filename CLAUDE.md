# annas-archive-plugin — конвенции репо

Репозиторий одного плагина: маркетплейс `becyborg-library`, плагин в `plugins/annas-archive`. Основной путь установки у получателя — хаб `jadlis` (`https://github.com/beCyborg/jadlis-plugins.git`), который тянет этот репо записью `git-subdir` с пином `ref` + `sha`. Правила ниже читает агент, который правит и коммитит.

## Коммиты

- Тема — Conventional Commits на английском: `type(scope): subject`, ≤72 символа. `scope` = `annas-archive`, `docs` или `repo`.
- Тело двухслойное: `Что изменилось:` — 1–3 предложения по-русски для человека; `Details (for agents):` — буллеты `Added / Changed / Removed / Migration / Refs` с путями.
- Без строк атрибуции (`Co-Authored-By` и подобных).

## Релизы

- Версия живёт только в `plugins/annas-archive/.claude-plugin/plugin.json`; бамп — в том же коммите, что и изменение (версия = ключ кеша обновлений).
- Тег `annas-archive--v{X.Y.Z}`: `claude plugin tag --push plugins/annas-archive`. GitHub Release поверх тега: заголовок и тело — `python3 ~/jadlis-plugins/tools/release-notes.py plugins/annas-archive [--title]`.
- `CHANGELOG.md` плагина: `## [X.Y.Z] — YYYY-MM-DD — <кратко по-русски> / <short EN>`, затем `### Для человека` (≤3 буллета) и `### For agents` (`Added / Changed / Removed / Migration / Breaking`, с путями).
- После релиза обновить пин в хабе: `python3 ~/jadlis-plugins/tools/bump-pin.py annas-archive annas-archive--v{X.Y.Z}`.
- Только patch-forward: никаких force-push и переписывания тегов.

## README и доки

- Пара `README.md` (RU) + `README.en.md` (EN) — и в корне, и в папке плагина; первая строка — переключатель языка. Одинаковое число и порядок H2. Проверка: `python3 ~/jadlis-plugins/tools/readme-parity.py .`
- Пять секций документа инструмента: **Зачем / Как выглядит / Как поставить / Как пользоваться / Границы и стоимость**; дополнительные H2 («Обновление», «Лицензия») — только одинаково в обеих языковых версиях.
- Иллюстрации — `docs/img/*.webp`, копии из `~/jadlis-plugins/docs/img/`; каждая просмотрена глазами, отметка ☑ ставится в `~/jadlis-plugins/docs/img/INDEX.md`. Схемы — Mermaid.
- Числа и механику сверять со скриптами (`aa_search.py`, `aa_download.py`) и писать рядом тег (`(тег annas-archive--v1.0.1)`). Примеры вывода — синтетические.
- Оценок времени в доках не давать: вместо «за 10 минут» — что именно нужно сделать.

## Приватность и ключи

- В файлах репо нет ключей, почт, телефонов, путей владельца (`/Users/<имя>` → `~`) и имени владельца. Перед пушем: `gitleaks git .` и `python3 ~/jadlis-plugins/tools/privacy-grep.py .` (оба гоняет CI).
- Ключ `ANNAS_ARCHIVE_KEY` живёт только у получателя — в блоке `env` файла `~/.claude/settings.json` либо в профиле шелла. В репо, в примерах и в логах его быть не может: скрипты читают ключ из окружения и вычищают из текстов ошибок. Никогда не печатать значение ключа в вывод скилла.
- Домашний стандарт хранит ключи в Связке ключей macOS; у этого плагина своего читателя Связки нет — если он появится, доки правятся вместе с кодом, а не раньше.
- Плагин ходит только на зеркала Архива Анны и на выданный API адрес загрузки; новых доменов в скриптах не заводить без явной просьбы.

## Разработка

- Правки только в рабочем клоне `~/annas-archive-plugin`, никогда в `~/.claude/plugins/marketplaces/` (фоновый рефреш стирает правки).
- Перед коммитом: `claude plugin validate plugins/annas-archive`, `claude plugin validate .claude-plugin/marketplace.json`, `readme-parity.py`, `privacy-grep.py`.
- Скрипты — stdlib + `curl`, без внешних зависимостей: HTTP через `curl`, потому что WAF отдаёт 403 на `urllib`. Зависимости не добавлять.
- Имена плагина (`annas-archive`) и маркетплейса (`becyborg-library`) неизменяемы: переименование ломает существующие установки.
- Язык доков — русский (RU-файл первичен) + английская пара; код, идентификаторы и имена файлов — английский.
