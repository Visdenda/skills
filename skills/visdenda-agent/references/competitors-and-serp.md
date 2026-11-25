# Competitors and SERP research

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Turn competitor changes into coverage gaps, not copycat pages; price research before buying.

## Competitor objects are separate from owned sites

Competitor sites live under a parent owned site. Use the competitor tools with the competitor
site id, not the parent id. If the key cannot see the competitor, say so; do not substitute a
different domain.

- `get_competitor` with `view=brief` — begin with this: the deterministic 30-day brief across
  every competitor under one owned site — new page counts, coverage, recurring themes, top gaps,
  publishing pace.
- `get_competitor` with `view=changes` or `view=feed` — the dated change list (new, removed,
  lastmod, content-changed), then page text and coverage for those pages.
- `get_competitor` with `view=gaps` — terms/pages where the competitor is present and the owned
  site is not.
- `get_competitor` with `view=rank` — latest keyword-compare positions for the owned site and
  each competitor.
- `get_competitor` with `view=discovery` — candidate competitors appearing in your tracked
  keywords' top 10.

## From change to meaning

A new competitor page is an observation. It becomes an inference only when it maps to a query,
intent or funnel stage the owned site already serves or wants to serve.

Check, in order:

1. Is the page in the competitor's sitemap and reachable?
2. What query intent does it target (product, comparison, guide, tool)?
3. Does the owned site already have a page for that intent?
4. Is the gap a missing page, a weaker page, or a different format?

Recommend a coverage decision, not a copy. If the competitor page is thin or off-strategy,
say that instead of manufacturing a task.

## SERP research is paid

`get_serp_results`, `get_keyword_metrics`, `research_keywords` and `check_ai_visibility`
call a paid provider. Call `get_research` with `view=estimate` first, show the estimate, and wait for
confirmation. Reuse cached results; do not run the same query twice.

`get_serp_results` is a snapshot, not a rank tracker. Report the measured position and the
snapshot date. "Not in top 10" is a measurement result, not a permanent ranking.

## Report

- What changed and when (with page/URL).
- Which owned page/query it affects.
- Whether the owned site has a gap, and the smallest page change that closes it.
- What evidence would change the recommendation.
- Cost/credits spent, if any.
