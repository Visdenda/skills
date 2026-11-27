# Platform-aware fixes and CMS write paths

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Read the write path before proposing; only ship fields the platform can write.

## Read the write path first

Before proposing or applying a title, meta or content change, call `get_site` with `view=cms`.
It returns the detected platform (or null), every CMS connection's declared capabilities
(`writableFields`, `publishModel`, `canListPages`, `canReadDraft`, `canRollback`,
`agentApplyEnabled`, `probe`) and one `writePath`:

- `agent-apply` — an active connection has agent apply on. Propose, preview, then apply.
- `proposal-only` — the connection is active but agent apply is off. Propose; a human applies
  in the console. Do not call `apply_fix`.
- `reconnect-required` — no connection is active. Ask the owner to reconnect; do not promise
  a write.
- `code-or-manual` — there is no CMS connection. Implement the change in code or the site's own
  admin. Framework, headless, custom and other self-built sites (Next, Astro, etc.) all share
  this path; `apply_fix` cannot write here.

The platform snapshot is detection, not permission: the connection provider and its
`writableFields` are authoritative. A WordPress `probe=skipped` result is conservative;
preview confirms meta/canonical/robots before anything is written.

## Capability matrix

| | WordPress | Webflow | Shopify | Code / custom |
| --- | --- | --- | --- | --- |
| Always writable | title, content | title, seo_title, meta_description, content | seo_title, meta_description, content | Whatever the codebase deploys |
| Conditional | SEO title, meta description, canonical, robots, OG with companion plugin + supported SEO plugin; Elementor body via plugin | CMS collection item needs an explicit field mapping | Products/collections use native SEO; pages/articles use global metafields | — |
| Never writable | H1 (only post_title) | canonical, robots, OG on collection items | H1, canonical, robots | No CMS connection |
| Publish model | immediate | apply then publish (separate or site-wide) | immediate, plan/subscription gated | deploy pipeline |
| Listing / draft | plugin 1.3.0+ | list pages, read drafts | neither | — |
| Rollback | yes | no | no | via code history |

## Fields, publish and verification

- Use the exact names in `writableFields`. Do not translate `seo_title` into `title` or invent
  a canonical field; unsupported fields are manual work, not a failed write.
- Respect `publishModel`: Webflow writes land in the saved state, so apply without publish is
  not a shipped fix. A site-wide publish affects more than one page; confirm the scope.
- Preview first: it returns the remote current values, the values to write and the unsupported
  items. A stale remote value means preview again; never overwrite a concurrent edit.
- After a write, read back. `verify_fix` only rechecks audit rules and declared fields; it is
  not a ranking or traffic result. Use the 28-day outcome window for the effect.
- If `canRollback` is false, do not promise a rollback; keep the before/after values in the
  report.

## Boundaries

- Do not edit themes, Liquid, Webflow Designer, checkout, custom code or plugin files.
- A capability is a declaration: sanitizers, plan limits and remote drift can still reject or
  change a write. Report the actual read-back, not the intent.
- No platform path justifies a ranking guarantee or a "fixed" claim before the read-back.
