---
name: hermes-agy-ponytail-workflow
description: "Master AGY + Ponytail workflow for minimal, reviewed, source-grounded, independently verified coding delivery."
version: 2.0.0
author: User-provided workflow, curated by Hermes
license: MIT
metadata:
  hermes:
    tags: [hermes, agy, antigravity, ponytail, coding, review, verification]
    related_skills: [antigravity-cli, ponytail]
---

# Hermes + AGY + Ponytail Master Workflow

Use this skill whenever Hermes supervises Antigravity CLI (`agy`) coding, review, debugging, installation, or repository delivery. It combines the official AGY operator guidance, current Ponytail behavior, and the user's proven Pokémon/Godot workflow.

## Non-negotiable project policy

- Hermes/GPT owns discovery, canonical research, architecture, task boundaries, review, verification, commit, and push.
- AGY is one bounded executor or reviewer; it does not plan product behavior, invent canonical data, commit, or push.
- Use **exactly** `gemini-3.6-flash-high` with high effort for every AGY task: investigation, implementation, repair, polish, and review.
- Do not use Gemini 3.1 Pro, medium models, AGY defaults, or silent model escalation for this project. If blocked, report the concrete blocker and stop.
- Use one AGY code writer per worktree. Never overlap writers in `/pokemongame`.
- For the Pokémon project, work only in `/pokemongame`, preserve Godot 3.5.x compatibility, and prefer the main Hermes model for direct coding when the user did not request AGY.
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

Confirm that `gemini-3.6-flash-high` is available before pinning it. Verify Ponytail is installed and configured:

```bash
agy plugin list
mkdir -p ~/.config/ponytail
printf '{"defaultMode":"ultra"}\n' > ~/.config/ponytail/config.json
```

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

For bounded long runs, use Hermes `terminal(background=true, notify_on_complete=true, pty=true)`, then `process(action=wait|poll|log)`. For interactive sessions use `pty=true`; resume with `--continue`/`-c` or a specific conversation when appropriate.

### AGY output and bounds

- `agy -p`/`--print` returns plain text, not a JSON envelope.
- There is no reliable `--output-format json` result envelope and no `--max-turns` flag.
- Bound execution with `--print-timeout` and the outer terminal timeout.
- AGY may write `.hermes/agent-status/<task-id>.md`; leave it untracked.
- A report saying tests passed is a claim until Hermes sees local command output.

### Review mode

Use the same pinned model for a bounded review when requested:

```bash
agy --model gemini-3.6-flash-high --effort high \
  --dangerously-skip-permissions --print-timeout 20m \
  --print "Review the current diff only. Check contract compliance, regressions, security/data-loss risks, Ponytail bloat, and missing focused tests. Do not edit, commit, or push. Report concrete findings first."
```

AGY review is advisory. Hermes remains the final reviewer and must inspect the diff and run checks independently.

## Required workflow

### 1. Inspect before writing

In the target repository:

```bash
git status --short --branch
git log --oneline -3
ps -eo pid,etimes,args | awk '$0 ~ /agy|godot3/ && $0 !~ /awk/ {print}' || true
```

Read `AGENTS.md`, relevant source, data, tests, task contract, and planner-provided references. Account for every pre-existing change. Pause the recurring Pokémon worker before manual AGY/project writing and resume only after review, validation, commit, push, and remote verification.

### 2. Establish canonical facts first

For domain content, Hermes supplies authoritative source revision, exact values, coordinate transforms, schema, edge cases, and acceptance tests. Use committed `docs/references/` handoffs for canonical Pokémon data. Never invent map geometry, encounter slots, trainer parties, item coordinates, warps, event requirements, or facility placement.

If a target terrain/state/map does not exist, create a source-backed handoff and reorder around the missing prerequisite. A playable native approximation must be labeled as an approximation; ROM `.blk` files, graphics, sprites, and derived assets are reference-only unless provenance and licensing permit reuse.

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

Launch only the pinned model and contract. Record launch time. Do not poll a healthy silent writer repeatedly; first inspect no earlier than five minutes unless it exits or emits a concrete error. After a check, wait five minutes unless a new signal appears.

After a skill rewrite, observe the required **15-minute cooldown** before starting any new AGY/project code-writing run. Read-only inspection, contract writing, and verification are allowed during cooldown.

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

Run the focused commands from the contract plus the appropriate full checks. For the Pokémon/Godot project, the normal gate is:

```bash
/opt/data/bin/godot3 --path /pokemongame --video-driver Dummy --audio-driver Dummy --script <focused-regression>.gd --quiet
python3 -m unittest discover tests
python3 tools/validate_data.py
git diff --check
```

Use finite timeouts. Run sibling regressions when a shared map graph, movement transaction, battle path, encounter rate, persistence flag, or validator changes. When extending a graph, update stale pre-extension assertions and verify static symmetry plus runtime traversal in both directions.

### 7. Commit, push, verify

Hermes commits only a coherent verified slice:

```bash
git add <reviewed files>
git diff --cached --check
git commit -m "<conventional message>"
git push origin main
git status --short --branch
git log -1 --oneline
```

Verify the remote branch SHA before claiming delivery. Do not claim deployment, CI, or endpoint success without external evidence.

## Pokémon-specific preferences and invariants

- Target Pokémon Red first, then unlock Pokémon Gold; Gen II is the content ceiling, not Gen IV/HeartGold.
- Prefer simple native Godot implementations over copying `gen1recomp` architecture; use `modern_clean` only as behavioral guidance.
- Preserve Windows/Linux playable builds and CubeXX-safe behavior where applicable.
- Outdoors remain one `kanto_overworld` with global coordinates; interiors are separate only when useful and must have explicit exits.
- Field HMs require skill unlock, badge, and compatible party species; they need not be taught. TM reuse remains policy-driven.
- Story requirements fail closed. One-shot events persist completion and are replay-safe.
- Trainer rewards occur only after the complete opposing party is defeated.
- Movement is transactional: source grid position during animation, exactly-once target commit on completion, then follower/camera/save/transition/encounter effects.
- Encounter rates are explicit per-step gates. Cave encounters may use a labeled interior `tall_grass` approximation while retaining exact source rate and slot order.
- For source warp IDs without direct native pairing, document the reviewed row-order mapping and test both directions; do not claim more canonical fidelity than the evidence supports.

## Failure handling

- **Eligibility/auth/network failure before edits:** report the concrete failure; do not switch models or start a concurrent writer.
- **Blank output:** inspect process, status, diff, and milestone files.
- **Timeout/stall:** inspect Git history and worktree; stop project-scoped child processes; salvage only reviewed changes.
- **Missing planner values:** stop with `BLOCKED: missing planner-provided source/values`.
- **Failed verification:** fix or cancel the task, add a revised narrow task, and rerun from a clean understood state.
- **Push failure:** do not claim success; retry only with the documented repository credentials or report the blocker.

## Master checklist

- [ ] Correct repository and project rules inspected.
- [ ] Recurring writer paused before manual work.
- [ ] Canonical sources and exact values supplied.
- [ ] One narrow contract with non-goals and focused checks.
- [ ] Only `gemini-3.6-flash-high`, high effort.
- [ ] One writer maximum; AGY did not commit/push.
- [ ] Ponytail review completed.
- [ ] Hermes inspected every changed file.
- [ ] Focused and full checks reproduced locally.
- [ ] Commit/push and remote SHA verified.
- [ ] Worker resumed only after delivery verification.
