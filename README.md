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
| `visdenda-agent` | 2026-11-28.1 | 10 | CC-BY-4.0 | `sha256:6ce8427350cb7c203cf7ac1f359685c087b85eba00f52bb2c76662b0a261796f` |
| `ai-crawler-readiness` | 2026-11-28.1 | 4 | CC-BY-4.0 | `sha256:ee6b8bd88957b7ae84781b177f446da65326c95b197d9c0118e38634658bba01` |

### `visdenda-agent`

Analyze a site's SEO with Search Console and crawl evidence: declining pages, low-CTR queries, cannibalization, indexing gaps, technical findings, internal-link and topic-cluster gaps, CMS write capability, and AI-crawler blind spots, then fix and re-verify. Use when the user asks why organic traffic changed, what to fix first, why a page is not indexed, how pages should link to each other, what can actually be edited on the site, or whether AI assistants can read the site. Requires a scoped Visdenda API key.

Read it: [`skills/visdenda-agent/SKILL.md`](skills/visdenda-agent/SKILL.md)

Install: copy `skills/visdenda-agent/` into your agent's skills directory, or point
the client at the folder after `git clone`:
```bash
git clone https://github.com/Visdenda/skills.git
```
No executable code ships in this repository.

### `ai-crawler-readiness`

Verify that AI crawlers and assistants can actually reach and read a site, audit one page against the current public checks, and prove what changed since the last scan: re-run a full audit of the same URL with compareTo for added, resolved and persisting findings plus the score delta. Checks cover robots.txt per bot (GPTBot, ClaudeBot, PerplexityBot), sitemap.xml, llms.txt, MCP discovery and agent readiness. Use when the question is AI visibility, bot blocking, llms.txt, sitemap, MCP discovery or agent access — or whether a fix actually worked. No API key.

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

## Claude plugin bundle

The repository root also carries `.claude-plugin/plugin.json`, so the
[Claude Directory](https://claude.ai/directory) can install both skills as one plugin.
Folder install from `skills/<name>/` keeps working, and is all a non-Claude client needs.

## Versioning

Each skill has its own `metadata.version`. History is the git log of this
repository; the digests above identify the exact bytes. A changed skill is a new
commit, and the site's `/skills.json` publishes the same digest.

## License

Skill text: CC BY 4.0. The Visdenda service and API remain proprietary. See
[LICENSE](LICENSE).
