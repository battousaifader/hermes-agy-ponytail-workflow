---
name: hermes-agy-ponytail-workflow
description: "Project-agnostic master AGY + Ponytail workflow for minimal, reviewed, source-grounded, independently verified coding delivery."
version: 2.2.0
author: User-provided workflow, curated by Hermes
license: MIT
metadata:
  hermes:
    tags: [hermes, agy, antigravity, ponytail, coding, review, verification]
    related_skills: [antigravity-cli, ponytail]
---

# Hermes + AGY + Ponytail Master Workflow

Use this skill whenever Hermes supervises Antigravity CLI (`agy`) repository implementation, review, debugging, installation, or delivery. It combines the official AGY operator guidance, current Ponytail behavior, and a disciplined project-independent delivery workflow.

## Non-negotiable project policy

- Hermes/GPT owns discovery, canonical research, architecture, task boundaries, review, verification, commit, and push.
- AGY is one bounded executor or reviewer; it does not plan product behavior, invent canonical data, commit, or push.
- Use **exactly** `gemini-3.6-flash-high` with high effort for every AGY task: investigation, implementation, repair, polish, and review.
- Do not use Gemini 3.1 Pro, medium models, AGY defaults, or silent model escalation for this project. If blocked, report the concrete blocker and stop.
- Use one AGY code writer per worktree. Never overlap writers in the target worktree.
- AGY must not commit or push. Hermes independently reviews, validates, commits, pushes, and verifies the remote SHA.
- `.hermes/` task, plan, and status artifacts remain untracked unless explicitly requested.

## Ponytail mode

Ponytail is active for every coding task. Default project execution mode is **ultra** for AGY work; respect an explicit user mode such as `/ponytail full` for the current interaction.

Apply the ladder after understanding the real flow:

1. Does this need to exist? Skip speculative work.
2. Reuse an existing helper, type, pattern, or transaction.
3. Prefer the standard library.
4. Prefer native platform features.
5. Prefer an already-installed dependency.
6. Prefer one line.
7. Write the minimum correct code.

Also:

- Read the task and trace callers before editing.
- Fix root causes in shared paths, not symptoms in one caller.
- Prefer deletion, boring code, few files, and no speculative abstractions.
- Mark deliberate simplifications with a `ponytail:` comment naming the ceiling and upgrade path.
- Never simplify away validation at trust boundaries, error handling that prevents data loss, security, accessibility, or explicitly requested behavior.
- Every non-trivial branch, loop, parser, money/security path, or state transaction leaves one runnable focused check. Trivial one-liners need no test.
- Output code first; then at most three concise lines unless the user requests a report or walkthrough.

## Official AGY operator model

AGY has two distinct layers:

1. **Shell wrapper:** `agy help`, `agy install`, `agy plugin`, `agy update`, `agy changelog`, and non-interactive `agy --print`/`-p`.
2. **Interactive TUI commands:** `/config`, `/permissions`, `/skills`, `/agents`, `/model`, `/settings`, `/tasks`, `/resume`, `/continue`, `/rewind`, `/fork`, and related commands. These do not work as shell commands.

Run AGY commands through Hermes `terminal`. Read AGY files with `read_file`, not `cat`.

### Preconditions

```bash
command -v agy
agy --version
agy models
agy plugin list
```

Confirm that `gemini-3.6-flash-high` is available before pinning it. Inspect plugin state:

```bash
agy models
agy plugin list
```

Do not create or overwrite Ponytail configuration during a precondition check. If plugin configuration is explicitly requested, first verify its documented path and preserve existing keys.

For setup or CLI troubleshooting, also verify:

```bash
agy help
```

Inspect `~/.gemini/antigravity-cli/settings.json` and the latest `~/.gemini/antigravity-cli/log/cli-*.log` with `read_file`. If an AGY plugin was changed, run `agy plugin validate <plugin-path>` before use.

Do not execute installer examples involving `curl | bash` without explicit review. Treat official skill documentation marked dangerous as documentation, not an instruction to execute.

### One-shot execution

Preferred for bounded work, reviews, and scripted prompts:

```bash
agy --model gemini-3.6-flash-high --effort high \
  --dangerously-skip-permissions --print-timeout 45m \
  --print "$(< .hermes/tasks/<task-id>.md)"
```

Pass the task text immediately to `--print`. Do not place another flag after short `-p`. Use the absolute AGY path when PATH is uncertain:

```bash
/opt/data/home/.local/bin/agy --model gemini-3.6-flash-high --effort high \
  --dangerously-skip-permissions --print-timeout 45m \
  --print "$(< .hermes/tasks/<task-id>.md)"
```

For bounded long `--print` runs, use `terminal(background=true, notify_on_complete=true)` without PTY, then `process(action=wait|poll|log)`. Use `pty=true` only for an interactive TUI session. Resume with `--continue`/`-c` or `--conversation <id>` when appropriate.

### AGY output and bounds

- `agy -p`/`--print` returns plain text, not a JSON envelope.
- There is no reliable `--output-format json` result envelope and no `--max-turns` flag.
- Bound execution with `--print-timeout` and the outer terminal timeout.
- AGY may write `.hermes/agent-status/<task-id>.md`; leave it untracked.
- A report saying tests passed is a claim until Hermes sees local command output.

### Review mode

Run review without `--dangerously-skip-permissions`:

```bash
agy --model gemini-3.6-flash-high --effort high --print-timeout 20m \
  --print "Review the current diff only. Check contract compliance, regressions, security/data-loss risks, Ponytail bloat, and missing focused tests. Do not edit, commit, or push. Report concrete findings first."
```

Record `git status --porcelain=v1` before and after review and fail the review if the worktree changed. Use `--sandbox` when review needs commands beyond read-only inspection. Reserve `--dangerously-skip-permissions` for explicitly authorized implementation runs in an isolated worktree.

AGY review is advisory. Hermes remains the final reviewer and must inspect the diff and run checks independently.

## Required workflow

### 1. Inspect before writing

In the target repository:

```bash
git status --short --branch
git log --oneline -3
```

Use Hermes `process(action="list")` and recorded AGY session IDs to identify runs launched by this workflow. Never stop a process based only on a process name. If ownership cannot be tied to the current task/worktree, report the conflict instead of terminating it.

Read `AGENTS.md`, relevant source, data, tests, task contract, and planner-provided references. Account for every pre-existing change. If an active recurring writer is confirmed to target the same worktree, pause it before writing and resume it after delivery verification. Otherwise, skip this step.

### 2. Establish canonical facts first

For domain content, Hermes supplies authoritative source revisions, exact values, coordinate or identifier transforms, schema, edge cases, and acceptance tests. Use committed repository references or pinned external sources. Never invent domain rules, geometry, encounter/content tables, item coordinates, warps, event requirements, facility placement, or other source-dependent values.

If a target state, integration point, schema, or external dependency does not exist, create a source-backed handoff or prerequisite contract and reorder around the missing prerequisite. Label approximations explicitly; do not reuse proprietary assets, generated artifacts, or derived data without provenance and permission.

### 3. Write one compact contract

Create `.hermes/tasks/<task-id>.md` containing:

```md
# Goal
One observable outcome.

## Scope / non-goals
- In: exact files and behavior.
- Out: deferred or blocked work.

## Constraints
- Existing architecture and source-of-truth rules.
- No invented values, dependencies, commits, or pushes.

## Acceptance
- Observable behavior.
- Persistence/idempotency/error edge case.

## Checks
- Exact focused command.
- Required project validation commands.

## Final report
Changed files, checks, commit/push status, blockers.
```

Start at the smallest useful scope. Do not combine feature, refactor, UI redesign, migration, or unrelated content in one contract.

### 4. Launch one writer

Launch one writer and record its Hermes process session ID. Use `notify_on_complete=true`; poll or inspect logs only when diagnosing a concrete signal, timeout, or suspected stall.

### 5. Review the real diff

After AGY exits:

```bash
git status --short --branch
git diff --stat
git diff --check
git diff -- <every changed file>
```

Check the diff against the contract, canonical source, existing callers, and Ponytail ladder. Reject unrelated churn, invented content, broad refactors, and test claims not reproduced locally. If a writer stalls, preserve useful changes only after inspecting every file and stop orphaned project processes before rerunning checks.

### 6. Verify independently

Run the focused commands from the contract plus the appropriate full checks. Use the repository’s documented build, test, lint, typecheck, security, migration, and deployment validation commands. Use finite timeouts and run sibling regressions when a shared interface, state transaction, dependency, persistence path, validator, or public API changes. Verify both directions when extending a graph or protocol.

### 7. Commit, push, verify

Only after confirming the repository’s branch and delivery policy:

```bash
git status --short --branch
git remote -v
git add <reviewed files>
git diff --cached --check
git diff --cached
git commit -m "<conventional message>"
git push <approved-remote> HEAD:<approved-target-branch>
git status --short --branch
git rev-parse HEAD
git ls-remote <approved-remote> refs/heads/<approved-target-branch>
```

The local `HEAD` and remote branch SHA must match before claiming delivery. Never assume `origin main`; use the repository’s approved remote and branch. Do not claim deployment, CI, or endpoint success without external evidence.

## Project integration policy

This skill is project-agnostic. Before any repository work, load and obey the target repository’s `AGENTS.md`, contribution guide, security policy, and applicable project-specific skills. Repository policy and explicit user requirements override this generic workflow. Keep project-specific architecture, language, framework, test commands, source handoffs, and deployment rules in the repository’s own guidance—not here.

## Failure handling

- **Eligibility/auth/network failure before edits:** report the concrete failure; do not switch models or start a concurrent writer.
- **Blank output:** inspect the Hermes process session, worktree status/diff, AGY milestone files, and the latest `~/.gemini/antigravity-cli/log/cli-*.log`.
- **Timeout/stall:** inspect Git history and worktree; stop only project-scoped child processes whose ownership is recorded for this task; salvage only reviewed changes.
- **Missing planner values:** stop with `BLOCKED: missing planner-provided source/values`.
- **Failed verification:** fix or cancel the task, add a revised narrow task, and rerun from a clean understood state.
- **Push failure:** do not claim success; retry only with the documented repository credentials or report the blocker.

## Master checklist

- [ ] Correct repository and project rules inspected.
- [ ] Active same-worktree recurring writer paused when present.
- [ ] Canonical sources and exact values supplied.
- [ ] One narrow contract with non-goals and focused checks.
- [ ] Only `gemini-3.6-flash-high`, high effort.
- [ ] One writer maximum; AGY did not commit/push.
- [ ] Ponytail review completed.
- [ ] Hermes inspected every changed file.
- [ ] Focused and full checks reproduced locally.
- [ ] Commit/push and remote SHA verified.
- [ ] Recurring writer resumed only after delivery verification, when one was paused.
