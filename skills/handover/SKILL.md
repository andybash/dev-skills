---
name: handover
description: Write end-of-session continuity so the next session's `prime` resumes with zero loss — a dense dated handover doc (deep context) + a tiny always-current ordered task baton in MEMORY.md + pruned durable memory. Pairs with the `prime` skill. Use this whenever the user wants to "write a handover", "hand off", "wrap up the session", "checkpoint", "save session state/context", "end of session", "I'm done for today", "prepare for next session", or is about to `/clear`, `/compact`, or run low on context — even if they don't say the word "handover". This exists to BEAT compaction: compaction lossily summarizes; this captures verified state, ordered next tasks with priority + why, and nuance, deliberately.
---

# Handover: Session Continuity

Write three artifacts with separate lifecycles so the next `prime` cannot misread the baton:

1. **Dated doc** `docs/superpowers/handover-<date>-<slug>.md` — deep context. One per session, never overwritten, unlimited size, self-contained (every task-critical fact present, nothing left to infer).
2. **Active baton** — a `## Active baton` section in `MEMORY.md`. Tiny, fully rewritten each time. The ordering authority: which tasks next, in what order, why, which doc to read. Index, not detail.
3. **Durable memory** — `MEMORY.md` index + `*.md` files. Updated + pruned.

Write all three in **caveman-lite style** (drop articles/filler/hedging; keep every number, path, identifier, tag, commit, file:line; fragments fine; no narrative). Do not invoke the caveman skill — that changes the live session mode, which is not yours to touch. Dense artifacts stay cheap to read in full, which is the point.

## Steps

**1. Reconstruct via ground truth, not memory.**
- Branch + commit: `git rev-parse --abbrev-ref HEAD` and `git rev-parse --short HEAD`. Use the literal values; never guess the branch.
- `git status`, `git diff --stat`, `git log --oneline -15`. Quote real test output if tests ran.
- If the session narrative conflicts with git (claimed a commit/fix that isn't there), record the discrepancy under "NOT verified" — do not launder it into "shipped".
- Invoke `honest-completion-claims` if available (skip if it errors). Either way, split **verified** (with evidence) from **unverified** (with how-to-verify).

**2. Resolve memory dir + date.**
```bash
MEM="$HOME/.claude/projects/$(pwd | sed 's#[/.]#-#g')/memory"
ls "$MEM/MEMORY.md" 2>/dev/null || ls -d "$HOME"/.claude/projects/*"$(basename "$(pwd)")"*/memory 2>/dev/null
DATE=$(date +%F)
```
No memory dir → still write the dated doc, but put the baton at its top and tell the user memory-backed continuity is unavailable.

**3. Write the dated doc** (`docs/superpowers/handover-<DATE>-<slug>.md`; mirror existing repo handovers — tables, inline `code`, file:line, exact tags). Tangential depth → link a spec/plan, don't paste.

```markdown
# Handover <DATE> — <theme>
> Deep context. `prime` reads this top-to-bottom in full. Ordering in MEMORY.md "Active baton".

## TL;DR
<where things stand; exact tags/commits/branch>
## State now
- Branch <name> (clean|dirty) · last <sha> "<msg>"
- Deployed/live: <tags; live vs paper vs local>
- Tests: <real status>
## Shipped + verified
| What | Where (commit/file) | Evidence |
## NOT verified / risk
- <claim> — verify: <cmd>
## Decisions (with why)
- <decision> — because <reason>; rejected <x> (<why>)
## Do NOT redo
- <dead end> — <why failed>
## Open tasks (full detail; order lives in baton)
### T1 <title>
- Why / Do / Where (files:line, specs) / Done when / Depends on
### T2 ...
## Links
- Spec/plan: <paths> · Memory: <bare filenames>
```

**4. Write the baton** — overwrite `## Active baton` in MEMORY.md (create near top if absent). Carry only open tasks, ranked.

```markdown
## Active baton — next tasks (ORDERED, top = do first)
> Index ONLY. Next session MUST read the active handover doc IN FULL before acting.

Active handover: docs/superpowers/handover-<DATE>-<slug>.md
Branch: <name> (<clean|dirty>) · last <sha>
State: <one line>

1. **P0 — <task>** — why <1 line> · do <action> · where <doc T1/spec> · done <criterion> · blocker <none|...>
2. **P1 — <task>** — ...
```

**5. Update + prune memory.**
- New durable facts → one `.md` each (schema: frontmatter `name` / `description` / `metadata.{node_type: memory, type, originSessionId}`; body = thesis + `**Why:**` + `**How to apply:**`/findings; `[[cross-link]]`). Add a one-line pointer in `MEMORY.md`.
- Prune: demote finished `Recent ships` → `Older ships`; delete done `Open follow-ups` lines; delete memory files only when a newer file says `Supersedes [[old]]`. Old dated docs stay as archive.

**6. Gate (re-read before claiming done).** Task order unambiguous (P-levels + numbering + deps). Every open task has why/do/where/done/blocker. Verified ≠ unverified; no unproven claim in "Shipped". Baton marked index-only + names the doc. Branch/sha from git. Links resolve. Pruning done. Then report: doc path, baton task count, what was pruned, open unverified items.
