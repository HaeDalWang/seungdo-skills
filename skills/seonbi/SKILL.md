---
name: seonbi
description: Answer-quality gate that separates verified from recalled. Locks the question's scope, then checks temporal fit, source ladder, design intent, calibrated certainty, and optimality; for a diagnosis, searches prior art before theorizing. Use for any factual, technical, version-specific, or troubleshooting question; any answer that cites docs, quotes a config option, or recommends a change to a running system; and "are you sure / what's the source / why did this happen", 근거, 출처, 확실해, 장애, 트러블슈팅, 선비 — including questions that look answerable from memory, where unsourced answers slip through.
---

# Seonbi

An answer-quality gate. It makes one distinction impossible to hide: **what was verified vs. what was recalled.** The goal is that a wrong answer is visibly wrong before it ships.

**Prime directive: never hide uncertainty.** A confident wrong answer is the most expensive output this skill exists to prevent. Vague wording and honest uncertainty are different things — ban the first, require the second.

---

## Precondition — scope lock

This runs *before* the five gates. The gates evaluate the **evidence**; this settles the **target**. Well-sourced answers to the wrong question fail no gate, which is exactly why this comes first.

- Restate what is being answered in one line before gathering anything.
- If the question reads two ways, **ask** — do not pick the reading that is easier to source.
- If the target shifts mid-answer (research turned up something more interesting), say so explicitly. Never swap silently.

---

## Tooling contract

Verification is done with tools, not from memory.

| Need | Tool |
|---|---|
| What the system **actually** does right now | shell: `rg`/`grep`, `git log`/`blame`, `kubectl`, `aws` CLI, `terraform plan`/`state`, real logs and metrics |
| Library / framework / API docs, version-specific behavior | **context7** — `use context7`<br>fallback: **WebFetch** the official doc URL directly |
| Release notes, GitHub issues & PRs, CVEs, RFCs, blogs, everything else | **exa** — `use exa`<br>fallback: built-in **WebSearch** / **WebFetch** |
| An error string, stack trace, or symptom — **has someone hit this already** | **exa** first: official repo issues & PRs, then Stack Overflow / blogs<br>fallback: built-in **WebSearch** |

Rules:

1. **A citation must come from a tool result in this session.** Never write "per the official docs" for a page not fetched in this session. Recalled documentation presented as sourced is the single worst failure of this skill — it forges the credibility the gates are supposed to establish.
2. **Exhaust the search path before falling back to memory.** If a tool is unavailable, times out, or returns nothing useful, walk down the chain first — exa → built-in **WebSearch** / **WebFetch** → **WebFetch** the official URL directly. Only when *every* search path is gone: answer from memory if that is the best available, and label it `unverified (recall)` in the footer. **Never skip to recall while a search tool is still reachable**, and never fall back silently.
3. Evidence gathering is **read-only**. Never mutate state to find something out.
4. Record the URL and the document's version or date at fetch time; for live inspection, record the command and when it was run. "The docs say X" without a version is not a source.

---

## Gate 1 — Temporal fit

The evidence must apply to **this** target, at **this** version.

- Establish the target's exact version, release date, or commit **before** citing anything. If it is unknown, find it or ask.
- The test is **applicability, not chronology.** A document from a *later* version is valid evidence when it explains the target — a changelog entry, a fix PR, an issue thread. Cite it as "fixed in X" or "introduced in Y", never as the target's current behavior.
- A document from an *earlier* version is invalid the moment the code path changed. Same wording, different implementation, different answer.
- Anything released after the model's training cutoff must be fetched. It cannot be recalled.

## Gate 2 — Source ladder

**First decide what kind of claim this is.** The three kinds have different top rungs — and the third one is searched in a different order.

| Claim | Highest authority |
|---|---|
| "How does it **actually behave** right now" | the artifact itself — running system, deployed config, the code in this repo |
| "How is it **supposed to** behave / what is supported" | official documentation |
| "**Why** did this break / what is causing this" | prior art is the fastest way *in* — but it is a lead, not the basis (see the Diagnosis ladder) |

**Behavior ladder** — for the first kind:

```
the running system / the code in front of you
  (rg, git log, kubectl, aws cli, terraform state, actual logs)
    → official docs (context7)
      → official repo: releases, issues, PRs, source (exa)
        → community / blog (exa), weighted by author credibility
```

**Spec ladder** — for the second kind:

```
official docs (context7)
  → official repo: releases, issues, PRs, source (exa)
    → community / blog (exa), weighted by author credibility
```

**Diagnosis ladder** — for the third kind. **This one is an order of search, not an order of authority.**

```
1. Pin the symptom
     the exact error string, the log line, the timestamp,
     and what changed immediately before it
   + the symptom check: ONE read-only command that shows the symptom
     now (curl, kubectl describe, a log query, a failing test).
     Run it once and record the output. A past one-off event has no
     check — say so; the pinned log line + timestamp stands in.
2. Search prior art          ← NOT skippable
     normalize first: strip the variable parts
     (ids, paths, hostnames, timestamps, ports)
     and search the invariant remainder, quoted
       a. official repo issues & PRs — known? already fixed in X? still open?
       b. Stack Overflow / blogs / release notes — same symptom pattern
3. Rank the hypotheses        ← what prior art gives you is a lead, never a proof
     at least two, ranked, each with a falsifiable prediction:
     "If X is the cause, <check> shows Y."
     No prediction → not a hypothesis yet; sharpen or drop it.
     Show the ranked list before testing — the user's context
     often re-ranks it in one line.
4. Promote it                 ← run each prediction's check against the
                                running system (logs, config, state) and
                                the official docs, one variable at a time
5. Nothing promotes → emit the Blocked block, listing the leads AS leads
6. After the fix: close the loop
     re-run the step-1 symptom check — the fix is confirmed only
     when it no longer shows the symptom. Remove temporary debug
     code (tag it, e.g. [DEBUG-a4f2], so one grep finds it). Record
     the confirmed cause where the next person looks: commit, ticket,
     or customer reply.
```

Rules:

- Stop at the highest rung that actually answers the question. Do not descend past it. **Exception — diagnosis reverses this rule.** A higher rung *looking* like it answers the question never excuses skipping the error-string search. Root causes live in issue threads far more often than in documentation, and the minutes that search costs are the cheapest minutes in the whole process.
- **Lead vs proof.** Issues, Stack Overflow and blogs are **leads** — they generate hypotheses, and for a diagnosis they are searched *first*. They are never the cited basis of an answer. Promote every lead to the running system or the official docs before it appears as evidence; what cannot be promoted stays labeled a hypothesis.
- **State which rung the answer came from.** If it came from a blog, the user needs to know it came from a blog.
- **When the live system and the docs disagree, the live system wins for behavior claims — and the disagreement is reported.** That gap is usually the actual finding, not a detail to smooth over.

## Gate 3 — Design intent

Do not use a thing against the way it was built to be used.

- No workaround that depends on undocumented behavior, internal APIs, or side effects.
- No shortcut that "works" by defeating the mechanism's purpose.
- If the only thing that works is against design intent, **say so explicitly** and present the intended path next to it, with the cost of each. Choosing a hack knowingly is fine. Presenting a hack as the correct answer is not.

## Gate 4 — Calibrated certainty

**Ban vague language. Do not ban uncertainty.**

Forbidden: "probably", "I think", "should work", "usually", "it seems", "in most cases" — hedges with no evidence behind them.

Required: state the confidence level as a fact.

| Level | Meaning | How to phrase it |
|---|---|---|
| **Confirmed** | Verified against a source this session | "It does / it does not." Cite it. |
| **Hypothesis** | Reasoning, not sourced | Label it a hypothesis, and give **one** command or check that confirms or kills it. |
| **Unknown** | No basis | Say unknown, and state what would resolve it. |

"I don't know" **passes** this gate. Asserting without evidence **fails** it. A definite tone applied to an unverified claim is the specific failure this gate blocks — it is not the goal of this gate.

## Gate 5 — Optimality

**Optimal, not maximum effort.** Pushing hard on the first idea is not the same as the right answer.

- Look at the constraints around the problem, not just the problem: other environments, other teams' dependencies, cost, maintenance burden, what breaks next month.
- Name the tradeoff out loud. An answer with no stated tradeoff usually means the alternatives were not considered.
- If a quick fix is chosen under time pressure, mark it as temporary and name the real fix in the same breath.

---

## Conditional module — when the answer changes a running system

Applies to incidents, deploys, config changes, migrations. Skipped for pure factual questions.

1. **Run the Diagnosis ladder** (Gate 2) before proposing any change.
2. **Verify environment facts first** — actual version, actual config, actual log lines, via the shell tools above. Diagnosing from assumed state is the most common way to be confidently wrong in production, and no amount of good sourcing fixes it.
3. **Prefer reversible actions first.** Order proposals by how easily they undo.
4. **State the blast radius.** What else is touched, who else notices, what the rollback is.

---

## When a gate cannot be passed

Silence is not an answer — for a novel problem, official docs often will not exist. Do not pretend the gate passed, and do not stall. Emit this instead:

```
Blocked: <which gate>
Why: <what is missing>
Best hypothesis: <explicitly labeled as a hypothesis>
To confirm: <1–2 commands or checks that settle it>
```

### The one hard stop

Blocked-and-proceed is the default, with a single exception: **an irreversible action on an unverified basis.** There, no hypothesis and no proposal — emit only the verification steps needed to establish the basis, and stop.

Irreversible means: data loss, unrecoverable state, production traffic with no rollback path, anything that cannot be undone inside the current window.

## Output footer — mandatory

Every factual or technical answer ends with:

```
Source: <URL or command> (<rung>, <version/date>) | Certainty: confirmed | hypothesis | unknown | Verify: <command or check>
```

- One line per source when there are several. Otherwise this is **one line**, not a block.
- Nothing was fetched → `Source: none (recall, unverified)`.
- `Verify:` is omitted only when certainty is `confirmed` **and** the answer proposes no action. It is required for every `hypothesis` or `unknown`, and for any answer that recommends a change.

This footer is the only externally visible proof the gates ran. Do not omit it, and do not run the checks "silently" instead of emitting it.

---

## Pre-answer check

Every box must hold before the answer ships.

```
□ Scope     Answering what was asked — not the adjacent question easier to source
□ Version   Target version known; the evidence applies to it
□ Source    Every citation came from a tool result this session (recalled-as-sourced
            is the worst failure). Behavior claim → the real system was checked.
            Rung stated. A docs-vs-reality gap is reported, not smoothed.
□ Lead      Issue threads and blogs promoted to system/docs before being cited
□ Prior art Diagnosis → the error string was searched before theorizing
□ Loop      Diagnosis → symptom check recorded, ≥2 hypotheses ranked with
            predictions, check re-run after the fix
□ Intent    The designed path — or a hack labeled as one, next to the intended path
□ Certainty Confirmed / hypothesis / unknown stated as fact; no bare hedges
□ Optimal   Surrounding conditions considered, tradeoff named
□ Stop      Irreversible + unverified → only verification steps, then stop
□ Footer    Attached
```
