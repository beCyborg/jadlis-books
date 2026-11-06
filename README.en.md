[Русский](README.md) · English

# You need the book right now, and you are assembling it out of tabs: wrong format, wrong language, and the file you did download opens as a stub

You name the title, the author or a DOI — the skill finds the record in Anna's Archive, picks the
file by a filter policy, pulls it with the member API and prints how many fast downloads are left.

```
claude plugin marketplace add https://github.com/beCyborg/jadlis-hub.git
claude plugin install jadlis-books@jadlis
```

No key is asked for during install: downloads go through the member API, and you place the key
yourself — into the `env` block of `~/.claude/settings.json`, after which Claude Code is restarted.

![A title, an author or a DOI goes in — a file in your folder and the remaining fast downloads come out](docs/img/hero-jadlis-books.webp)

In words: on the left, the request in plain words or as a DOI string; on the right, the picked file
in the downloads folder and a line with the remaining fast downloads.

This is my workbench published as it is, not a product: whatever I stopped using, I removed.

## Before → after

| By hand | With an AI chat | With this plugin |
|---|---|---|
| **What you arrive with.** You half-remember the title, you know the author, and for a paper you only have a DOI — three different ways to search. | It parses the phrase and stops in the same place: what you need next is the page, not an understanding of your request. | Title, author and DOI are parsed on the way in; a DOI is recognised by the shape of the string and takes its own route. |
| **Which file you take.** You judge the results list by eye: which one is a scan, which one is a stub, which one is the full text — you cannot see. | It hands you a link to a page, but it will not fetch the file or put it in a folder. | Candidates get ranked: title and author match, the fast-download flag as a hard requirement, more mirror sources, a newer year, not a microscopic size. |
| **When the format you want is missing.** You take whatever is offered and sort it out at home. | It does not know which formats exist: it never opened the results. | The format follows the chain EPUB → PDF → MOBI/AZW3 → FB2/DJVU within English; nothing in English at all — it shows the other languages and asks. |
| **A list of books.** Each one from scratch: search, pick, download, and round again. | It will retype the list; it will not download it. | A batch runs in a loop with pauses between books, departures from the policy are flagged in the table, and at the end you get the path to every file. |
| **How many downloads are left.** You find out at the exact moment you hit the wall. | It knows nothing about the quota on your account. | The remaining fast downloads come back in the API response and are printed in the report; at zero the skill stops and hands you the unfinished list with md5s. |

## How it works

![A request splits into single, batch and DOI, candidates are ranked, and the file is checked by its first bytes](docs/img/how-jadlis-books.webp)

Going in — a title with an author, a list of titles, or a DOI string.
Inside — a search with format, language and publication-type filters, then candidate selection:
title and author match, the mandatory fast-download flag, more mirrors, a newer year.
Coming out — the file in your folder, checked by its first bytes, and the remaining fast downloads.

In words: request → filtered search → candidate ranking → download via the member API → first-bytes
check → a report with the path and the remaining quota.

The skill tells three kinds of request apart: one book, a list of books, and a DOI. A single request
goes out as a strict search with the `--ext epub --lang en` filters and a publication type; zero
matches and the filters come off, with the format picked client-side along the fallback chain. A list
takes the same road, one results page per book, with a pause between books, and lands in a table of
"book / format / language / year / mirrors / fallback / file". A string shaped like `10.xxxx/…` is
recognised on its own: first an exact hit through `/scidb`, then, on a miss, a search of the journals
index; if you did not ask to download, the skill hands you the candidate table and waits for
confirmation.

The file lands in `~/Books/AnnasArchive` under a name shaped like `Author — Title (Year)`, with the
extension taken from the download URL; want another folder — say so in the request. Before
downloading, the skill checks whether that file is already in the target folder. After downloading it
verifies the first bytes (EPUB → `PK`, PDF → `%PDF`), deletes a file that is too small as a partner
server's stub, and moves to the next candidate. Mirrors are rotated on their own along the chain
`.gl` → `.pk` → `.gd`, and the key is never printed into the output and is scrubbed out of error
texts.

## Installing and the first run

**a) Text to paste to an agent.** Copy the whole thing into a Claude Code chat:

```
You are the installer. Install the plugin jadlis-books from the jadlis marketplace on this Mac.
Run exactly these commands, verbatim, shortening nothing:
1. claude plugin marketplace add https://github.com/beCyborg/jadlis-hub.git
2. claude plugin install jadlis-books@jadlis
3. claude plugin list — show me the line about jadlis-books and its version.
The plugin asks for no keys during install. Once it is in, tell me in one line:
"Put the Anna's Archive key into the env block of ~/.claude/settings.json and restart Claude Code."
Do not edit that file yourself and never print the key value: check only "present" or "absent".
Before each command show it to me in full and wait for "yes". If I say "no", do not run it,
tell me what you skipped, and move on.
If a command returns an error, stop, show me the output, and do not move to the next one.
```

**b) Commands by hand.**

```
claude plugin marketplace add https://github.com/beCyborg/jadlis-hub.git
claude plugin install jadlis-books@jadlis
claude plugin list
```

The first command installs nothing — it adds the marketplace. Only the second one installs, and one
line removes it: `claude plugin uninstall jadlis-books@jadlis --keep-data`.

The key is not passed in here: the scripts read it from an environment variable, not from the
plugin's config. Put it into the `env` block of `~/.claude/settings.json` — no such file yet means
this is its entire content; the file already exists, so add only the `env` pair:

```json
{
  "env": {
    "ANNAS_ARCHIVE_KEY": "<your-key>"
  }
}
```

The alternative is your shell profile: `export ANNAS_ARCHIVE_KEY="<your-key>"` in `~/.zshenv` (zsh)
or `~/.bashrc` (bash). After the edit, restart Claude Code: variables are read when the session
starts. The `settings.json` file sits there in plain text — keep the permissions to yourself
(`chmod 600`).

**c) The short command.** Open Claude Code in the folder you work in and type:

```
/books download The Mom Test as an epub
```

If it is not found, check the name with `claude plugin list`. The skill also comes up from a plain
phrase, without a slash: "find the book …", "download these books: …", "download the paper by DOI …".
With no key it stops at the very first check and shows you where to put one; that is expected
behaviour, not a breakage.

More example phrases:

- "download The Mom Test in epub"
- "find the book Obviously Awesome"
- "download these books: …" — a list runs as one batch
- "download the paper with DOI 10.1038/nature12373"

The full form of the command is `/jadlis-books:books <request>`.

**Check after install.** The request `download The Mom Test in epub` puts the file
`~/Books/AnnasArchive/Rob Fitzpatrick — The Mom Test (2013).epub` in place. The report shows the
format, size, year and the remaining fast downloads.

## What is inside and how a file is picked

| File | Role |
|---|---|
| `skills/books/SKILL.md` | the instruction: selection policy, fallbacks, error handling |
| `skills/books/references/gotchas.md` | traps from real downloads: DNS, quota, DOI |
| `skills/books/scripts/aa_search.py` | search (HTML parsing of `/search`), DOI and `--meta` modes |
| `skills/books/scripts/aa_download.py` | download via `/dyn/api/fast_download.json`, file verification |

Both scripts are stdlib + `curl`, with no dependencies. HTTP goes through `curl` because the WAF
returns 403 for `urllib` requests.

File selection policy:

1. Title and author match.
2. The fast-download flag (🚀) is mandatory, otherwise the membership API will not serve the file.
3. Format: EPUB → PDF → MOBI/AZW3 → FB2/DJVU.
4. Language: English by default, or whichever you asked for.
5. On a tie: more mirror sources → newer year → size not microscopic.

## If something goes wrong

| Symptom | Cause and fix |
|---|---|
| `ERROR: ANNAS_ARCHIVE_KEY not set` | The session cannot see the key. Add it as described above and restart Claude Code |
| `API error: Invalid secret key` | The key expired or was copied truncated. Check the membership and reissue the key |
| `API error` about quota | The daily fast-download quota is spent. The skill keeps the md5 of what is left |
| `all mirrors failed` | The mirrors are unreachable from your network. Set your own: `ANNAS_ARCHIVE_MIRROR=https://<domain>` in the `env` block (or shell profile) and restart Claude Code |
| `ERROR: scripts not found` | The plugin is installed incorrectly. Reinstall it with the command from "Updating" below |
| `downloaded file suspiciously small` | The partner server returned a stub. The skill takes the next candidate |

Two more caveats:

- Re-downloading the same md5 may spend quota, so the skill checks the folder before downloading.
- Search parses the site's HTML: if the site changes its layout, `aa_search.py` will need editing.

## Limits, cost, updating

**What it does not do.** It does not retell the book and does not summarise it — it brings the file,
you do the reading. It does not parse book formats: it never looks inside an EPUB, PDF or DJVU and
does not judge the quality of the text. It does not do OCR on scans and does not repair broken files:
it checks the first bytes and moves to the next candidate on a stub. It does not decide the doubtful
cases for you — nothing in English, and it shows the options and asks; a DOI with no download request
comes back as a candidate table. **It does not bring you a file by DOI:** it searches by one and finds
the record, but the download route runs through the membership API by checksum, and for a journal
article that route ends there — the paper comes from open access, fetched plainly and outside the
plugin, and that part you do yourself. It does not reach logged-in places: the scripts only knock on
the Anna's Archive mirrors and the download address they were handed.

**What you need.** An active Anna's Archive membership and its API key from the account page: fast
downloads go through the member API, and without the key the skill stops on the first step. The key
never reaches the install dialog — it lives in the `ANNAS_ARCHIVE_KEY` environment variable (`env` in
`~/.claude/settings.json`, or `export` in your shell profile). Externally you need only `python3` and
`curl` on the PATH: the scripts are standard library, they pull no dependencies. You can point it at
your own mirror — `ANNAS_ARCHIVE_MIRROR`, in the same `env` block. I do not restate other people's
pricing: the membership invoice is theirs.

[уточнить] — I have not tested an account without an active membership: search needs no key,
downloading does.

[уточнить] — on some providers the local DNS does not resolve the partner download hosts; checked on
my network, the way around it is a public DNS.

**How tokens get spent.** A run is light: search, candidate selection and download are script calls,
no subagents come up here. A batch costs more than the rest: every book gets its own results page,
its own candidate pass and its own report line. What gets spent is not only your model quota but the
account's fast-download quota as well — the remainder is printed in the report, and at zero the skill
stops by itself.

**Verified where I work:** my Mac, my membership, my key. Where else this works — [уточнить].

**Terms of use.** There is no license: all rights reserved by the author. You may read it and use it
personally. Commercial use, republishing and bundling it into your own products — by arrangement
with me.

**Updating.** With a third-party marketplace, auto-update is off on your side: until you run the
first command you keep the version you installed.

```
claude plugin marketplace update jadlis
claude plugin update jadlis-books@jadlis
claude plugin list
```

Reinstall, if something ended up crooked:

```
claude plugin uninstall jadlis-books@jadlis --keep-data && claude plugin install jadlis-books@jadlis
```

**Moving from 2.x.** Since 3.0.0 the plugin sits at the
repository root. If you installed the plugin from the old jadlis-books catalogue (the
`becyborg-library` marketplace), the update will not arrive by itself. Remove the old install and put
the plugin in from the jadlis hub:

```
claude plugin uninstall jadlis-books@becyborg-library --keep-data
claude plugin marketplace remove becyborg-library
claude plugin marketplace add https://github.com/beCyborg/jadlis-hub.git
claude plugin install jadlis-books@jadlis
```

If your `jadlis` marketplace still points at the old GitHub address, remove it first:
`claude plugin marketplace remove jadlis`. Then run the last two commands from the block above.
