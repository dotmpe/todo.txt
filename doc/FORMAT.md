# TODO.txt format

Levels 0–4 for a plain-text record file. Write records at level 3 and place them at level 4. A tool names the highest level it reads and writes.

## Pior art

The record prefix:

```
[x ][(P) ][YYYY-MM-DD ][YYYY-MM-DD ]body
```

`P` is `A`–`Z` for extended or `A`-`F` for standard 6-level priority ordering.
On an open record the date is the creation date. On a closed record the first date is the close date and the second is the creation date. The body is prose plus tags.

Upstream <https://github.com/todotxt/todo.txt> states the same prefix as five rules, split by open and closed. Every task line in those rules is a level 1 or level 2 record here. This adds the card, the locator, and the standard home map for project work trees.

Upstream outlines priority/project/context axes for user driven organisation of tasks, and takes care to detail out a strict format for just that. This builds on the idea, and adds as as well plans for slightly different derived syntax.

Readers for lower levels require adjusted pipelines or sources for correct consumption of level >2 cards.

The appendix adds further high level notes, on priority levels and logical structure.

## Levels

| Level | Unit | What is defined |
| --- | --- | --- |
| 0 | line | a non-comment line is a record |
| 1 | prefix | close mark, priority, dates |
| 2 | tags | project, context, attribute, category, locator |
| 3 | card | continuation lines belong to the header |
| 4 | home | which file holds the canonical copy |

A blank line has no non-space characters. A comment is a line whose first non-space character is `#`. Blanks and comments are not records, at every level.

## 1. Prefix

```
open   = [priority] [date SP] body LF
closed = "x" SP [priority] [date SP] [date SP] body LF
priority = "(" P ")" SP
date   = 4DIGIT "-" 2DIGIT "-" 2DIGIT
P      = "A" … "Z"
```

One space between slots. The body is the remainder of the header line.

| Form | Binding |
| --- | --- |
| `[priority] body` | open, no dates |
| `[priority] YYYY-MM-DD body` | open, creation date |
| `x [priority] body` | closed, date unknown |
| `x [priority] YYYY-MM-DD body` | closed, close date |
| `x [priority] YYYY-MM-DD YYYY-MM-DD body` | close date, then creation date |

A further date-shaped token is body text. `(a)` is body text. `X ` is body text. `(B)->Submit` is body text, because the priority slot requires a space after `)`.

Preferred header when writing:

```
(A) 2026-10-12 body
x (A) 2026-10-30 2026-10-12 body
```

Writers at this profile always emit the priority and the creation date. Readers accept either slot's absence.

`(P)` after `x` is part of this convention. An upstream reader treats that `(P)` as prose. Write `pri:A` as well when an upstream reader must see the priority.

A byte sort of the prefix orders `(A)`–`(Z)` first, dated before bare text within one letter, then lines that lead with a creation date, then other open text, then `x`. Preferred closed records (`x (P) close-date …`) sort by priority, then by close date.

## 2. Tags

A tag is a whole token, a maximal run of non-space. A `+` or `@` inside a token stays prose (`2+2`, `soandso@example.com`).

| Form | Role |
| --- | --- |
| `+name` | project. `name` is one or more non-space characters |
| `@name` | context. Same rule |
| `key:value` | attribute. Split on the first `:`. `key` is non-empty and contains no `:`. `value` is the non-empty remainder and may contain `:` |
| `<locator>` | locator: path, URL, or other home. Both brackets are part of the token, and the inside has no spaces |
| category | `TODO`, `FIXME`, `BUG`, `XXX`, or `NOTE`, optional `-id`, optional trailing colon (`TODO:`, `FIXME-3:`) |
| `ident:` | record id. The token ends in `:`, the stem is not a category (`HTD-1:`, `SCRIPT-MPE-3:`) |

| Category | Use |
| --- | --- |
| `TODO` | work to do |
| `FIXME` | known breakage to repair |
| `BUG` | a defect |
| `XXX` | a doubt, a hazard, or a temporary hack |
| `NOTE` | a remark with no required change |

Attributes are an open set. `pri:A` repeats the priority slot. `due:YYYY-MM-DD` is a due date. `src_name:` and `src_line:` name a source line.

A record may carry any number of projects and contexts, anywhere in the body, including on continuation lines.

```
(A) 2026-10-12 Thank Mom for the meatballs +Family @phone
(B) 2026-10-12 TODO: replace syntastic with ALE <plugin/syntastic.vim>
(A) 2017-03-19 X-TRCK-1001: rewrite the layer @coverage due:2017-04-01
```

## 3. Cards

A record is one header plus an optional body. 
A body is an regular indented block, or nested list/tree of blocks.

A blank line ends the record. A line that is not indented and not a comment starts a new record.

```
(A) 2026-10-12 TODO: track feature and environment trees @build @dev \
  Orthogonal trees for features and environments, branches as history
  and tags as editions. The index lives on this card.
```

The body text normally follows the same free-text rules as the record description;
tags belong to the same record.

The `\<EOL>` joiner or *continuation markup* may be used in a way compatible with simple line-based preprocessors, ie. on each line;
or as a special reminder on only header lines to indicate in level 0-2 consumers of data that *there is*
follow-up body (but only via an alternative tool path or consumer).

A level 0–2 reader would treat each continuation line as its own record,
so it must get a filtered stream that depends on the exact use of this
continuation joiner per instance; as pseudo- or real line-continuation.


## 4. Homes

The established format touches on a tag format for projects.


| Path | Holds |
| --- | --- |
| `README.md` | project goals, kept short |
| `HACKING.md`, or a file under `doc/` | operations and development notes |
| `TODO.txt` | open records. `todo.txt` is the same role |
| `DONE.txt` | closed records. `done.txt` is the same role |
| comments and strings in source | inline category marks |

This project placement follows [CONVENTIONS+baseline.md](CONVENTIONS+baseline.md).

The canonical copy of a task is the card in `TODO.txt` or `DONE.txt`. An inline mark is a shadow: one category and a short phrase at the site. The card points at the site with `<locator>` or with `src_name:` and `src_line:`. The site can name the card by its record id.

Closing:

1. Keep the creation date. Add one when it is missing.
2. Write the close mark and the close date.
3. Move the header and its continuation from `TODO.txt` to `DONE.txt`.

## 5. Agent card

See [TODO.txt conventions](CONVENTIONS+tasks.md) for the canonical copy.

## 6. Tool cuts

| Level | A tool at this level can |
| --- | --- |
| 0 | list, edit, and count records as lines |
| 1 | sort and filter by open/closed, priority, and date |
| 2 | filter and group by project, context, category, attribute, and locator |
| 3 | show headers, and open one card with its body |
| 4 | move a card to `DONE.txt`, and pair a card with its inline shadow |

A writer reports the level it emits. A reader reports the level it preserves. A lower-level reader will split a card into lines and will miss `(P)` after `x`.
