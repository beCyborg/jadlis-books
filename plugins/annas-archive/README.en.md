[Русский](README.md) · English

# annas-archive — books and papers in one sentence

A librarian skill for Claude Code. You name a book — Claude finds it on Anna's Archive, picks the best file, downloads it through the membership API and reports the remaining quota.

## Usage

No separate command is needed — the skill triggers on phrases like:

- "download The Mom Test in epub"
- "find the book Obviously Awesome"
- "download these books: …" (a list runs as one batch)
- "download the paper with DOI 10.1038/nature12373"

Explicit call: `/annas-archive <request>`.

## Key setup

Downloads go through the membership fast-download API — you need an active Anna's Archive account and its API key (account page on the site). Search works without a key.

Option 1 (recommended) — `~/.claude/settings.json`:

```json
{
  "env": {
    "ANNAS_ARCHIVE_KEY": "<your-key>"
  }
}
```

No such file yet — this is its entire contents. The file exists — add just the `env` pair.

Option 2 — your shell profile: `export ANNAS_ARCHIVE_KEY="<your-key>"` in `~/.zshenv` (zsh) or `~/.bashrc` (bash).

Restart Claude Code after editing.

## Smoke test

```
download The Mom Test in epub
```

Expected: the file `~/Books/AnnasArchive/Rob Fitzpatrick — The Mom Test (2013).epub`, and a report with format, size, year and the remaining fast downloads.

## What is inside

| File | Role |
|---|---|
| `skills/annas-archive/SKILL.md` | the instruction: selection policy, fallbacks, error handling |
| `skills/annas-archive/scripts/aa_search.py` | search (HTML parsing of `/search`), DOI and `--meta` modes |
| `skills/annas-archive/scripts/aa_download.py` | download via `/dyn/api/fast_download.json`, file verification |

Both scripts are stdlib + `curl`, with no dependencies. HTTP goes through `curl` because the WAF returns 403 for `urllib` requests.

## File selection policy

1. Title and author match.
2. The fast-download flag (🚀) is mandatory, otherwise the membership API will not serve the file.
3. Format: EPUB → PDF → MOBI/AZW3 → FB2/DJVU.
4. Language: English by default (or whichever you asked for).
5. On a tie: more mirror sources → newer year → size not microscopic.

## Troubleshooting

| Symptom | Cause and fix |
|---|---|
| `ERROR: ANNAS_ARCHIVE_KEY not set` | The session cannot see the key — add it as above and restart Claude Code |
| `API error: Invalid secret key` | The key expired or was copied truncated — check the membership and reissue |
| `API error` about quota | The daily fast-download quota is spent — Claude keeps the md5 of what is left |
| `all mirrors failed` | Mirrors are unreachable from your network — set your own: `ANNAS_ARCHIVE_MIRROR=https://<domain>` in the same `env` block (or shell profile) and restart Claude Code |
| `ERROR: scripts not found` | The plugin is installed incorrectly — reinstall: `/plugin install annas-archive@becyborg-library` |
| `downloaded file suspiciously small` | The partner server returned a stub — Claude takes the next candidate |

## Limits

- Re-downloading the same md5 **spends quota** (observed in practice, contrary to the FAQ's 18-hour dedup claim) — the skill checks the folder before downloading.
- Search parses the site's HTML: a layout change may require editing `aa_search.py`.
