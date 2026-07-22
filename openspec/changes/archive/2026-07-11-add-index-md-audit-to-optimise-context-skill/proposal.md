## Why

The `/optimise-claude-context` skill audits six areas of Claude context loading but misses `openspec/INDEX.md` — the tiered loading mechanism that reduces session token cost by ~95% (from ~9,859 tokens to ~401 tokens) for exploratory sessions. Without this check, repos using OpenSpec silently pay full spec-load cost every session, and there is no tooling to flag or fix it.

## What Changes

- Add a new audit step **1g** to the `optimise-claude-context` skill that checks whether `openspec/INDEX.md` exists and whether it is stale
- Add a corresponding **fix step** that generates `openspec/INDEX.md` from the first requirement header of each `openspec/specs/*/spec.md`
- Include the INDEX.md status in the audit report table and before/after summary

## Capabilities

### New Capabilities

- `openspec-index-audit`: Audit and fix `openspec/INDEX.md` presence and staleness within the `optimise-claude-context` skill

### Modified Capabilities

<!-- None — no existing specs to modify; this is a new skill capability -->

## Impact

- Only `skills/optimise-claude-context/skill.md` is modified
- Only affects repos that have an `openspec/` directory — repos without it skip this step silently
- No breaking changes to existing audit steps
