# Search performance and opportunity analysis

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Windows, declines, CTR gaps and cannibalization without inventing causality.

## Establish the window

GSC final data lags about 3 days. A "last 28 days" read ends roughly 3 days ago; say that
when you report it. `get_gsc` with `view=overview` / `queries` / `pages` uses live GSC on
paid plans and has a daily live-read budget. On a free plan, use `get_gsc` with `view=monthly`
for an archived month instead of pretending the live window exists. Read the returned source and
`dataThrough`; never silently mix live and archive windows.

## Use the judgment layer first

`get_site_insights` already computes:

- `declining_page`: previous equal-length window vs current, clicks lost.
- `high_impressions_low_ctr`: impressions above the site median with CTR below the site median.
- `cannibalization`: one query with multiple pages, with revenue-page conflict flagged.

These are rule outputs, not verdicts. Treat `severity` as a starting order and read
`evidence` and `impact.method` before recommending anything.

## One-call answers

For a weekly question ("what changed this week?"), start with `get_gsc` (`view=weekly_report`)
instead of stacking per-dimension reads. The server pre-computes four digests so you do not
have to re-rank raw rows yourself:

- `get_gsc` with `view=quick_wins` — striking-distance queries (position 4-20, above-median impressions)
  with the page, CTR and potentialClicks measured against the site's own top-position CTR.
- `get_gsc` with `view=cannibalization` — queries split across competing pages: the leader's impression
  share and each page's position and clicks, ordered by clicks landing on non-leader pages.
- `get_gsc` with `view=weekly_report` — last 7 days vs the previous 7 in one read: totals with deltas,
  top-5 winning/losing pages, top-5 new/lost queries, top-3 quick wins and position movers.
  The report is co-occurrence data; never narrate a cause from it.
- `get_gsc` with `view=decay` — pages losing clicks across two equal windows (28/90d, vs previous or
  last year), labeled `decayed-content` (position held — a refresh is the actionable first
  step) or `ranking-drop` (position fell — a refresh will not fix it). Counts cover the
  full evaluated set even though steady rows stay out of the list.

All `get_gsc` views share one daily live-read cap on paid plans; one call counts as one upstream read
even when it fans out over several cached windows.

## Opportunity buckets

Four starting buckets, ordered by what usually pays first:

| Bucket | Signal | Action | First tool |
| --- | --- | --- | --- |
| A | Position 4-20, impressions above the site median | Protect and strengthen the page: title/snippet work or a small content gap | `get_gsc` (`view=quick_wins`) |
| B | Position 16-40 with real impressions | Deepen the content, add internal links, check intent match; do not estimate clicks from a CTR benchmark above position 20 | `get_gsc` (`view=queries`) + `get_page_report` |
| C | Position 1-10, high impressions, low CTR | Check SERP features, AI Overview presence, intent and the snippet before rewriting | `get_page_report` / `get_ai_visibility` (`view=aio`) |
| D | Queries newly appearing for a page | Decide whether to expand the existing page or build a new one | `get_gsc` (`view=query_coverage`) |

Buckets are a starting order, not a verdict. A page can sit in two of them; pick the one whose
action you can verify this round. When the server already ranks the opportunities, keep that
order instead of re-bucketing.

## Query rows are a sample

GSC returns top rows, not every query. Page-level totals are authoritative: never sum the named
query rows and call the result page impressions. Preserve the `truncated` and sample
disclosures the response carries, and say the row list is partial when it is. If a view cannot
be read at all, say which type was not evaluated instead of presenting a smaller list as the
whole picture.

## CTR is not a title verdict

Before recommending a title or snippet change, check the position bucket, the query intent, the
SERP features on that result and whether an AI Overview is present. Features and AI Overviews
can absorb clicks, so a CTR gap against the site's own median is not proof that the title is
bad. On positions 1-5 a low CTR is more often a result-page composition problem than a copy
problem. For a tracked query, cross-read `get_ai_visibility` (`view=aio`, pass `query`) before
calling it a copy problem; for a new query, `check_ai_visibility` is a paid sample and needs the
same cost confirmation as any research call.

## Batch and measurement discipline

- At most 3-5 pages per round. A wide batch makes every result unattributable.
- Before editing, record the baseline (window, clicks, impressions, average position, sampled
  query coverage) and the single hypothesis with `record_worklog_entry`.
- One primary change per page: title/snippet, content or links, not all three at once.
- Do not re-edit the same page's title or meta description within 28 days; a second edit
  destroys the comparison.
- Re-read the same window after 28 days, and use control pages when the site has enough traffic
  for them. "No conclusion yet" is a valid result.
- No ranking guarantees. Report the observation and the uncertainty that remains.

## New page or expand existing

- Same SERP and same intent → strengthen the existing page. It is usually cheaper and faster,
  and it avoids competing with yourself.
- Distinct intent with its own SERP and its own demand → new page.

Check `get_serp_results` for the query's current results and `get_competitor` (`view=gaps`)
for pages competitors have that you do not. Read the existing page's query distribution first:
a page already ranking for several close variants is a signal to expand, not to split. Never
mass-produce variant pages for keyword permutations.

## Drill down

- Page-level story → `get_page_report` for search, sessions, AI fetches, index verdict,
  findings and CWV in one read.
- Query-level story → `get_gsc` with `view=queries` for clicks, impressions, CTR, position and deltas.
- Page list → `get_gsc` with `view=pages`; use the deltas to separate growth, decline and new pages.
- Monthly history → `get_gsc` with `view=monthly` for an archived month when live data is unavailable.

## Rules of evidence

- A CTR gap is relative to the site's own median, not a universal benchmark. Do not claim
  "the title is bad" from CTR alone; check position, query intent and the landing page.
- A decline is not automatically an algorithm update. Check `get_radar_updates`,
  seasonality, tracking changes and page changes before naming a cause.
- Cannibalization means pages compete for the same query, not that one must be deleted.
  Check revenue role (the tool flags it), internal links and canonical signals.
- If GSC is not bound, free-plan live reads are unavailable, or the daily cap is reached,
  say which type was not evaluated. Do not estimate clicks from impressions as a substitute.

## Recommend and verify

For title/meta or content changes, the effect window is weeks, not minutes. State the
expected window (usually 28 days), then re-read the same window. Use `verify_fix` only for
rule rechecks, not for traffic outcomes. "No conclusion yet" is a valid result.
