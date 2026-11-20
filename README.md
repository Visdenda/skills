# Visdenda skills

Agent Skills for SEO and AI-search work, published by [Visdenda](https://visdenda.com).
Installable folders, one job each.

> This repository is generated from the Visdenda monorepo. Do not edit it here;
> changes are overwritten on the next sync. Source of truth for the versions and
> digests below: [https://visdenda.com/skills.json](https://visdenda.com/skills.json).

Install page with the same two commands: [https://visdenda.com/skills](https://visdenda.com/skills).

## Skills

| Skill | Version | References | License | Digest |
| --- | --- | --- | --- | --- |
| `visdenda-agent` | 2026-11-20.1 | 8 | CC-BY-4.0 | `sha256:2b5c0893c7a943755c0fc6a1214966bd01e68dc53717e6a72af65c424801f6bf` |
| `ai-crawler-readiness` | 2026-11-20.1 | 2 | CC-BY-4.0 | `sha256:240be11fa49fab1609c8d4511d33dc57d1ade6570a27d1b9ac3d754fe362c254` |

### `visdenda-agent`

Analyze a site's SEO with Search Console and crawl evidence: declining pages, low-CTR queries, cannibalization, indexing gaps, technical findings and AI-crawler blind spots, then fix and re-verify. Use when the user asks why organic traffic changed, what to fix first, why a page is not indexed, or whether AI assistants can read the site. Requires a scoped Visdenda API key.

Read it: [`skills/visdenda-agent/SKILL.md`](skills/visdenda-agent/SKILL.md)

Install: copy `skills/visdenda-agent/` into your agent's skills directory, or point
the client at the folder after `git clone`:
```bash
git clone https://github.com/Visdenda/skills.git
```
No executable code ships in this repository.

### `ai-crawler-readiness`

Check whether AI crawlers and agents can actually read a site: robots.txt per bot (GPTBot, ClaudeBot, PerplexityBot), llms.txt shape, MCP discovery documents, an agent-readiness score and a single-page scan. Use when the question is AI visibility, bot blocking, llms.txt, or agent access. No API key.

Read it: [`skills/ai-crawler-readiness/SKILL.md`](skills/ai-crawler-readiness/SKILL.md)

Install: copy `skills/ai-crawler-readiness/` into your agent's skills directory, or point
the client at the folder after `git clone`:
```bash
git clone https://github.com/Visdenda/skills.git
```
No executable code ships in this repository.

## What is not here

- The MCP servers, API clients and SDKs live in their own packages
  ([MCP](https://www.npmjs.com/package/@visdenda/mcp-free-bridge),
  [agent-watch](https://www.npmjs.com/package/@visdenda/agent-watch)).
- The skills never contain credentials. `visdenda-agent` needs a scoped API key
  issued on the site; `ai-crawler-readiness` needs no key at all.

## Versioning

Each skill has its own `metadata.version`. History is the git log of this
repository; the digests above identify the exact bytes. A changed skill is a new
commit, and the site's `/skills.json` publishes the same digest.

## License

Skill text: CC BY 4.0. The Visdenda service and API remain proprietary. See
[LICENSE](LICENSE).
