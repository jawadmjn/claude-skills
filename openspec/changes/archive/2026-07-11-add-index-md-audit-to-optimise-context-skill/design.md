## Context

The `optimise-claude-context` skill audits six areas (CLAUDE.md, .claudeignore, QUICK_REF.md, session hook, slash commands, MEMORY.md) but has no awareness of `openspec/INDEX.md`. The INDEX.md is the tiered-loading mechanism from the companion plugin — when present, exploratory sessions load ~401 tokens instead of ~9,859. Without it, every session in an OpenSpec repo pays full cost silently.

Two files need updating: `skills/optimise-claude-context/skill.md` (the skill itself) and `README.md` (the audit item list and background section).

## Goals / Non-Goals

**Goals:**
- Add step 1g to the skill: detect `openspec/` presence, check INDEX.md existence and staleness
- Add a fix step: generate INDEX.md from spec headers
- Include INDEX.md in the audit report table and before/after summary
- Update README.md audit item list to include the new check

**Non-Goals:**
- Changing how INDEX.md is consumed at session start (that lives in the hook, not this skill)
- Validating INDEX.md content correctness beyond staleness detection

## Decisions

**Staleness detection via file modification times**
Compare `openspec/INDEX.md` mtime against the newest `openspec/specs/*/spec.md` mtime using `find ... -newer`. Simple, no state file needed, works cross-platform on macOS/Linux.

**Skip silently when no openspec/ directory**
Repos without OpenSpec should see zero noise. The step checks for `openspec/specs/` first — if absent, it skips with no mention in the report. This keeps the skill general-purpose.

**INDEX.md format: one line per capability**
Each entry: `- **<capability-name>**: <first requirement heading> — \`openspec/specs/<capability-name>/spec.md\``
Mirrors what the companion plugin generates, so a skill-generated index is compatible with the hook's loader.

**README update scope**
Only update the audit item list in the `What it does` section and the token figure in the Background section (which already references the ~9,859 / ~401 split). No structural changes to the README.

## Risks / Trade-offs

- [Risk] First-requirement heading may be generic (e.g. "Core behaviour") → Mitigation: use as-is; quality depends on spec authoring, which is enforced upstream by the SDD review process
- [Risk] `find -newer` mtime comparison can be fooled by `touch` or git checkouts → Mitigation: acceptable for a best-effort audit tool; false positives just trigger a harmless regeneration

## Migration Plan

No migration needed. Both files are additive edits. Skill change is backward-compatible — existing repos without `openspec/` are unaffected.
