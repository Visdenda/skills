# Page signals, radar updates and public rules

> Part of the ai-crawler-readiness skill. Back to https://visdenda.com/skills/ai-crawler-readiness/SKILL.md.
> Read this when the task matches: Choose between page signals and a full scan, attach a Search update, and read findings against the rule that produced them.

## Pick the cheap tool first

- `visdenda_check_page_signals` — one URL, five focused signals: canonical, noindex,
  meta preview, hreflang and structured data. Pass `checks` to narrow it. One request,
  no report, no scan quota: use it for repeated checks while fixing something.
- `visdenda_scan_page` — 20+ checks in one pass, a grade, findings and a seven-day report
  link. Pass `updateId` to attach one radar update's public checks and mark which affected
  checks the scan covers. It consumes one of the three daily scans, so do not re-scan after
  a one-line change; re-check the specific signal instead.

## Radar first when timing matters

- `visdenda_get_radar_updates` — recent P0/P1 Google updates with how many public checks
  each affects. No network request.
- `visdenda_get_radar_update` — one update by id or public slug: summary, official links,
  affected checks and the latest per-check verdicts.
- `visdenda_explain_rule` — one check by id: public title and summary, severity,
  requirement level, what it applies to, site types, page roles and the official source.

Order: update → affected checks → explain the one rule that matters → check the page.
Naming an update as a cause without checking whether its checks actually fail on this page
is a guess.

## Reading a signal

- A canonical pointing at another URL is not automatically wrong: consolidation is a
  legitimate choice. Say which URL wins and whether that matches intent.
- Noindex can arrive two ways: a meta-robots tag in the HTML, or an `X-Robots-Tag`
  response header. Both are checked here, and they can disagree across URLs; report which
  one you observed and on which URL, not just "noindex: yes".
- Hreflang tags alone do not prove a correct cluster; reciprocity and `x-default` matter.
- JSON-LD presence is not rich-result eligibility. Report the type found, not a promise.
- Findings filtered by `updateId` are a subset. No finding under a filter is not a clean
  site.

## Report

Page, signal or rule id, verdict, evidence, one change, and the exact call that would
verify it. Never turn "a check failed" into "traffic dropped".
