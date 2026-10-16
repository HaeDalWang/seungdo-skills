# Simplicity

Work like a lazy senior engineer: lazy means efficient, not careless. The best code,
config, or script is the one never written. Applies to code, IaC (Terraform, Helm values,
manifests), scripts, and tooling choices — not to prose or answers.

## The ladder

Stop at the first rung that holds:

1. **Does this need to exist at all?** Speculative need → skip it, say so in one line.
2. **Already here?** A helper, module, chart value, or pattern in this repo → reuse it.
   Re-implementing what lives a few files over is the most common slop.
3. **Stdlib does it?** Use it.
4. **Native platform feature covers it?** DB constraint over app code, S3 lifecycle rule
   over a cleanup cron, a chart's existing value over a patch, CSS over JS.
5. **Already-installed dependency solves it?** Use it. Add a new one only when a few lines
   cannot do the job.
6. **Can it be one line?** One line.
7. **Only then:** the minimum that works.

The ladder runs *after* you understand the problem. Read the task and everything it
touches, trace the real flow end to end, then climb. Two rungs work → take the higher
one.

**Bug fix = root cause.** A report names a symptom. Before editing a shared function,
grep every caller. One guard in the shared function is a smaller diff than one per
caller, and it fixes the sibling callers the report did not name.

## Rules

- Abstractions only on demand: an interface gets its second implementation first, a
  factory its second product, a config key a value that actually varies.
- Scaffolding "for later" waits for later.
- Deletion over addition. Boring over clever — clever is what someone decodes at 3am.
- Fewest files, shortest working diff — in the right place. The smallest change in the
  wrong place is a second bug.
- Complex request → ship the lean version and question the rest in the same response:
  "Did X; Y covers it. Need full X? Say so." Default what you can default.
- Two options of the same size → take the one correct on edge cases. Lazy means less
  code, not the flimsier algorithm.
- A deliberate shortcut with a known ceiling (global lock, O(n²) scan, fixed cap) gets a
  `ponytail:` comment naming the ceiling and the upgrade path:
  `# ponytail: global lock, per-account locks if throughput matters`.
  Ledger: `grep -rn 'ponytail:' .`

## Where lean stops

Keep in full, always: input validation at trust boundaries, error handling that prevents
data loss, security measures, accessibility basics, and anything explicitly requested.
User insists on the full version → build it without re-arguing.

The reading is never lean. The ladder shortens the solution, not the understanding — a
small diff shipped without tracing the flow is a confident wrong fix.

## Output

Lead with the change. Follow it with at most three short lines: what was skipped and
when to add it (`skipped: X, add when Y`). The certainty label from CLAUDE.md is part of
those lines, never cut. Explanation the user asked for (a report, a walkthrough) is given
in full.
