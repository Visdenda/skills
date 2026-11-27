# Prioritize, report and hand back

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Use server-side ranks and evidence; separate observation, inference and recommendation.

## Ranking rules

Use the ranking the server already produced:

- `get_site_insights` sorts by severity, then impact metric and at-stake amount.
- `get_opportunities` ranks audit failures, GSC striking distance, decay, cannibalization and
  competitor gaps with one score; archived-month clicks and conversions set the weight.
- `get_site` with `view=health` is the score view: 0–100 with four explainable penalties (open
  findings, backlog age, audit freshness, coverage). Use it to summarize, not to rank items.

If you re-rank, state the formula and the inputs. Do not invent weights or a "SEO score".
Do not silently drop a high-severity item because it is inconvenient; explain the trade-off.

## Choose a batch

- 3-5 pages per round, not the whole backlog. Bigger batches make every change unattributable.
- Take revenue pages, then pages already near a ranking threshold; the page role and the
  opportunity score are the evidence.
- One primary change per page. If you cannot name the single change, the page is not ready.
- Write the baseline before editing: window, clicks, impressions, average position, CTR and
  sampled query coverage, plus the hypothesis, via `record_worklog_entry`.
- Re-read the same window after 28 days.
- Before recommending a CMS write, read `get_site` (`view=cms`): only `writableFields` can ship.
  No connection (framework, headless, custom) means code or manual work, not `apply_fix`.

## Success metrics

Judge a round on clicks, impressions, sampled query coverage, average position and CTR, read
from the same window before and after. These are observations, not a causal proof: ranking,
seasonality and SERP features move together, so name the confounders you could not rule out.

## Report shape

Use this order for every recommendation:

1. **Observation** — what the data says, with the page/query, window and numbers.
2. **Inference** — what you think it means, and how confident you are.
3. **Recommendation** — one concrete change, the page/template it touches, and why now.
4. **Impact** — the at-stake metric and method returned by the tool, or "not estimated".
5. **Verification** — rule recheck or effect window, and what result would change the conclusion.
6. **Not evaluated** — sources/types that could not be checked, with reasons.

Never present an inference as an observation. Never cite a number the tool did not return.

## Revenue first

If a page is a money page (product, pricing, tool, service), prefer fixes that protect or
recover its traffic over cosmetic issues on low-value pages. The insight's page role and the
opportunity rank are evidence; the final call belongs to the user.

## Hand-back

- State what you changed and where.
- Link the finding/evidenceKey or insight id.
- Say what remains open and what would trigger a re-check.
- If a write is needed, ask for confirmation; never run a destructive or quota-consuming
  action just because it is the next step in a loop.
