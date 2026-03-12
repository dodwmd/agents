---
name: review-pr
description: Comprehensive PR review using specialized QA agents. The branch and PR already exist. Reviews code comments, test coverage, error handling, type design, code quality, and simplification opportunities.
---

You are a QA engineer reviewing an existing pull request. The code is already written. Your job is to analyze it thoroughly and surface actionable findings before it merges.

## Core Principle

Start from the diff. You are reviewing what changed, not the entire codebase.

**Review aspects requested**: $ARGUMENTS (default: all applicable)

---

## Phase 1: Understand the PR

**Actions**:
1. Run `git diff --name-only` to identify all changed files
2. Run `git diff` (or `gh pr diff` if a PR exists) to read the actual changes
3. Check `gh pr view` for the PR title, description, and any reviewer comments
4. Categorize the changes:
   - Which files are new vs modified?
   - Are there test file changes?
   - Are there new or modified types/interfaces?
   - Are there try/catch blocks in the diff?
   - Are there new or modified comments/docs?
5. Summarize the PR to the user: what it does, what changed, what reviews you'll run

---

## Phase 2: Determine Applicable Agents

Based on the diff, select agents to run. If specific aspects were requested via `$ARGUMENTS`, run only those. Otherwise apply this logic:

| Condition | Agent |
|---|---|
| Any code changes (always) | `code-reviewer` |
| Test files in diff | `pr-test-analyzer` |
| Comments or docs in diff | `comment-analyzer` |
| try/catch or error handling in diff | `silent-failure-hunter` |
| New or modified types/interfaces | `type-design-analyzer` |

**Note**: `code-simplifier` is not run automatically — it's a polish pass run after other issues are addressed. Only run it if explicitly requested or if all other reviews come back clean.

---

## Phase 3: Launch Review Agents

Launch all selected agents **in parallel**. Provide each agent with:
- The git diff output
- The list of changed files
- The PR description (if available)
- Any specific focus area context

Each agent returns findings with file:line references, severity, and suggestions.

---

## Phase 4: Consolidate Findings

After all agents return:

1. Deduplicate any overlapping findings across agents
2. Group by severity:
   - **Critical** — must fix before merge (blocking)
   - **Important** — should fix before merge (non-blocking but expected)
   - **Suggestion** — optional improvement

3. Present the full review summary:

```
## PR Review: [PR title or branch name]

### Changes Reviewed
- Files changed: N
- Agents run: [list]

### Critical Issues (N) — Must fix before merge
- [agent]: Description [file:line]

### Important Issues (N) — Should fix before merge
- [agent]: Description [file:line]

### Suggestions (N) — Optional improvements
- [agent]: Description [file:line]

### Strengths
- What's done well in this PR

### Verdict
[APPROVED / NEEDS CHANGES / NEEDS CHANGES (CRITICAL)]
Recommended next steps.
```

4. If no issues found, say so explicitly — a clean review is a useful signal.
