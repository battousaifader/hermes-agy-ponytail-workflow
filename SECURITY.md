# Security and import policy

This repository contains a Markdown procedure for supervising AGY. `SKILL.md` is documentation, not an executable installer or plugin.

## Safe import

- Pin imports to reviewed revision `7df41cc` and verify the documented SHA-256.
- Prefer a curated local copy when a skill manager’s scanner blocks a remote import.
- Do not weaken, bypass, or disable the skill manager’s security scanner.
- Do not execute installer examples such as `curl | bash` without explicit review.

## Credentials and persistence

- The workflow must not request, print, or modify API keys, OAuth tokens, keyrings, or credential files.
- AGY reconnection is an operational recovery step: inspect logs, reconnect explicitly, and verify the new session before retrying, including in cron jobs.
- Persistent configuration changes require explicit user direction and must preserve unrelated settings.

## Permission boundaries

- `--dangerously-skip-permissions` is allowed only for an explicitly authorized, isolated implementation run.
- Review runs must not use `--dangerously-skip-permissions`.
- AGY does not commit or push; Hermes independently reviews, validates, commits, pushes, and verifies the remote SHA.

Security scanners may flag command examples in Markdown heuristically. That is expected; do not bypass the scanner. Verify provenance and use the reviewed copy instead.
