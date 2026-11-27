---
name: visdenda-agent
description: "Analyze a site's SEO with Search Console and crawl evidence: declining pages, low-CTR queries, cannibalization, indexing gaps, technical findings and AI-crawler blind spots, then fix and re-verify. Use when the user asks why organic traffic changed, what to fix first, why a page is not indexed, or whether AI assistants can read the site. Requires a scoped Visdenda API key."
license: CC-BY-4.0
compatibility: Requires an MCP client with Streamable HTTP and a Bearer token.
metadata:
  version: "2026-11-26.1"
---

# visdenda-agent

> Let an AI agent read Visdenda's judgment layer, drill into the evidence, fix the site,
> and verify the fix. Full endpoint reference: https://visdenda.com/docs/api (live from
> https://visdenda.com/openapi.json). MCP alternative: https://visdenda.com/docs/mcp (same key, read-only by
> default; write tools need explicit confirmation).

## When to use

The user's own site needs SEO analysis, a fix decision, or a verified outcome: search
performance, indexing, technical findings, content decay, AI-crawler visibility, or a
competitor gap. Tie the answer to evidence, not a checklist.

## Do not use

- Do not use it on a site the key cannot access, or a competitor as an owned site.
- Do not run a paid research call before the user accepts the estimate.
- Do not claim a ranking factor, citation rate or traffic outcome the tool did not return.

## Auth

- Header: `Authorization: Bearer <YOUR_API_KEY>` (admin-issued; plaintext shown once).
- Any authenticated key may call `get_agent_skill`; other tools need one of `radar:read`,
  `rules:read`, `audit:read`, `sites:read`, `sites:write` (add_site / verify_site /
  record_worklog_entry), `audits:write` (run audits, import sitemaps, verify fixes),
  `research:read`, `research:write`.
- The free face (`ai-crawler-readiness`) needs no key.
- Missing key and insufficient scope both answer 401; do not retry without a new key.

## Start here

| Situation | First call |
| --- | --- |
| No site chosen, or only a domain | `list_sites` — attention order; resolve the domain to a `siteId`. |
| Key owns no sites | `add_site` → `verify_site` (Google-bound sites skip manual verification) → `run_audit`. |
| Run history or a delta | `get_site` with `view=status` — runs, changes, radar and GSC in one read. |
| Resuming earlier work | `get_site` with `view=worklog` — read what previous rounds changed before re-litigating a decision. |

## Work shape

Choose the smallest surface that answers the question; do not walk the whole tool list.

- **One fact, one page or one query** → one tool call. Name the call, report the result, stop.
- **A whole question about one site** → `get_site_insights`, then drill only with the chosen
  insight's `suggestedAction`.
- **One page's full evidence** → `get_evidence`. **Several pages of one site** →
  `get_opportunities` (`view=rank`), then `get_evidence` for the pages you keep.
- **Several sites** → `list_portfolio_opportunities` with 1-10 `siteIds`; do not loop
  `get_site_insights` once per site.
- **Beyond the tools** (hundreds of URLs, scheduled work) → use the REST API described by
  `/openapi.json` with bounded concurrency, a checkpoint file and one stable output file.
  Do not burn context looping tool calls.

## Task routing

Load the matching reference before the second call; do not load all eight.

| Task | Read this reference first | First call |
| --- | --- | --- |
| New site, or one with no history yet | [Triage a site before touching anything](references/triage.md) | `list_sites`, then `get_site_insights` |
| Traffic moved, or the user asks why | [Search performance and opportunity analysis](references/gsc-performance.md) | `get_gsc` (`view=weekly_report`) or `get_site_insights` |
| What to fix first | [Prioritize, report and hand back](references/prioritization-and-reporting.md) | `get_opportunities` |
| Audit details, findings, or a fix to verify | [Technical audit: from finding to verified fix](references/technical-audit.md) | `get_audit` (`view=findings`) |
| A page is not indexed | [Indexing and crawler gaps](references/indexing-and-crawl.md) | `get_page_report` |
| AI assistants, robots or llms.txt | [AI search: crawlers, referrals and AI Overviews](references/ai-search.md) | `get_ai_visibility` or `get_site` (`view=crawler_gaps`) |
| Competitor gap or SERP question | [Competitors and SERP research](references/competitors-and-serp.md) | `get_competitor` |
| Field meanings, `na`, contracts or quotas | [Data contracts: freshness, missing data and quotas](references/data-contracts.md) | `get_page_report` |

## Budget

- Prefer bundled reads over per-dimension calls: `get_site_insights`, `get_opportunities`,
  `get_evidence`, `get_site` (`view=status`), `get_gsc` (`view=weekly_report`),
  `get_page_report`.
- One network call per question unless the first is inconclusive; stop when the question is answered.
- GSC live reads share one daily cap; when it runs out, use archived data or mark the type not
  evaluated.
- After two failed reads of the same source, report the gap instead of retrying: name the source
  and what was not evaluated.
- Hand a human the `artifactUrl` (a 30-day read-only page with the same summary and evidence)
  instead of re-describing the result.

## Verify loop

1. `get_audit` with `view=findings` (or `view=finding`) → take the `findingStateId`.
2. `check_url` with `view=diff` and `siteId` → read-only pre-check; diffs against the last run.
3. `verify_fix` with that id → commits the rule recheck; a pass proves the change landed.
4. `get_audit` with `view=compare` → when the change is broad or spans pages/templates.
5. Outcome → read the effect window: 28-day baseline vs 14 days from the change day +3 (after GSC
   settles), with control pages when available.
6. `record_worklog_entry` → append what changed and cite the run/finding/page evidence,
   so the next round can read it with `get_site` (`view=worklog`) instead of re-deriving it.

A rule pass is not a traffic or ranking result; never present it as one.

A null `findingStateId` means no open state, so there is nothing to verify; report the finding
closed instead of re-running the check.

A full audit is a job, not a read loop. `run_audit` returns `runId`; when it is queued, the
same response also carries `queuePosition` and `queueEtaSeconds`. Poll `get_audit` with
`view=job` (`siteId` + `runId`) for status, progress, queue position/ETA, terminal counts and
`reportUrl`. Do not start a second run to check the first.

## Example

"What should I fix first on example.com?"

1. `get_site_insights` → take one `declining_page` insight; read its `evidence`, window and
   `impact` first.
2. `get_page_report` → search, audit, index and AI-fetch facts behind it.
3. `get_radar_updates` → check for an in-flight Search update before naming a cause.
4. Report observation → inference → recommendation → verification.

## Proposal loop

- `propose_fix` — when a change needs a human decision, write a pending proposal with a title,
  summary, rationale and 1–20 changes. It needs `audits:write` and never edits the site.
- A person approves or rejects it in the console. Approval authorizes the change, not execution:
  apply it yourself, then verify with `verify_fix`.
- `get_audit` with `view=proposals` — read the decision, including any rejection note, before
  proposing again. Expired proposals say expired; an identical pending proposal is deduplicated.

## References

- [Triage a site before touching anything](https://visdenda.com/skills/visdenda-agent/references/triage.md) — Pick the one question that matters, read the judgment layer, then drill.
- [Search performance and opportunity analysis](https://visdenda.com/skills/visdenda-agent/references/gsc-performance.md) — Windows, declines, CTR gaps and cannibalization without inventing causality.
- [Technical audit: from finding to verified fix](https://visdenda.com/skills/visdenda-agent/references/technical-audit.md) — Explain the finding, fix the page, re-check the rule, then decide what else changes.
- [Indexing and crawler gaps](https://visdenda.com/skills/visdenda-agent/references/indexing-and-crawl.md) — Unknown is not missing; a blocked or 404 path is a concrete task.
- [AI search: crawlers, referrals and AI Overviews](https://visdenda.com/skills/visdenda-agent/references/ai-search.md) — Read what AI bots actually did; do not treat an empty sample as absence.
- [Competitors and SERP research](https://visdenda.com/skills/visdenda-agent/references/competitors-and-serp.md) — Turn competitor changes into coverage gaps, not copycat pages; price research before buying.
- [Prioritize, report and hand back](https://visdenda.com/skills/visdenda-agent/references/prioritization-and-reporting.md) — Use server-side ranks and evidence; separate observation, inference and recommendation.
- [Data contracts: freshness, missing data and quotas](https://visdenda.com/skills/visdenda-agent/references/data-contracts.md) — What each source can and cannot prove; how to avoid turning absence into zero.

## Non-negotiables

- Fails before passes; attach `evidenceKey` (audit) or the insight `id` and `evidence`.
- `na`, empty and `notEvaluated` are results, not zeros. Name the unavailable source.
- Use the server's ranking; if you re-rank, state the formula and inputs.
- Writes need confirmation, a unique idempotency key, and the smallest scope.
- Respect 429 and `Retry-After`; failed reads do not count against the quota.
