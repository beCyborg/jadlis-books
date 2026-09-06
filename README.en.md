[Русский](README.md) · English

# annas-archive — books and papers in one sentence

A Claude Code plugin that turns "download The Mom Test in epub" into a file on disk: search on [Anna's Archive](https://annas-archive.org), pick the best file out of dozens of duplicates, download it through the membership fast-download API.

## Why

The manual route: open the site, type the title, eyeball twenty entries with identical covers, guess which EPUB is not broken, hit download — and repeat for every book on your list.

The plugin removes the sifting: candidates are ranked by format, language, number of mirror libraries, year and size, and entries without the fast-download flag are dropped immediately — the membership API will not serve them.

It handles lists ("download these twelve books") and papers by DOI, and reports the remaining daily quota after each download.

## What it looks like

![A staircase built from stacks of books with scales on top](docs/img/10-overview.v2-03.webp)

<details>
<summary>Synthetic batch report</summary>

```
Downloaded 4 of 5 (tag annas-archive--v1.0.1)

| Book                          | Format | Year | File                                    |
|-------------------------------|--------|------|-----------------------------------------|
| The Mom Test                  | epub   | 2013 | Rob Fitzpatrick — The Mom Test (2013).epub |
| Obviously Awesome             | epub   | 2019 | April Dunford — Obviously Awesome (2019).epub |
| Zero to Sold                  | pdf    | 2020 | Arvid Kahl — Zero to Sold (2020).pdf    |
| Deploy Empathy                | epub   | 2021 | Michele Hansen — Deploy Empathy (2021).epub |
| The SaaS Playbook             | —      | —    | ⚠ German only, needs your call          |

Folder: ~/Books/AnnasArchive/
Fast downloads left today: 16 of 20
```

</details>

## Install

Open Claude Code and paste this block into the input line:

```
You are an installer. Do exactly these steps and nothing beyond them:
1. Bash: claude plugin marketplace add https://github.com/beCyborg/jadlis-plugins.git
2. Bash: claude plugin install annas-archive@jadlis
3. Tell me in one line: "Put your Anna's Archive key into ~/.claude/settings.json and restart Claude Code".
```

The same two commands by hand, if Bash is unavailable in the app:

```bash
claude plugin marketplace add https://github.com/beCyborg/jadlis-plugins.git
claude plugin install annas-archive@jadlis
```

The plugin also installs from this repository's own marketplace, `becyborg-library`:

```bash
claude plugin marketplace add https://github.com/beCyborg/annas-archive-plugin.git
claude plugin install annas-archive@becyborg-library
```

The full HTTPS URL guards against an SSH clone: the `owner/repo` shorthand goes over SSH, and a new user usually has no SSH key on GitHub.

**The key.** You need an active Anna's Archive membership; the key is on your account page. The scripts read it from the `ANNAS_ARCHIVE_KEY` environment variable, so it goes into the `env` block of `~/.claude/settings.json` (which applies to every Claude Code session and every process launched from it):

```json
{
  "env": {
    "ANNAS_ARCHIVE_KEY": "<your-key>"
  }
}
```

No such file yet — this is its entire contents; the file exists — add just the `env` pair. The alternative is your shell profile (`export ANNAS_ARCHIVE_KEY="..."` in `~/.zshenv` or `~/.bashrc`). Restart Claude Code afterwards: variables are read at session start. The house standard keeps keys in the macOS Keychain, but this plugin has no Keychain reader of its own — `settings.json` is plain text, so keep its permissions to yourself (`chmod 600`).

**Check.** Tell Claude: "download The Mom Test in epub". The file lands in `~/Books/AnnasArchive/` — the plugin works. Without a key Claude stops at the first step and prints the setup instructions: expected behaviour, not a failure.

## Usage

Three scenarios, in plain text or as a skill command:

```
/annas-archive:annas-archive download The Mom Test in epub
/annas-archive:annas-archive download these books: Obviously Awesome, Zero to Sold, Deploy Empathy
/annas-archive:annas-archive download the paper 10.1038/nature12373
```

- **One book.** Ranking is `EPUB → English → more mirrors → newer → more popular`; no EPUB — the chain `pdf → mobi/azw3 → fb2/djvu`; nothing in English — Claude shows the other-language options and asks.
- **Batch.** The list is downloaded in a loop with pauses; at the end a "book / format / year / file" table and the remaining quota. Quota exhausted — it stops and lists what is left.
- **DOI.** A string like `10.1038/nature12373` goes to `/scidb` as an exact hit, falling back to the journals index on a miss.
- **Where files land.** `~/Books/AnnasArchive/` by default, named `Author — Title (Year).epub`. Need another folder — say so in the request.

## Limits and cost

- **Membership is required.** Without a key only search works: downloading goes through the membership fast-download API. The plugin is free, the membership is not.
- **Daily quota.** The number of remaining fast downloads comes from the API response and is printed in the report. Re-downloading the same file does spend quota (observed 2026-07; the 18-hour dedup claimed in the FAQ did not hold).
- **What it does not do.** No OCR and no scan recognition, no repairing broken files, no bypassing service limits. After each download a magic byte is checked (EPUB → `PK`, PDF → `%PDF`), so a partner-server stub is caught immediately — but the quality of the text inside is not judged.
- **Where it connects.** Only Anna's Archive mirrors (`.gl` → `.pk` → `.gd`, with rotation; override via `ANNAS_ARCHIVE_MIRROR`) and the download URL the API returns. The key is never printed and is scrubbed from error text.
- **Requirements.** Claude Code (any platform), `python3` and `curl` on PATH. Copyright compliance in your jurisdiction is on you.

## Update

For third-party marketplaces auto-update is off by default on the recipient's side — a new version arrives on command:

```bash
claude plugin update annas-archive@jadlis
```

Or enable auto-update once: `/plugin` → **Marketplaces** → `jadlis`. If you installed from `becyborg-library`, use that name instead of `jadlis`. The version is explicit semver in `plugin.json`; history in [CHANGELOG.md](plugins/annas-archive/CHANGELOG.md). Marketplace and plugin names are frozen since the first release: renaming breaks existing installs.

## License

MIT — see [LICENSE](LICENSE). Skill details — [plugins/annas-archive/README.en.md](plugins/annas-archive/README.en.md).
