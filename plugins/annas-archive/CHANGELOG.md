# Changelog — annas-archive

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [SemVer](https://semver.org/lang/ru/).

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
