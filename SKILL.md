---
name: hermes-agy-ponytail-workflow
description: "Use when Hermes supervises Antigravity CLI (agy) coding work. Enforces one writer, narrow task contracts, Ponytail ultra, independent verification, and verified delivery."
version: 1.0.0
author: User-provided workflow, curated by Hermes
license: MIT
metadata:
  hermes:
    tags: [hermes, agy, antigravity, ponytail, coding, verification]
    related_skills: [antigravity-cli, ponytail]
---

# Hermes + AGY + Ponytail Workflow

## Overview

Hermes owns discovery, task boundaries, independent review, and verified delivery. AGY owns a single narrow code-writing slice. Ponytail ultra governs both: implement the smallest correct solution, without removing validation, security, data integrity, accessibility, or the smallest meaningful check.

## When to Use

- A coding task is delegated to AGY.
- A project needs a verified implementation → review → commit → push sequence.
- The user asks for Hermes + AGY coordination or Ponytail discipline.

Do not use multiple AGY code writers in one worktree. Queue the next writer until the active one exits and Hermes has inspected `git status`.

## Required Setup

Before code-writing work, confirm:

```bash
agy --version
agy models
agy plugin list
```

Ponytail must be installed in AGY and configured persistently:

```bash
agy plugin install https://github.com/DietrichGebert/ponytail
mkdir -p ~/.config/ponytail
printf '{"defaultMode":"ultra"}\n' > ~/.config/ponytail/config.json
```

`ultra` is a Ponytail mode, not an AGY effort level. Prefer `PONYTAIL_DEFAULT_MODE=ultra` as an environment override only when it is intentionally part of the host startup environment; the config file is the portable persistent default.

For Hermes, load the `ponytail` skill before every code-writing task and require a Ponytail diff review before a verified commit. If the native Ponytail plugin is installed, it must be enabled and configured for ultra; start a fresh session/gateway restart after plugin changes.

## Model Policy

Use exact IDs confirmed by `agy models`.

| Work | Model | Effort |
|---|---|---|
| Investigation or non-trivial implementation | `gemini-3.6-flash-high` | `high` |
| A specific documented blocker after one focused attempt | `gemini-3.1-pro-high` | `high` |
| Mechanical repair or final polish | `gemini-3.6-flash-medium` | `medium` |

Do not escalate for task size alone. Record the actual blocker first: failing command, ambiguous boundary/rule, or reproducible defect.

## Planner–executor boundary

Hermes/GPT is the planner and verifier; AGY is the bounded executor.

Before launching AGY, Hermes must supply the decision-bearing work: target behavior, file-level change plan, data/schema shape, source-of-truth references for canon/domain facts, edge cases, acceptance assertions, and exact checks. AGY may inspect implementation details and make the smallest mechanical choice consistent with that plan, but must not invent product rules, canonical game data, broad mappings, architecture, or an unspecified expansion of scope.

If the contract lacks an authoritative source or explicit values needed for domain data, AGY must stop without edits and report `BLOCKED: missing planner-provided source/values`. Structural validation is not evidence that generated domain data is correct.

### 1. Inspect before writing

Run and read the relevant outputs before AGY starts:

```bash
git status --short --branch
git log --oneline -3
find . -maxdepth 2 \( -name AGENTS.md -o -name CLAUDE.md \)
```

Read repository instructions, affected code/data paths, tests, and deployment workflow. Preserve pre-existing changes unless the user explicitly authorizes modifying them.

**Complete when:** every pre-existing change and applicable repository rule is accounted for.

### 2. Write a compact task contract

Create `.hermes/tasks/<task-id>.md` with:

```md
# Goal
<one observable outcome>

## Scope / non-goals
- In: <files or user flows>
- Out: <deferred work>

## Constraints
- Preserve existing work.
- No new dependency unless necessary.

## Acceptance
- [ ] <visible behavior>
- [ ] <edge case / data or security constraint>

## Checks
- <focused test>
- <build/typecheck>

## Final report
Changed files, checks, commit SHA, push status, deployment evidence, blockers.
```

**Complete when:** one narrow vertical slice has explicit acceptance checks and non-goals.

### Skill-rewrite cooldown

After any rewrite of a loaded Hermes/AGY workflow skill, do not start a new project code-writing task for **15 minutes**. Record the rewrite time and defer the task instead of starting a writer early. This cooldown applies to manual and scheduled project work; it does not block read-only inspection, contract writing, or verification.

### Scope ramp: find the breaking point slowly

Start every AGY evaluation cycle at **S0**: one observable behavior in one source area, plus one focused regression/check. Do not combine a feature, UI redesign, migration, refactor, or second behavior in the same S0 contract.

Advance only after **three consecutive clean AGY runs**: useful minimal diff, AGY completed its listed focused checks, and Hermes needed no code repair. Expand one dimension at a time (one adjacent behavior *or* one additional source area, never both). After any stall, blank output, broad diff, failed acceptance, or Hermes repair, return the next AGY run to the last proven-small scope; do not escalate models merely because scope failed.

Record the scope level and why it advanced, held, or shrank in the 20-run evaluation entry.

### 3. Launch one bounded AGY writer

Use a short launch prompt that directs AGY to read repository rules and the task contract, implement only that slice, preserve unrelated changes, run listed checks, review with Ponytail, and report changed files/checks/commit/push/blockers.

For non-interactive execution, pass task text immediately to `--print`; do not put another flag directly after short `-p`.

```bash
agy --model gemini-3.6-flash-high --effort high \
  --dangerously-skip-permissions --print-timeout 45m \
  --print "$(< .hermes/tasks/<task-id>.md)"
```

AGY may write an untracked milestone file at `.hermes/agent-status/<task-id>.md`; do not commit it.

### Process observation cadence

Record launch time, then make the **first** process/status/diff check no earlier than **5 minutes** after launch. Do not spend 120-second polling turns on a healthy silent writer. Check earlier only when the process exits, emits a concrete error/completion signal, or the user asks. After the first check, inspect no more often than every 5 minutes unless there is a new signal. Each check should combine process state, `git status`, and a concise diff summary.

**Complete when:** only one writer is active and its process/state is observable.

### 4. Verify independently

Treat AGY’s report as a claim. Hermes must inspect the real repository and rerun appropriate checks:

```bash
git status --short --branch
git diff --check
git diff --cached --check
git show --stat --oneline HEAD
```

Run the focused checks named in the task contract. For Docker/UI delivery, also verify `docker compose config`, running services, and the actual endpoint. For remote delivery, verify remote SHA and CI/workflow status. For image deployment, verify the running image revision rather than assuming a local commit is live.

Apply a Ponytail review: remove only demonstrably safe bloat; do not remove required safety, behavior, or tests.

**Complete when:** changed files, test results, diff hygiene, remote state, and any requested deployment target are verified from tools—not summaries.

### 5. Deliver

Commit only a coherent verified slice using a conventional message, then push. Verify the remote branch after push. Do not claim deployment without endpoint/container/CI evidence.

**Complete when:** the final report states verified facts, any exact blocker, and no unverified AGY claim.

## Failure Handling

- **Blank AGY print output:** inspect process status, `git status`, and milestone file before declaring failure.
- **Timeout:** inspect Git history and working tree; a commit may have completed while final output was pending.
- **AGY stall or context failure:** preserve useful changes, stop the writer, inspect the tree, then use Hermes/main tools for the narrowest safe cleanup.
- **Concurrent-writer risk:** stop or wait for the active writer; never overlap writers in one worktree.
- **Push/deploy claim:** verify it externally before reporting success.

## Twenty-Run AGY Refinement Loop

When the user requests workflow tuning, evaluate the next 20 AGY writing runs without adding a second writer or weakening verification.

For each run, record an untracked entry under `.hermes/agy-workflow-evaluation.md`: task ID, scope level and decision, contract/prompt length, launch-to-first-check time, number of checks, elapsed time, whether AGY produced a useful diff, exact checks run, cleanup required, and final outcome. Compare against the prior entry.

Make **at most one** evidence-backed adjustment per run. Prefer deleting duplicated prompt text, naming the exact files/acceptance checks, or narrowing scope. Do not change the one-writer rule, Ponytail ultra, independent verification, no-commit AGY boundary, or required validation merely to reduce tokens. After run 20, stop automatic tuning and summarize the measured result before any further workflow change.

**Complete when:** each run has a comparable record and any workflow edit is traceable to a measured failure or avoidable token cost.

## Verification Checklist

- [ ] One AGY code writer at most per worktree.
- [ ] Repository rules and pre-existing changes inspected first.
- [ ] Compact task contract defines scope, acceptance, checks, and non-goals.
- [ ] Ponytail is ultra and a Ponytail review occurred before commit.
- [ ] Hermes independently inspected diff and ran relevant checks.
- [ ] Commit, push, CI, image, and endpoint claims are backed by tool output where requested.
- [ ] Final report distinguishes verified facts from blockers.
