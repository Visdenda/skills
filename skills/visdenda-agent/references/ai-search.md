# AI search: crawlers, referrals and AI Overviews

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Read what AI bots actually did; do not treat an empty sample as absence.

## Start from observed behavior

`get_ai_visibility` with `view=traffic` reads probe data for the last 7, 28 or 30 days: hits by agent, previous-window
delta, markdown negotiation, machine-file reads, AI referral hosts and robots violations.
If the site has no probe data it returns `na` with a reason. That is not zero traffic and
not proof that AI bots ignore the site.

`get_site` with `view=crawler_gaps` adds the actionable layer: paths AI/search crawlers requested that 404/410,
paths blocked by robots or 403, and whether robots.txt / llms.txt / sitemap.xml were fetched.

## AI Overviews

`get_ai_visibility` with `view=aio` returns the latest AI Overview check for tracked queries and, on paid
plans, sampled presence for a limited set of queries. An empty trend means "not sampled",
not "absent". Do not generalize from one query sample to the whole site.

`check_ai_visibility` is a research call and may cost money. Call `get_research` with `view=estimate`
first, show the estimate, and only execute after the user confirms.

## CTR and AI Overviews

When a query has impressions but a low CTR, read its AIO status before blaming the title:
`get_ai_visibility` (`view=aio`, pass `query`) for tracked queries, or `check_ai_visibility`
for a paid sample of a new query. An AIO that answers the query can absorb the click even when
the page ranks well. Say the sample is limited; do not convert one AIO observation into a
site-wide CTR explanation.

## What to report

- Crawler access: which agents came, which paths were blocked or missing, and the evidence.
- Referrals: which AI hosts sent sessions and how the previous window compares.
- Machine files: whether robots.txt / llms.txt / markdown were read at all.
- AI Overview presence: only the sampled queries, with the sample window.

Never claim a ranking inside an AI answer, a citation rate, or "AI traffic is down" unless the
tool returned that exact evidence. If the plan does not include live sampling, say so.

## Action and verification

- Blocked path → unblock only when the user wants that agent; re-check with `get_site` (`view=crawler_gaps`).
- Missing path → create/redirect, then verify with a fresh probe read.
- llms.txt/robots gap → fix the file, then confirm crawler reads.
- AIO measurement → repeat the sample after the agreed window; do not promise a citation.
