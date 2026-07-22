# openspec-index-audit Specification

## Purpose
Audit and generate `openspec/INDEX.md` within the `optimise-claude-context` skill, enabling tiered context loading that reduces exploratory session token cost by ~95% in repos using OpenSpec.
## Requirements
### Requirement: Audit openspec/INDEX.md presence and staleness

The skill MUST check for `openspec/INDEX.md` as step 1g when auditing a repo.

#### Scenario: repo has no openspec/ directory
- Given a repo with no `openspec/` directory
- When step 1g runs
- Then the skill skips this step silently and does not report it

#### Scenario: openspec/ exists but INDEX.md is missing
- Given a repo with an `openspec/specs/` directory but no `openspec/INDEX.md`
- When step 1g runs
- Then the skill flags INDEX.md as missing with HIGH impact (~9,458 tokens wasted per exploratory session)

#### Scenario: INDEX.md exists and is up to date
- Given `openspec/INDEX.md` exists and no spec file is newer than it
- When step 1g runs
- Then the skill reports INDEX.md as present and current

#### Scenario: INDEX.md exists but is stale
- Given `openspec/INDEX.md` exists but one or more `openspec/specs/*/spec.md` files have a newer modification time
- When step 1g runs
- Then the skill flags INDEX.md as stale

### Requirement: Generate openspec/INDEX.md as a fix

When INDEX.md is missing or stale, the skill MUST offer to generate it.

#### Scenario: fix generates INDEX.md from spec headers
- Given the engineer approves the fix
- When the fix runs
- Then the skill reads the first requirement heading from each `openspec/specs/*/spec.md` and writes `openspec/INDEX.md` with one entry per capability: capability name, requirement heading, and file path

#### Scenario: no spec files found
- Given `openspec/specs/` is empty or has no spec.md files
- When the fix runs
- Then the skill writes an empty INDEX.md with a header comment and reports zero entries generated

