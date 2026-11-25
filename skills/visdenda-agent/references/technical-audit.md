# Technical audit: from finding to verified fix

> Part of the visdenda-agent skill. Back to https://visdenda.com/skills/visdenda-agent/SKILL.md.
> Read this when the task matches: Explain the finding, fix the page, re-check the rule, then decide what else changes.

## Read the latest run, not the whole history

`get_opportunities` ranks the latest completed run's failing rules by page count and expects
traffic at stake, and links each rule to a P0/P1 search update when the rule came from one.
`get_audit` with `view=findings` gives the per-page findings with status, severity, detail,
`evidenceKey` and `findingStateId`.

Use `warn` and `fail` differently:

- `fail` is a conclusion that the page violates the rule.
- `warn` is a signal worth review; it does not mean the same as fail.
- `na` means the rule could not be evaluated on that page. It is not a pass.

## Explain before editing

Call `get_audit` with `view=finding` for the rule title, category, stored detail,
documentation link and verification guidance. Quote the `evidenceKey` when you report the finding; it is how a human
can replay the evidence. Do not invent a cause beyond the stored detail.

## Fix in the right layer

- Page-level markup/content → edit the page.
- Template-wide issue → change the template, not one page.
- Crawl/index directive issue → check `get_page_report` and a live `check_url` (`view=diff`)
  before changing robots, canonical or hreflang.
- If the site is not verified, stop: `add_site` → `verify_site` (Google-bound sites skip
  manual verification), then run the audit again.

## Verify the rule

`findingStateId` comes from `get_audit` (`view=findings` or `view=finding`); it is the input
`verify_fix` needs. A null `findingStateId` means the finding has no open state, so there is
nothing to verify — report it closed instead.

After the change, run `check_url` with `view=diff`, `url` and `siteId`: it is read-only and diffs
the applicable rules against the latest completed run (fixed / regressed / still failing).
Then call `verify_fix` with the site and `findingStateId`, plus an idempotency key: it
commits the rule recheck. A pass resolves the finding as fix-applied; anything else records
the verdict and leaves it open.

`verify_fix` is a rule recheck. It is not a traffic experiment. For traffic effects, use the
28-day GSC window and say the result is not yet conclusive.

## Close the loop

- `get_audit` with `view=lifecycle` for backlog age and resolutions.
- `get_audit` with `view=compare` for what changed across two full runs.
- `get_site` with `view=status` for the next action and current run state.
- A full `run_audit` commits finding state; use it when the change is broad, not for every
  single-page edit.
