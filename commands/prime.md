---
description: Prime agent with codebase understanding
---

# Prime: Load Project Context

## Objective

Build comprehensive understanding of the codebase by analyzing structure, documentation, and key files.

## Process

### 0. Enforce caveman mode (MANDATORY, FIRST STEP)

Before any analysis, invoke the `caveman:caveman` skill via the Skill tool to activate caveman mode for the entire session being primed. Default level: `full`. Do this even if caveman mode appears already active — re-invoking is idempotent and ensures the mode persists across the priming run and afterward. Do not skip this step.

### 1. Enforce honest-completion-claims (MANDATORY, IMMEDIATELY AFTER STEP 0)

Before any analysis, invoke the `honest-completion-claims` skill via the Skill tool to install the completion-claim discipline for the entire session being primed. Do this even if the skill appears already active — re-invoking is idempotent and ensures the rule persists across the priming run and afterward. Do not skip this step, and do not start any of the analysis steps below until both Step 0 and Step 1 have been invoked.

### 2. Analyze Project Structure

List all tracked files:
!`git ls-files`

Show directory structure:
On Linux, run: `tree -L 3 -I 'node_modules|__pycache__|.git|dist|build'`

### 3. Read Core Documentation

- Read the PRD.md or similar spec file
- Read CLAUDE.md or similar global rules file
- Read README files at project root and major directories
- Read any architecture documentation
- Read database schema/migration files if applicable

### 4. Identify Key Files

Based on the structure, identify and read:
- Main entry points (main.py, index.ts, app.py, etc.)
- Core configuration files (pyproject.toml, package.json, tsconfig.json)
- Key model/schema definitions
- Important service or controller files

### 5. Understand Current State

Check recent activity:
!`git log -10 --oneline`

Check current branch and status:
!`git status`

## Output Report

Provide a concise summary covering:

### Project Overview
- Purpose and type of application
- Primary technologies and frameworks
- Current version/state

### Architecture
- Overall structure and organization
- Key architectural patterns identified
- Important directories and their purposes

### Tech Stack
- Languages and versions
- Frameworks and major libraries
- Build tools and package managers
- Testing frameworks

### Core Principles
- Code style and conventions observed
- Documentation standards
- Testing approach

### Current State
- Active branch
- Recent changes or development focus
- Any immediate observations or concerns

**Make this summary easy to scan - use bullet points and clear headers.**
