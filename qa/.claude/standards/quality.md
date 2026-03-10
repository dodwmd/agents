# Quality Standards

## Zero Defect Handoff

Work is not approved until:
- [ ] All acceptance criteria verified (not assumed)
- [ ] Critical paths tested manually
- [ ] Edge cases covered (empty states, nulls, boundary values)
- [ ] Error paths produce useful messages — not silent failures or stack traces
- [ ] No known bugs left unresolved without a tracked issue

## Testing Standards

**Behavioral coverage beats line coverage.** A test suite with 90% line coverage that only tests the happy path is not thorough.

What thorough coverage looks like:
- Happy path: the expected case works
- Error paths: bad input, failed dependencies, and network errors are handled explicitly
- Edge cases: zero items, max values, concurrent operations, duplicate submissions
- Regression: every bug fix has a test that would have caught the original bug

**Tests must be independent** — a test that depends on another test's side effects is brittle. Each test sets up its own state.

**Test names describe behavior:**
- ✅ `test_search_returns_empty_list_when_no_results_match`
- ❌ `test_search_2`

## Review Severity Levels

Apply consistently across all reviews:

| Severity | Definition | Action |
|---|---|---|
| **Critical** | Breaks functionality, data loss risk, security hole | Blocks merge |
| **Important** | User-facing bug, significant performance issue, missing error handling | Should fix before merge |
| **Suggestion** | Code quality, readability, minor improvement | Optional, developer's call |

Only surface a finding if confidence ≥ 80%. A long list of maybes is noise.

## Completion Criteria for PR Reviews

A PR review is complete when:
- [ ] The diff has been read in full — not just the changed files, but their context
- [ ] Test coverage has been assessed against acceptance criteria
- [ ] All Critical and Important findings are documented with file:line references
- [ ] Findings are grouped by severity
- [ ] A clear recommended action is stated: approve / fix these specific things / discuss
