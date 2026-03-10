# Tools Reference

Skills and agents available for QA review. All files live under `.claude/`.

---

## Skills

### `/review-pr [aspects]`
**File**: `.claude/skills/review-pr.md`

Comprehensive PR review using specialized agents. The PR/branch already exists — this skill reviews what's there.

**Aspects** (optional, default is all applicable):
- `comments` — comment accuracy only
- `tests` — test coverage only
- `errors` — error handling only
- `types` — type design only
- `code` — general code quality only
- `simplify` — simplification pass only
- `all` — all applicable agents (default)

**Agent selection logic** (when running `all`):

| What changed | Agents applied |
|---|---|
| Any code changes | `code-reviewer` (always) |
| Test files modified/added | `pr-test-analyzer` |
| Comments or docs modified | `comment-analyzer` |
| Try/catch blocks in diff | `silent-failure-hunter` |
| Types/interfaces added or modified | `type-design-analyzer` |
| After other reviews pass | `code-simplifier` (polish pass) |

---

## Agents

### `comment-analyzer`
**File**: `.claude/agents/comment-analyzer.md`

Reviews code comment accuracy and maintainability.
- Comment accuracy vs actual code behavior
- Documentation completeness
- Comment rot and outdated references
- Misleading or technically incorrect comments

---

### `pr-test-analyzer`
**File**: `.claude/agents/pr-test-analyzer.md`

Reviews test coverage quality and completeness.
- Behavioral coverage (not just line coverage)
- Critical gaps rated 1–10 (10 = must add before merge)
- Test resilience and isolation
- Missing edge cases and error condition tests

---

### `silent-failure-hunter`
**File**: `.claude/agents/silent-failure-hunter.md`

Hunts for error handling that hides failures.
- Empty or logging-only catch blocks
- Fallbacks that mask real errors
- Missing error propagation
- Swallowed promise rejections and async errors

---

### `type-design-analyzer`
**File**: `.claude/agents/type-design-analyzer.md`

Rates type design quality across four dimensions (each 1–10):
- **Encapsulation**: Does the type hide its implementation details?
- **Invariant expression**: Does the type make illegal states unrepresentable?
- **Usefulness**: Does the type provide meaningful operations?
- **Invariant enforcement**: Does the type enforce its rules at runtime?

---

### `code-reviewer`
**File**: `.claude/agents/code-reviewer.md`

General code review against project guidelines.
- Project convention compliance
- Bug detection
- Style and consistency issues
- Confidence-scored 0–100 (91–100 = critical, must fix)

---

### `code-simplifier`
**File**: `.claude/agents/code-simplifier.md`

Simplifies code for clarity without changing behavior.
- Unnecessary nesting and complexity
- Redundant code and abstractions
- Overly clever or compact code
- Consistency with project standards

**Important**: Run after other reviews pass, not before. Simplification is a polish step.

---

## Standards

Reference these when reviewing. They define what findings are Critical vs. Important vs. Suggestion.

### `.claude/standards/quality.md`
Zero defect handoff criteria, testing standards (behavioral coverage, test independence, naming), severity level definitions (Critical / Important / Suggestion), and PR review completion checklist.

### `.claude/standards/security.md`
Security patterns to flag in every PR: hardcoded secrets, command injection, path traversal, SQL injection, silent failures, error leakage. Includes severity mapping per finding type.

### `.claude/standards/documentation.md`
When to review docs, what makes documentation acceptable (accurate, complete, actionable), code comment review criteria, and markdown quality checklist.

---

## External Skills

### `paperclip`
**Documentation skill only — it loads reference instructions, it does not execute API calls.**

Invoke the `paperclip` skill once at the start of a heartbeat to load the API reference into context. After that, make every actual API call yourself using `Bash` + `curl` against `$PAPERCLIP_API_URL`. Do not pass arguments like `get-task <id>` to the skill — those are not valid commands.

Covers all organizational coordination: task checkout, status updates, comments, and handoff — but via `curl`, not the skill itself.
