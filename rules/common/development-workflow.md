# Development Workflow

> This file extends [common/git-workflow.md](./git-workflow.md) with the process that
> happens before git operations.

## Feature Implementation Workflow

0. **Research & Reuse** _(before any new implementation)_
   - **GitHub code search first:** `gh search repos` / `gh search code` for existing
     implementations, templates, and patterns before writing anything new.
   - **Library docs second:** use the `context7` MCP or primary vendor docs to confirm
     API behavior, package usage, and version-specific details before implementing.
   - **Exa when the first two are insufficient:** the `exa` MCP for broader web
     research or discovery.
   - **Check package registries:** npm, PyPI, crates.io, etc. before writing utility
     code. Prefer battle-tested libraries over hand-rolled solutions.
   - Prefer adopting or porting a proven approach over writing net-new code when it
     meets the requirement.

1. **Plan First** for anything non-trivial — restate requirements, identify
   dependencies and risks, break into phases before touching code. Use the `plan`
   skill for structured planning on complex work.

2. **TDD where it fits** — write the test first (RED), implement to pass it (GREEN),
   refactor (IMPROVE). See [testing.md](testing.md) for coverage targets. Not every
   change warrants full TDD ceremony — infra/ops scripts and one-off fixes often don't.

3. **Verify before claiming done** — run the actual build/type/lint/test commands and
   look at their output. Use the `verification-loop` skill (build → types → lint →
   test → diff review) for anything beyond a trivial change. Don't claim fixed without
   rerunning the proving command.

4. **Commit & Push** — see [git-workflow.md](./git-workflow.md) for message format and
   PR process.

5. **Pre-push checks** — CI passing, merge conflicts resolved, branch up to date with
   target before opening or requesting review on a PR.
