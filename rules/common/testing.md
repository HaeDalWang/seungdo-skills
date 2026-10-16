# Testing

Non-trivial logic (a branch, a loop, a parser, a money/security path) leaves ONE
runnable check: the smallest thing that fails if the logic breaks — an `assert`-based
self-check or one small test file. Trivial one-liners need none. Suites, fixtures,
frameworks, and coverage targets only when explicitly asked.

- On a failing test, fix the implementation, not the test — change the test only when
  the test itself is wrong.
- Name tests after the behavior they verify
  (e.g. `returns empty array when no markets match query`).
