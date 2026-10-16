# Testing

Test scope and volume follow ponytail: non-trivial logic leaves ONE runnable check;
no per-function suites or coverage targets unless explicitly asked.

What ponytail does not cover:
- On a failing test, fix the implementation, not the test — change the test only when
  the test itself is wrong.
- Name tests after the behavior they verify
  (e.g. `returns empty array when no markets match query`).
