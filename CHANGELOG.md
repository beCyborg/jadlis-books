# Changelog — jadlis-books

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [SemVer](https://semver.org/lang/ru/).

## [Unreleased]

## [3.0.0] — 2026-11-03 — репозиторий на GitLab, плагин в корне / repo moved to GitLab, plugin at repo root

### Для человека
- Репозиторий переехал на GitLab: `https://gitlab.com/beCyborg/jadlis-books`. Ставится плагин из хаба: `claude plugin marketplace add https://gitlab.com/beCyborg/jadlis-hub.git`, затем `claude plugin install jadlis-books@jadlis`.
- Плагин теперь лежит в корне репозитория, отдельного каталога `becyborg-library` больше нет. Имя `jadlis-books` и команда `/books` не изменились.
- Если плагин стоял из старого каталога jadlis-books (git-subdir, маркетплейс `becyborg-library`), удалите его и поставьте заново из хаба jadlis — команды ниже в Migration и в README («Переход с версии 2.x»).

### For agents
- Changed: `plugins/books/skills/` → `skills/`, `plugins/books/.claude-plugin/plugin.json` → `.claude-plugin/plugin.json`, `plugins/books/CHANGELOG.md` → `CHANGELOG.md`; папка `plugins/` удалена.
- Changed: `.claude-plugin/plugin.json` — `version` 2.0.0 → 3.0.0, `homepage`/`repository` → `https://gitlab.com/beCyborg/jadlis-books`, keyword `jadlis`.
- Changed: `README.md` / `README.en.md` — слиты с README плагина (примеры фраз, проверка после установки, «Что внутри и как выбирается файл», «Если что-то не так»); адрес хаба `https://gitlab.com/beCyborg/jadlis-hub.git`; раздел о переходе с 2.x. Первая строка `README.en.md` — `[Русский](README.md) · English`.
- Changed: `CLAUDE.md` → `.claude/CLAUDE.md` (корневой `CLAUDE.md` валит `claude plugin validate --strict`), переписан под корень-как-плагин и инструменты `~/jadlis-hub-gitlab/tools/`.
- Removed: `.claude-plugin/marketplace.json` (маркетплейс `becyborg-library`), `plugins/books/README.md`, `plugins/books/README.en.md`, `.github/`.
- Added: `.gitlab-ci.yml` (include `/ci/plugin-ci.yml` из `beCyborg/jadlis-hub`), расширенный `.gitignore`.
- Unchanged: `skills/books/SKILL.md`, скрипты `aa_search.py` / `aa_download.py`, переменные `ANNAS_ARCHIVE_KEY` / `ANNAS_ARCHIVE_MIRROR`, папка загрузок `~/Books/AnnasArchive`.
- Breaking: маркетплейс `becyborg-library` и источник `git-subdir` `plugins/books` больше не существуют — установка `jadlis-books@becyborg-library` не обновится.
- Migration: `claude plugin uninstall jadlis-books@becyborg-library --keep-data && claude plugin marketplace remove becyborg-library && claude plugin marketplace add https://gitlab.com/beCyborg/jadlis-hub.git && claude plugin install jadlis-books@jadlis`. Маркетплейс `jadlis` всё ещё смотрит на GitHub — сначала `claude plugin marketplace remove jadlis`.

## [2.0.0] — 2026-09-10 — переименование в jadlis-books / renamed to jadlis-books

### Для человека
- Плагин переименован: `books` → `jadlis-books`. Ставится теперь строкой `claude plugin install jadlis-books@jadlis`, маркетплейс добавляется по адресу хаба `https://github.com/beCyborg/jadlis-hub`.
- Короткая команда `/books` не изменилась — её задаёт `name:` во frontmatter скилла; полная форма стала `/jadlis-books:books`.
- Совместимости со старым именем нет: запись `renames` убрана, внешних установок не было. Стоит старая версия — переустановите под новым именем.

### For agents
- Changed: `plugins/books/.claude-plugin/plugin.json` — `name` `books` → `jadlis-books`, `version` 1.1.0 → 2.0.0, `displayName`.
- Changed: `.claude-plugin/marketplace.json` — запись плагина `name: jadlis-books`; блок `renames` удалён; имя маркетплейса `becyborg-library` и `source: ./plugins/books` не менялись.
- Changed: `README.md` / `README.en.md` (корень) — `claude plugin marketplace add https://github.com/beCyborg/jadlis-hub`, установка/обновление/удаление под именем `jadlis-books`; текст промпта-установщика.
- Changed: `plugins/books/README.md` / `README.en.md` — заголовок `jadlis-books`, строка `/plugin install jadlis-books@jadlis` в Troubleshooting.
- Changed: `CLAUDE.md` — хаб `jadlis-hub`, тег `jadlis-books--v{X.Y.Z}`, пути инструментов `~/jadlis-hub/tools/`, рабочий клон `~/jadlis-books`, правило про `renames` заменено на «без совместимости».
- Changed: `.github/workflows/ci.yml` — переиспользуемый workflow `beCyborg/jadlis-hub/.github/workflows/plugin-ci.yml@main`.
- Unchanged: папки `plugins/books/` и `skills/books/`, `name: books` во frontmatter скилла, переменные `ANNAS_ARCHIVE_KEY` / `ANNAS_ARCHIVE_MIRROR`, скрипты `aa_search.py` / `aa_download.py`, папка загрузок `~/Books/AnnasArchive`.
- Breaking: имя плагина изменилось, `renames` нет — старая установка `books@jadlis` не обновится сама.
- Migration: `claude plugin uninstall books@jadlis --keep-data && claude plugin install jadlis-books@jadlis`.

## [1.1.0] — 2026-09-07 — переименование в books / renamed to books

### Для человека
- Плагин переименован в `books`, команда `/books`; репо — `jadlis-books`, маркетплейс `jadlis`. Старая установка `annas-archive@becyborg-library` продолжает работать через `renames`.

### For agents
- Renamed: `plugins/annas-archive/` → `plugins/books/`; `skills/annas-archive/` → `skills/books/`; репозиторий `beCyborg/annas-archive-plugin` → `beCyborg/jadlis-books`.
- Changed: `plugins/books/.claude-plugin/plugin.json` — `name` `annas-archive` → `books`, `version` 1.0.2 → 1.1.0, `displayName`, `homepage`/`repository`.
- Changed: `.claude-plugin/marketplace.json` — запись плагина `name: books`, `source: ./plugins/books`, добавлен `renames: {"annas-archive": "books"}`; имя маркетплейса `becyborg-library` не менялось.
- Changed: `.github/workflows/` — CI переведён на переиспользуемый workflow `beCyborg/jadlis-start/.github/workflows/plugin-ci.yml@main` (`mode: marketplace`).
- Changed: README (корень и плагин), `CLAUDE.md` — команды установки и вызова, пути, тег `books--v{X.Y.Z}`.
- Unchanged: переменные окружения `ANNAS_ARCHIVE_KEY` / `ANNAS_ARCHIVE_MIRROR`, зеркала `annas-archive.*`, имена скриптов `aa_search.py` / `aa_download.py`, папка загрузок `~/Books/AnnasArchive`.
- Migration: не требуется — `renames` перенаправляет существующие установки на новое имя.

## [1.0.2] — 2026-09-07 — репозиторий как источник правды / repo as source of truth

### Для человека
- Перенесены наработки из локальной копии скилла: собран файл `references/gotchas.md` с граблями, накопленными при реальных загрузках, и ссылка на него из `SKILL.md`.
- Локальная копия скилла выводится из обращения — плагин из этого репозитория теперь единственный источник правды.

### For agents
- Added: `plugins/annas-archive/skills/annas-archive/references/gotchas.md`.
- Changed: `plugins/annas-archive/skills/annas-archive/SKILL.md` — врезка со ссылкой на `references/gotchas.md` после frontmatter.
- Changed: `plugins/annas-archive/.claude-plugin/plugin.json` — `version` 1.0.1 → 1.0.2.
- Known issue: `SKILL.md` (Phase 4) и `references/gotchas.md` расходятся в вопросе, тратит ли повторный запрос того же md5 квоту. Требует проверки; до неё считать поведение неопределённым.
- Migration: не требуется.

## [1.0.1] — 2026-09-06 — двуязычный README и гейты передачи / bilingual README and handover gates

### Для человека
- README переписан по пяти секциям (Зачем / Как выглядит / Как поставить / Как пользоваться / Границы и стоимость), рядом появился английский `README.en.md`.
- Основной путь установки теперь через хаб `jadlis`; свой маркетплейс `becyborg-library` остался как альтернатива.
- Из README убрано устаревшее «версия = commit SHA»: версия задана semver'ом, обновление приезжает командой `claude plugin update`.

### For agents
- Added: `README.en.md`, `plugins/annas-archive/README.en.md`, `CHANGELOG.md`, `CLAUDE.md`, `docs/img/10-overview.v2-03.webp`.
- Changed: `README.md` — 5 секций + «Обновление» и «Лицензия»; установка через `https://github.com/beCyborg/jadlis-plugins.git` → `claude plugin install annas-archive@jadlis`; ключ `ANNAS_ARCHIVE_KEY` описан с оговоркой про открытый текст `settings.json`.
- Changed: `plugins/annas-archive/README.md` — первой строкой переключатель языка.
- Changed: `.github/workflows/plugin-validate.yml` — job `gitleaks` (`gitleaks/gitleaks-action@v2`, `fetch-depth: 0`) рядом с job `validate`.
- Changed: `plugins/annas-archive/.claude-plugin/plugin.json` — `version` 1.0.0 → 1.0.1.
- Migration: не требуется, скрипты и политика выбора файла не менялись.

## [1.0.0] — 2026-09-01 — первый релиз плагина / first plugin release

### Для человека
- Скилл-библиотекарь: поиск книги или статьи в Архиве Анны, выбор лучшего файла по политике EPUB → английский → больше зеркал → новее → популярнее, скачивание членским API и отчёт с остатком квоты.
- Умеет батч-списки и научные статьи по DOI; после скачивания сверяет magic-байт файла.
- Репозиторий приведён к домашнему стандарту: явный semver, `$schema` в манифестах, LICENSE, CI-валидация плагина.

### For agents
- Added: `plugins/annas-archive/` — скилл `annas-archive` (`SKILL.md`), `scripts/aa_search.py` (парсинг `/search`, режимы `scidb`/`journals_search`, `--meta`), `scripts/aa_download.py` (`/dyn/api/fast_download.json`, ротация зеркал `.gl` → `.pk` → `.gd`, проверка magic-байта).
- Added: маркетплейс `becyborg-library` в `.claude-plugin/marketplace.json`; `.github/workflows/plugin-validate.yml`; `LICENSE` (MIT).
- Changed: версия плагина переведена с commit SHA на явный semver в `plugins/annas-archive/.claude-plugin/plugin.json`.
