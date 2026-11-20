# Agent discovery: llms.txt, MCP cards and readiness

> Part of the ai-crawler-readiness skill. Back to https://visdenda.com/skills/ai-crawler-readiness/SKILL.md.
> Read this when the task matches: What each discovery document proves, what it does not, and how to read a score.

## What the documents are for

- `llms.txt` — an optional map for agents: H1, one-line summary, link lists. Serve it as
  `text/plain` or `text/markdown`; an HTML page answering 200 is a soft 404.
- MCP server card plus OAuth discovery documents — tell a client how to reach the server
  and how to authorize. A card that points at a dead endpoint is worse than no card.
- `auth.md` — how a human or an agent obtains a credential.
- A2A agent card — what the agent does, its skills and its endpoint.
- None of these are ranking factors. They remove guesswork for clients that look for them.

## Checks in order

1. `visdenda_check_llms_txt` — missing file, soft 404, missing H1, broken links, shape.
2. `visdenda_check_mcp_server` — server card, protected-resource and authorization-server
   metadata, `auth.md` and the A2A card; one fix per failure.
3. `visdenda_check_agent_readiness` — up to eight probes per call (chosen from the full check set), a 0–100 score and per-check detail.
4. `visdenda_scan_page` — one page: meta, canonical, robots, sitemap, llms.txt, schema,
   hreflang and redirects; returns a seven-day report link.

## Interpret, do not worship the score

- A missing optional document is a decision. Say what the absence costs — an extra
  round-trip, or a client that cannot discover the server — before calling it a defect.
- A high score does not mean assistants cite the site; discovery is not usage.
- A low score with one broken critical document (a server card pointing at 404) matters
  more than several missing nice-to-haves.
