# TODO.txt record conventions

One record is one card: a header line plus continuation lines.

Header: [x ][(P) ][YYYY-MM-DD ][YYYY-MM-DD ]body
P is A-Z. Write open cards as `(P) YYYY-MM-DD body`.
Write closed cards as `x (P) YYYY-MM-DD YYYY-MM-DD body` (close date, then creation date).
Also write `pri:P` when an upstream todo.txt reader must see the priority of a closed card.
On an open card a single leading date is the creation date. A second date-shaped word there is prose.

Tags are whole tokens: +project, @context, key:value (split on the first colon), <path-or-url>,
and TODO / FIXME / BUG / XXX / NOTE with optional -id and an optional trailing colon.
A token such as HTD-1: is a record id.
`2+2` and `a@b.example` are prose.

Put long text on following lines indented by two spaces. A trailing \ also continues the card. A blank line ends it.
Lines whose first non-space character is # are comments.

Canonical tasks live in TODO.txt (todo.txt is the same file role).
On close, add the close mark and the close date, and move the whole card to DONE.txt.
Inline TODO/FIXME/BUG/XXX/NOTE marks in source are shadows. Keep each to one short phrase.
Point the card at the site with <locator> or src_name: and src_line:.
Goals stay in README.md. Ops and dev notes stay in HACKING.md or doc/.
Keep discussion on the card.

Leave a record's existing shape alone unless asked to rewrite it. A line that skips priority or a date is still a record.
