---
name: fix
description: Bug fix workflow. Guides systematic investigation, root cause analysis, minimal fix implementation, regression testing, and review. Use when fixing a bug or unexpected behavior.
---
name: fix

You are helping a developer fix a bug. Follow this systematic 8-phase approach: triage, investigate, confirm root cause, design the fix, implement, write regression tests, review, summarize.

## Core Principles

- **Find the root cause, not just the symptom**: Don't fix the error message — fix why the error occurs.
- **Minimal footprint**: Bug fixes should be as small and surgical as possible. Only change what's necessary.
- **Regression test is mandatory**: Every bug fix must include a test that would have caught this bug originally.
- **Confirm before fixing**: Always present the root cause to the user and get confirmation before implementing.
- **Read files agents identify**: After agents complete, read the files they identify before proceeding.
- **Track progress**: Use TodoWrite throughout all phases.

---
name: fix

## Phase 1: Triage

**Goal**: Understand the bug clearly before touching any code.

**Bug/Issue**: $ARGUMENTS

**Actions**:
1. Create a todo list covering all 8 phases
2. If the report is unclear, ask the user:
   - What is the expected behavior?
   - What is the actual behavior?
   - How is it reproduced? (steps, environment, frequency)
   - When did it start? Any recent changes?
   - What is the severity and impact?
3. Summarize your understanding of the bug and confirm with the user before investigating

---
name: fix

## Phase 2: Investigation

**Goal**: Trace the code to find the root cause from two angles simultaneously.

**Actions**:

Launch **2 `bug-investigator` agents in parallel**, each with a different focus:
- **Execution trace**: Traces the call chain step by step from entry point to failure point, mapping exact file and line numbers
- **Causal analysis**: Examines surrounding code, related components, and assumptions to understand why this bug exists

Prompt example:
- `"Investigate this bug: [description]. Focus: execution trace. Trace the call chain from [entry point] to the failure, mapping each step with file:line references. Return a list of key files."`
- `"Investigate this bug: [description]. Focus: causal analysis. Examine the surrounding code, assumptions, and related components to understand why this bug exists. Return a list of key files."`

After agents return:
1. Read **all files** identified by both agents
2. Cross-reference both investigation angles to build a complete picture
3. Form a hypothesis about the root cause with supporting evidence

---
name: fix

## Phase 3: Root Cause Confirmation

**Goal**: Confirm the root cause with the user before designing a fix. **DO NOT SKIP.**

**Actions**:
1. Present the root cause clearly:
   - Exact location (`file:line`)
   - What the code is doing wrong and why
   - The blast radius (what else is affected)
   - Any contributing factors (missing validation, test gaps, etc.)
2. Ask the user to confirm this is the correct root cause
3. If the user disagrees or has additional context, go back to Phase 2 with new information
4. Only proceed once root cause is confirmed

---
name: fix

## Phase 4: Fix Design

**Goal**: Design the minimal correct fix.

**Actions**:
1. Based on confirmed root cause, design the fix:
   - **Minimal footprint**: Change only what's necessary to fix the root cause
   - **No opportunistic refactoring**: Do not clean up unrelated code
   - **Preserve existing behavior**: The fix should only change the broken behavior
2. Present the fix design to the user:
   - What exactly will change and where (`file:line`)
   - Why this change fixes the root cause
   - Any risk of unintended side effects
   - Whether any related code has the same bug and should be fixed together
3. Get explicit user approval before implementing

---
name: fix

## Phase 5: Implementation

**Goal**: Apply the fix. **DO NOT START WITHOUT EXPLICIT USER APPROVAL.**

**Actions**:
1. Wait for user approval of the fix design
2. Re-read all relevant files before making changes
3. Apply the fix — minimal and surgical
4. Do not change anything beyond what was approved
5. If you discover unexpected complexity or realize the approved fix is wrong, stop and surface it immediately — do not improvise

---
name: fix

## Phase 6: Regression Testing

**Goal**: Write a test that proves the bug is fixed and prevents it from recurring.

**Actions**:

Launch the **`code-tester` agent** with:
- The bug description and root cause
- The files that were modified
- Instruction to write a regression test that **would have failed before the fix and passes after**

After the agent returns:
1. Read all test files it created
2. Run the test suite to confirm:
   - The regression test passes
   - No existing tests were broken
3. If the fix breaks existing tests, stop and investigate — the fix may be incorrect

---
name: fix

## Phase 7: Quality Review

**Goal**: Verify the fix is correct and has no unintended side effects.

**Actions**:

Launch **2 `code-reviewer` agents in parallel** — fewer than feature review since the change is small, but still two perspectives:
- **Bugs/correctness**: Is the fix actually correct? Does it fully address the root cause? Could it introduce new bugs?
- **Conventions/abstractions**: Does the fix follow project patterns? Is it consistent with how similar issues are handled elsewhere?

After agents return:
1. Consolidate findings
2. Present to user — focus especially on any correctness concerns
3. Address critical issues before proceeding; defer minor ones

---
name: fix

## Phase 8: PR Creation and Summary

**Goal**: Open the pull request, then document the bug and fix for future reference.

**Actions**:
1. Create the pull request using `gh pr create`. **This is mandatory — do not skip, do not treat it as a "next step".** Use the format from `.claude/standards/git.md`:
   ```
   gh pr create --title "<type>(<scope>): <description>" --body "..."
   ```
   PR body must include:
   - **Summary**: What was broken and what was changed
   - **Test plan**: How to verify the fix and confirm the regression test passes
   - Any breaking changes
2. Copy the PR URL from the output.
3. Mark all todos complete
4. Present final summary:
   - **Bug**: What was broken and how it manifested
   - **Root cause**: The exact cause with file and line reference
   - **Fix**: What was changed and why it works
   - **Regression test**: What test was added to prevent recurrence
   - **Blast radius**: Any related areas that were checked or also fixed
   - **Suggested follow-up**: Anything deferred, related tech debt, or systemic issues worth addressing separately
5. Update the Paperclip ticket: set status to `in_review`, assign to QA, and include the PR URL in the comment (see HEARTBEAT.md §7).
