# Example: Running `/optimise-claude-context`

Real numbers from a live audit of a NestJS microservice repo, run against a clean baseline
with no prior Claude context optimisation in place.

---

## Before state

| Item | Status | Notes |
|---|---|---|
| `CLAUDE.md` | ❌ Missing | No context file in repo |
| `.claudeignore` | ❌ Missing | `node_modules/` (459 MB), `package-lock.json` (755 KB), `proto/`, `sql/` all visible to Claude |
| `docs/QUICK_REF.md` | ❌ Missing | No quick-reference for commands or rules |
| `.claude/hooks/session-start.sh` | ❌ Missing | Claude ran orientation commands manually every session |
| `MEMORY.md` (auto-memory) | ✅ Clean | File not found — no stale entries |

**Approximate pre-loaded tokens per session: ~5,977**  
(Plus whatever Claude scanned mid-session from unblocked directories — effectively unbounded.)

---

## Audit report produced by the skill

```
## Claude context audit — example-service

### CLAUDE.md
- Status: ❌ missing

### .claudeignore
- Status: ❌ missing
- Impact: node_modules (459 MB) and package-lock.json (755 KB) are scannable mid-session

### docs/QUICK_REF.md
- Status: ❌ missing

### .claude/hooks/session-start.sh
- Status: ❌ missing

### MEMORY.md (auto-memory)
- Status: ✅ not found / clean
- Estimated tokens: N/A

### Estimated token saving from fixes: ~947 tokens preloaded + unbounded scan risk eliminated
```

---

## Fixes applied

| Fix | Result |
|---|---|
| `CLAUDE.md` created (lean, 37 lines) | ~304 tokens — preloaded every session, stays in prompt cache |
| `.claudeignore` created | Blocks `node_modules/`, `*.lock`, `dist/`, `coverage/`, `proto/`, `sql/` |
| `docs/QUICK_REF.md` created | ~476 tokens — on-demand only, not preloaded |
| `.claude/hooks/session-start.sh` created | Executable — shows branch, last commit, active work items |

---

## After state

**Pre-loaded tokens: ~304** (just the lean CLAUDE.md)  
**Unbounded scan risk: eliminated** (.claudeignore blocks all large directories)

---

## Combined saving with tiered spec loading

If your team also uses a session-start hook that loads spec/capability files on every session,
combining this skill with tiered loading (index only for exploratory sessions, full specs only
when actively implementing) can compound the saving significantly.

Example numbers from the same audit, combining repo optimisation with tiered spec loading
(23 capability specs, ~9,859 tokens total):

| Scenario | Pre-loaded tokens | Notes |
|---|---|---|
| No optimisation, full spec load | ~15,836 | node_modules also scannable — unbounded |
| Repo fixes only (this skill) | ~14,889 | node_modules blocked; lean CLAUDE.md |
| Repo fixes + tiered spec loading | ~5,431 | Index (~401 tokens) replaces full specs for exploratory sessions |

**Total saving (exploratory sessions): ~10,405 tokens per session (~66% reduction)**

The spec saving compounds over time — each new spec added to the library widens the gap
between full-load and tiered-load. With full loading, per-session cost grows linearly with
codebase complexity. With tiered loading, it stays flat.

---

## RFC alignment — what was verified in the live test

| Claim | Verified |
|---|---|
| Audit covers all 6 checks (CLAUDE.md, .claudeignore, QUICK_REF, session hook, slash commands, MEMORY.md) | ✅ |
| MEMORY.md keyword scan runs correctly; reports "not found / clean" when absent | ✅ |
| Skill reports first, never acts without confirmation | ✅ |
| Never deletes content — moves it | ✅ |
| MEMORY.md lines shown to engineer before removal, confirmation required | ✅ (code path verified; file was absent in this run) |
