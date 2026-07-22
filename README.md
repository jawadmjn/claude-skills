# claude-skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#contributing)

A collection of general-purpose Claude Code skills for developer productivity and AI session optimisation. Skills here work in any repo, with any tech stack, regardless of whether you use a plugin.

## Table of contents

- [Skills](#skills)
- [How to install a skill](#how-to-install-a-skill)
- [Background](#background)
- [Contributing](#contributing)
- [License](#license)

---

## Skills

### [`/optimise-claude-context`](skills/optimise-claude-context/skill.md)

Audit and fix Claude Code context loading in any repository.

**What it does:** Runs a structured 4-step audit:

1. **Audit** — checks CLAUDE.md token size (target ≤800), `.claudeignore` coverage, `docs/QUICK_REF.md`, session-start hook, slash command gaps, whether `MEMORY.md` (Claude's auto-memory file) contains entries that duplicate context already managed by hooks, and whether `openspec/INDEX.md` exists and is current (saves ~9,458 tokens per exploratory session when present)
2. **Report** — presents all findings before touching anything
3. **Fix** — applies only flagged items; never deletes content silently — CLAUDE.md content is moved, MEMORY.md entries are shown first and removed only after confirmation
4. **Summary** — before/after table with estimated token savings

**Why it matters:** Claude Code loads `CLAUDE.md`, session-start hook output, and the first 200 lines of `MEMORY.md` on every session. In practice, repos accumulate 2,000–10,000 tokens of context that Claude never needs for most tasks. This skill surfaces the waste and fixes it in one pass.

See [examples/context-audit-example.md](examples/context-audit-example.md) for a real before/after.

---

## How to install a skill

**Option 1 — Copy into your repo**

```bash
cp skills/optimise-claude-context/skill.md /path/to/your-repo/.claude/skills/optimise-claude-context.md
```

Then register it in your `.claude/settings.json`:

```json
{
  "skills": [
    ".claude/skills/optimise-claude-context.md"
  ]
}
```

Invoke with `/optimise-claude-context` in any Claude Code session.

**Option 2 — Reference directly from CLAUDE.md**

Paste the skill content into a new file in your repo and reference it from your `CLAUDE.md`:

```markdown
## Skills
- Context optimisation: see `.claude/skills/optimise-claude-context.md`
```

---

## Background

This repo is meant to grow into a general library of Claude Code skills — it started with the one below, born out of a specific problem, but isn't limited to it. Contributions of new, unrelated skills are welcome.

The first skill, `/optimise-claude-context`, grew out of research into Claude Code context bloat, where a production repo was loading ~9,859 tokens of spec files on every session — including exploratory sessions where none of those specs were relevant. Combined with a missing `.claudeignore`, build artefacts and lock files were also scannable mid-session, making actual per-session token cost effectively unbounded.

The fix involved two things: tiered spec loading (a hook-level change) and a general-purpose audit skill that any team can run in any repo. This repo contains the general-purpose skill, extracted and generalised for public use.

I built this after noticing that Claude Code was quietly loading far more context than it needed on every session — spec files, a bloated CLAUDE.md, stale auto-memory entries — even for simple one-line questions. Responses were slower and costs were creeping up with no tooling to surface or fix it. If you're running Claude Code and wondering why sessions feel heavier than they should, this is for you.

Full write-up: [How I Cut Claude Code Token Costs by 66% Per Session](https://medium.com/@jawadnawaz/how-i-cut-claude-code-token-costs-by-66-per-session-ac525589b7bb)

— Jawad

---

## Contributing

Open a PR. Skills should be:
- **General-purpose** — no assumptions about specific plugins, frameworks, or internal tools
- **Prose-driven** — Claude performs the work; no shell scripts required
- **Safe by default** — never delete content without showing the user what will be removed and getting confirmation

---

## License

[MIT](LICENSE)
