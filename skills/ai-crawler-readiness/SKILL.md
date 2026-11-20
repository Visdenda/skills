---
name: ai-crawler-readiness
description: "Check whether AI crawlers and agents can actually read a site: robots.txt per bot (GPTBot, ClaudeBot, PerplexityBot), llms.txt shape, MCP discovery documents, an agent-readiness score and a single-page scan. Use when the question is AI visibility, bot blocking, llms.txt, or agent access. No API key."
license: CC-BY-4.0
compatibility: Requires an MCP client with Streamable HTTP; no credentials.
metadata:
  version: "2026-11-20.2"
---

# ai-crawler-readiness

> Anonymous checks for the AI-facing surface of a site. Endpoint: https://visdenda.com/v1/mcp/free
> (Streamable HTTP, no Authorization header). Tool names are prefixed `visdenda_`.
> Nothing here reads Search Console or account history; that needs a key — see
> https://visdenda.com/mcp.

## When to use

- "Can ChatGPT / Claude / Perplexity read this site?"
- "Why is GPTBot blocked, and what does robots.txt say per bot?"
- "Do we have llms.txt, and is it shaped right?"
- "Is our MCP server / agent card / auth.md discoverable?"
- "Score how agent-ready this site is" or "scan this one page".

## Do not use

- Do not use it to prove AI citations, AI traffic or rankings: it checks access and
  discoverability, not whether an assistant cited the page.
- Do not report a blocked bot as "invisible to AI" without naming the bot and the rule.
- Do not loop the network tools or the scan: five network checks and three scans per IP per day.

## Endpoint and limits

- https://visdenda.com/v1/mcp/free — anonymous, rate-limited per IP.
- Directory reads 100/day, network checks 5/day, single-page scans 3/day, 30 requests/minute.
- These are per-IP numbers. On a shared egress (for example, a hosted assistant) an aggregate
  cap can answer 429 sooner; stop and report the retry time.
- A 429 is a limit, not an error to retry; read `Retry-After`.

## Start here

1. `visdenda_get_radar_updates` — is a Search update in flight? It changes what a sudden
   drop means, and it costs no external fetch.
2. `visdenda_check_crawler_access` — robots.txt per crawler, the deciding rule and
   crawl-delay, from one fetch. With exactly one crawler, `probe: true` also fetches the
   page as that agent to catch a CDN/WAF block.
3. `visdenda_check_llms_txt` — missing file, HTML soft 404, missing H1, broken links,
   llmstxt.org shape.
4. `visdenda_check_agent_readiness` — up to eight probes, a 0–100 score and per-check detail.
5. `visdenda_scan_page` — one page: meta, canonical, robots, sitemap, llms.txt, schema,
   hreflang and redirects; returns a seven-day report link. Pass `updateId` to filter the
   findings to one radar update.

## Example

"Can ChatGPT read our pricing page?"

1. `visdenda_check_crawler_access` with `crawlers: ["GPTBot"]` and `probe: true` → the
   robots verdict plus the page response seen as that agent.
2. If blocked, name the deciding rule and the path; check `visdenda_get_ai_crawler` for the
   current token and ranges before advising a change.
3. Report bot + rule + path + status, then re-run the same call after the fix.

## Bot access loop

- `visdenda_match_user_agent` — paste a user-agent or a log line; exact, prefix and
  substring matches tell you which crawler actually hit the site.
- `visdenda_search_ai_crawlers` — search the directory by name, vendor, category or
  robots.txt token; `visdenda_get_ai_crawler` returns user agents, reverse-DNS suffixes,
  published IP ranges, sources and the review date.
- Separate search crawlers from training and user-triggered fetchers before advising a
  block: blocking a training bot does not remove the site from search answers, and
  blocking a search bot can.

## Discovery loop

- `visdenda_check_mcp_server` — MCP server card, OAuth discovery documents, `auth.md`
  and the A2A agent card, with a fix per failure.
- A missing optional document is a decision, not automatically a defect. Say what the
  absence costs before calling it a finding.

## Hand-off

Report what is blocked (bot + rule + evidence), what is missing (document + why it
matters), the readiness score with its failing checks, and the one fix with the highest
expected effect. Then re-run the same check.

## References

- [Bot access: robots, user agents and log evidence](https://visdenda.com/skills/ai-crawler-readiness/references/bot-access.md) — What robots.txt decides per bot, why a 403 is not a block, and how to prove a hit.
- [Agent discovery: llms.txt, MCP cards and readiness](https://visdenda.com/skills/ai-crawler-readiness/references/agent-discovery.md) — What each discovery document proves, what it does not, and how to read a score.

## Upgrade path

Owned-site history — Search Console windows, audit runs, indexing coverage, AI-crawler
fetches with paths — needs a scoped key: https://visdenda.com/mcp. The same surface, plus
`get_site_insights` and its drill-downs. Connect an account with
`visdenda_connect_account` when the user is ready to approve a six-digit code. It needs
the account owner's email and is not idempotent: call it once per attempt and do not retry
on timeout.
