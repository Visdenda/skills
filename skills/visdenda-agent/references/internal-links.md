# Internal links and topic clusters

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Read the crawl snapshot, fix reachability and anchors, then re-run the audit.

## Read the graph

`get_links` with `view=page` (pass `url`) returns one page's neighbourhood: inlink and
outlink counts with example URLs, crawl depth, template cluster, the orphan flag, the top
anchor texts pointing at it, adoption candidates when it is an orphan, and the snapshot
coverage (`crawledPages`, `sitemapUrls`, `truncated`).

`get_links` with `view=anchor` (pass `anchor`) returns the anchor-text distribution: how often
a phrase is used, which pages use it, which pages it points at, and whether the anchor index
is available or truncated. Omit `anchor` to list the most used anchors.

`get_links` with `view=opportunities` (pass `url`) returns source-page candidates that have
inlinks but do not already link to the target, same template first, plus one suggested anchor.
Use it when you need a source page, not the target's current neighbourhood; after adding the
link, re-run the audit and confirm with `view=page`.

Both views read the latest audit crawl snapshot, not a live crawl. Report coverage with every
conclusion. A page outside the snapshot is `inSnapshot=false`, which is not "no links" — and a
site without a snapshot returns `na` with a `run_audit` next action, so run the audit instead
of concluding anything.

## Rules

- Link from a relevant, already-strong page to the target, inside body copy. Footer and sitewide
  blocks do not carry the same signal and mostly create noise.
- Keep anchor text descriptive and varied. Avoid exact-match stuffing: the same commercial phrase
  on every link is a pattern, not a recommendation.
- Link both ways only when both directions are genuinely useful to the reader.
- Do not build link schemes, do not advise paid links, and do not present internal links as a
  ranking guarantee.

## Decision order

1. Orphan or far from home → take the adoption candidates from `get_links` (`view=page`) and add
   the first natural inlink from a relevant hub.
2. Weak or generic anchors → add one natural anchor from a page that already covers the topic.
3. New page without a home → link it from its topic hub, then from one or two sibling pages.
4. After the edit → run `run_audit` so the snapshot updates, then confirm with `get_links`
   (`view=page`) and `get_links` (`view=anchor`).

A snapshot is a state, not proof of crawl completeness: when `truncated` is true or coverage is
low, say the graph is partial.
