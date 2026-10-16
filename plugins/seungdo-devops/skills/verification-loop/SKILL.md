---
name: verification-loop
description: "Prove a code change works before claiming it is done: run the project's own build, type, lint, and test commands and report the evidence. Use after a non-trivial code change in a repo that has build/test tooling, before a PR, or after a refactor. Skip for docs-only edits, config tweaks, and one-off ops commands — rerunning the proving command is enough there."
license: MIT
metadata:
  origin: ECC
  upstream: https://github.com/affaan-m/ECC
  upstream-license: MIT
  upstream-copyright: Copyright (c) 2026 Affaan Mustafa
  modified: "Detect commands from the project instead of assuming npm; drop coverage target and continuous mode (test scope follows ponytail)."
---

# Verification Loop Skill

Prove the change works with the project's real commands, then report. Evidence, not claims.

## When to Use

- After a non-trivial code change, before saying "done"
- Before creating a PR
- After a refactor

Not for docs-only edits, config tweaks, or one-off ops commands.

## Step 0: Detect what the project actually has

Read `package.json` scripts, `Makefile`, `pyproject.toml`, `go.mod`, `Cargo.toml`, or CI config
(`.github/workflows/*`) and use **those** commands. Skip any phase the project has no tooling for —
say "skipped: no <phase> tooling", don't invent one.

## Verification Phases

### Phase 1: Build
Run the project's build command (e.g. `npm run build`, `go build ./...`, `cargo build`).
If it fails, STOP and fix before continuing.

### Phase 2: Type Check
e.g. `npx --no-install tsc --noEmit`, `pyright .`, `mypy .`. Fix errors in changed code.

### Phase 3: Lint
e.g. `npm run lint`, `ruff check .`, `golangci-lint run`.

### Phase 4: Tests
Run the existing test command (e.g. `npm test`, `pytest`, `go test ./...`).
Test scope follows ponytail — no coverage target. If the change has non-trivial logic and no
check covers it, add the ONE smallest check that fails if the logic breaks.

### Phase 5: Security Scan (changed files only)
Compare against the merge-base with the default branch, so committed-but-unpushed work,
uncommitted edits, and new untracked files are all included:
```bash
REF=$(git rev-parse --abbrev-ref origin/HEAD 2>/dev/null || echo main)
BASE=$(git merge-base HEAD "$REF" 2>/dev/null || git rev-parse HEAD)
{ git diff --name-only --diff-filter=d "$BASE"; git ls-files --others --exclude-standard; } \
  | sort -u | xargs grep -HnE "sk-|api_key|secret|password|BEGIN .*PRIVATE KEY" 2>/dev/null | head -10
```
An empty file list means nothing changed — report `SKIPPED`, not `PASS`.

### Phase 6: Diff Review
```bash
git diff --stat "$BASE"
git ls-files --others --exclude-standard   # new files the stat above does not show
```
Review each changed file for unintended changes, missing error handling, and edge cases.

Pipe long output through `head`/`tail` — report the failures, not the full log.

## Output Format

```
VERIFICATION REPORT
==================
Build:     [PASS/FAIL/SKIPPED]
Types:     [PASS/FAIL/SKIPPED] (X errors)
Lint:      [PASS/FAIL/SKIPPED] (X warnings)
Tests:     [PASS/FAIL/SKIPPED] (X/Y passed)
Security:  [PASS/FAIL/SKIPPED] (X issues)
Diff:      [X files changed]

Overall:   [READY/NOT READY]

Issues to Fix:
1. ...
```
