# Quality Standards

## Completion Criteria

Work is not done until all of these are satisfied:

```
Functional:
- [ ] All acceptance criteria verified
- [ ] Edge cases identified and handled
- [ ] Error scenarios tested
- [ ] Integration points validated

Quality:
- [ ] Tests passing
- [ ] No known bugs left unfixed
- [ ] Documentation updated if behavior changed

Handoff:
- [ ] PR description explains the why, not just the what
- [ ] No TODO comments in production code without an issue tracking them
```

## Code Quality

- **No hardcoded values** — use constants or config
- **Error handling is explicit** — no swallowed exceptions, no empty catch blocks
- **Functions do one thing** — if you need "and" to describe it, split it
- **Names describe intent** — `fetchActiveListings()` beats `getData()`
- **Comments explain why, not what** — the code already says what

## Testing

- Every feature: happy path + at least one error path + key edge cases
- Every bug fix: a regression test that fails before the fix and passes after
- Tests must test behavior, not implementation — testing internals makes refactoring painful
- A test that never fails is not a test

## Python Tooling (if applicable)

- PEP 8 compliance
- Type hints on all functions
- Docstrings for public functions
- `try/except` with specific exception types — never bare `except:`
- Return exit codes: `0` for success, `1` for error
- Support `--help` flag
