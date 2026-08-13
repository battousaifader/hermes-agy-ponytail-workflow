# Hermes + AGY + Ponytail Workflow

A reusable, project-agnostic Hermes skill for supervising bounded AGY coding
work. The supervising agent owns architecture, canonical facts, task
contracts, review, verification, commit, and push. AGY executes one explicit
implementation or review slice and must not invent missing product decisions
or authoritative domain data.

## Install in Hermes

Install the public skill through Hermes’ normal community-skill flow:

```bash
hermes skills install \
  https://raw.githubusercontent.com/battousaifader/hermes-agy-ponytail-workflow/main/SKILL.md
```

Then load it by name:

```text
hermes-agy-ponytail-workflow
```

The skill is scanner-safe and must install without a force or security-scan
bypass.

## Update

The skill is URL-backed, so check for upstream changes and update normally:

```bash
hermes skills check hermes-agy-ponytail-workflow
hermes skills update hermes-agy-ponytail-workflow
```

Verify the installed content when needed:

```bash
hermes skills inspect hermes-agy-ponytail-workflow
```

## Dependencies

Install Ponytail separately from its upstream source. Do not copy Ponytail’s
rules into this workflow; Ponytail updates independently:

```bash
hermes skills install \
  https://raw.githubusercontent.com/DietrichGebert/ponytail/main/skills/ponytail/SKILL.md \
  --category software-development \
  --name ponytail
```

Load both `hermes-agy-ponytail-workflow` and `ponytail` for coding work. The
AGY-side Ponytail plugin is maintained separately through the AGY-compatible
plugin source/import path.

## Use

Use this workflow when the supervising agent delegates a repository task to
AGY:

1. Inspect the repository rules, current worktree, relevant code, tests, and
   canonical sources.
2. Write one narrow `.hermes/tasks/<task-id>.md` contract with scope,
   non-goals, exact values, acceptance criteria, and checks.
3. Run one AGY writer in the target worktree using the AGY policy in
   `SKILL.md`.
4. Review the real diff independently; reject unrelated churn or invented
   content.
5. Re-run focused and full project checks locally.
6. Commit and push only from the supervising agent, then verify the remote SHA.

AGY-specific model policy applies only inside AGY: use exactly
`gemini-3.7-flash-medium` with medium effort. The supervising agent’s model and
provider are intentionally unspecified.

## Provenance

- Reviewed workflow version: `3.2.0`
- `SKILL.md` SHA-256: `e415b4d2a854cacb8857f5600cc7a2aafa0461bc1b4027d0fb0e34f835324ea3`
- The installed Hermes copy should match this hash before use.

See `SECURITY.md` for provenance, scanner, credential, and permission
boundaries.
