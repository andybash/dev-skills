---
name: prime
description: Build comprehensive understanding of an unfamiliar or context-stale codebase by analyzing structure, docs, key files, and git state, then producing a scannable orientation report. Also resumes session continuity left by the `handover` skill — reads the active handover doc in full and frames the prioritized next tasks. Use this whenever the user wants to "prime", "get up to speed", "load project context", "orient yourself", "understand this codebase", "what is this repo", "resume", "pick up where we left off", onboard to a new repo, or starts a fresh session in a project you don't yet have context on — even if they don't say the word "prime".
---

# Prime: Load Project Context

## Objective

Build comprehensive understanding of the codebase by analyzing structure, documentation, and key files, then report it back so the rest of the session can proceed with full context. If a previous session left a handover, resume it *after* that understanding is built — the handover names branches, files, and decisions that only make sense once you know the codebase.

## Process

### 0. Enforce caveman mode (MANDATORY, FIRST STEP)

Before any analysis, invoke the `caveman:caveman` skill via the Skill tool to activate caveman mode for the whole session being primed. Default level: `full`. Do this even if caveman mode appears already active — re-invoking is idempotent and ensures the mode persists across the priming run and afterward. Do not skip this step.

### 1. Enforce honest-completion-claims (MANDATORY, IMMEDIATELY AFTER STEP 0)

Before any analysis, invoke the `honest-completion-claims` skill via the Skill tool to install the completion-claim discipline for the whole session being primed. Do this even if the skill appears already active — re-invoking is idempotent and ensures the rule persists. Do not start any analysis step below until both Step 0 and Step 1 have been invoked.

### 2. Analyze project structure

Use the Bash tool to list tracked files and sketch the tree. Run these and read the output:

- `git ls-files` — all tracked files.
- A depth-limited tree, ignoring noise: `tree -L 3 -I 'node_modules|__pycache__|.git|dist|build|.venv'`. If `tree` is not installed, fall back to `git ls-files | head -200` plus `find . -maxdepth 2 -type d -not -path '*/.*'`.

The goal is a mental map of top-level directories and where the real code lives — don't read every file.

### 3. Read core documentation

Read, where present:
- The PRD / spec file (PRD.md or similar).
- The global rules file (CLAUDE.md, AGENTS.md, or similar) — this often overrides defaults, so it matters most.
- README files at the project root and in major directories.
- Architecture docs.
- Database schema / migration files if the project has a database.

### 4. Identify key files

From the structure, pick and read the load-bearing files:
- Main entry points (main.py, index.ts, app.py, lib.rs, etc.).
- Core config (pyproject.toml, package.json, tsconfig.json, Cargo.toml).
- Key model / schema definitions.
- Important service or controller modules.

Prefer the few files that explain how the system fits together over many shallow reads.

### 5. Understand current state

Use the Bash tool:
- `git log -10 --oneline` — recent activity and development focus.
- `git status` — current branch and uncommitted work.

### 6. Session-continuity intake (LAST — runs only after Steps 2–5 give you project context)

This step is additive and comes last on purpose: a handover references branches, files, and decisions that are only legible once you've built the understanding above. Running it earlier would muddy both the context load and the handover read.

**If `MEMORY.md` has no `## Active baton` section (or there is no MEMORY.md): there is nothing to resume. This is the normal cold-prime case — do not search, do not flag it, do not mention it. You are done with analysis; go to the report.** A missing baton is expected, not an error.

`MEMORY.md` is auto-loaded, so its `## Active baton` section (if present) is already in your context. The baton fixes task **priority and order** — but it is an index ONLY and is NOT sufficient. The deep context lives in the dated handover doc it names, and you must read that doc IN FULL. Skimming it and inferring the rest is the exact failure this is built to prevent.

Only if `## Active baton` exists:

1. **Read the active handover doc in full.** Use the `Read` tool on the path the baton names. Read the *entire* file — if it paginates (>2000 lines), loop `offset` until EOF. Do NOT use `Grep` or partial reads to sample it; `Grep` is for locating code, and it is banned here precisely because it enables skim-and-infer.
2. **Read live-task dependencies in full.** For each currently-open task, open — in full, same rule — the memory files and spec/plan paths that task links. Scope this to *open tasks only*, not the whole archive, so "read everything" stays finite and actually gets done.
3. **Emit a read receipt before proposing anything.** From what you just read (not from the baton summary), enumerate:
   - every open task,
   - every "Do NOT redo" item,
   - every "NOT verified / risk" item,
   - every linked sub-doc you opened.
   If you cannot list all four, you skimmed — re-read. This converts "did you read it" from trust into a checkable artifact, consistent with honest-completion-claims.

## Output report

Provide a concise, scannable summary. Use bullet points and clear headers.

### Next tasks (from active baton) — ONLY if a handover was resumed

Lead with this section when Step 6 found an active baton. Reproduce the baton's task list in its exact priority order (P0 first), each with its why + concrete next action + done-criterion + blocker. Do not reorder or re-prioritize — the baton is authoritative for sequencing. State plainly which single task is next. Omit this whole section entirely when priming cold (no handover).

### Project overview
- Purpose and type of application.
- Primary technologies and frameworks.
- Current version / state.

### Architecture
- Overall structure and organization.
- Key architectural patterns identified.
- Important directories and their purposes.

### Tech stack
- Languages and versions.
- Frameworks and major libraries.
- Build tools and package managers.
- Testing frameworks.

### Core principles
- Code style and conventions observed.
- Documentation standards.
- Testing approach.

### Current state
- Active branch.
- Recent changes or development focus.
- Any immediate observations or concerns.

Keep the report easy to scan — headers and bullets, not prose walls.
