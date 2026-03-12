---
name: feature-dev
description: Feature development workflow. Guides systematic implementation of new features through 8 phases: discovery, exploration, clarifying questions, architecture design, implementation, test writing, quality review, and summary.
---
name: feature-dev

You are helping a developer implement a new feature. Follow this systematic 8-phase approach: understand deeply, ask questions, design, implement, test, review.

## Core Principles

- **Ask before assuming**: Identify ambiguities you genuinely cannot resolve yourself. If you have a clear recommendation, state it and proceed — do not block waiting for confirmation.
- **Understand before acting**: Read and comprehend existing code patterns before writing anything.
- **Read files agents identify**: When launching agents, ask them to return lists of key files. After agents complete, read those files to build detailed context.
- **Simple and elegant**: Prioritize readable, maintainable, architecturally sound code.
- **Track progress**: Use TodoWrite throughout all phases.

---
name: feature-dev

## Phase 1: Discovery

**Goal**: Understand what needs to be built.

**Feature/Issue**: $ARGUMENTS

**Actions**:
1. Create a todo list covering all 8 phases
2. If the request is unclear, ask the user:
   - What problem are they solving?
   - What should the feature do?
   - Any constraints, performance requirements, or backward compatibility concerns?
3. Summarize your understanding and confirm with the user before continuing

---
name: feature-dev

## Phase 2: Codebase Exploration

**Goal**: Understand relevant existing code and patterns at both high and low levels.

**Actions**:

Launch **2–3 explorer agents in parallel**, each targeting a different aspect. Use the `code-explorer` agent profile. Each agent should:
- Trace through code comprehensively
- Focus on abstractions, architecture, and control flow
- Return a list of **5–10 key files** to read

Example prompts:
- `"Find features similar to [feature] and trace through their implementation comprehensively. Return a list of the 10 most important files."`
- `"Map the architecture and abstractions for [feature area], tracing the code comprehensively. Return a list of 10 key files."`
- `"Analyze the current implementation of [existing feature/area] and identify integration points. Return a list of 10 key files."`
- `"Identify UI patterns, testing approaches, or extension points relevant to [feature]. Return a list of 10 key files."`

After agents return:
1. Read **all files** identified by the agents
2. Present a comprehensive summary of findings: patterns, conventions, relevant abstractions, integration points

---
name: feature-dev

## Phase 3: Clarifying Questions

**Goal**: Fill in all gaps before designing. **DO NOT SKIP.**

**Actions**:
1. Review codebase findings and the original request
2. Identify underspecified aspects:
   - Edge cases and error handling
   - Integration points and side effects
   - Scope boundaries (what's in/out)
   - Design preferences (e.g. new abstraction vs. extending existing)
   - Backward compatibility requirements
   - Performance or scalability needs
   - Testing requirements
3. For each gap, either: ask the user (if genuinely ambiguous) OR state your recommendation and proceed (if you have a clear answer)
4. Only block and wait if there are questions you truly cannot answer yourself — questions about business intent, user preferences, or constraints only the user knows
5. If you have recommendations for all gaps, state them clearly and proceed to Phase 4 without waiting — document your decisions so the user can review them

---
name: feature-dev

## Phase 4: Architecture Design

**Goal**: Design and evaluate multiple implementation approaches.

**Actions**:

Launch **2–3 architect agents in parallel** with different focuses. Use the `code-architect` agent profile:
- **Minimal changes**: Smallest diff, maximum reuse of existing code
- **Clean architecture**: Long-term maintainability, elegant abstractions
- **Pragmatic balance**: Speed + quality, practical trade-offs

After agents return:
1. Review all approaches
2. Form your own recommendation based on: task size (small fix vs. large feature), complexity, urgency, codebase context
3. Present to user:
   - Brief summary of each approach
   - Trade-offs comparison
   - Your recommendation with concrete reasoning
   - Key implementation differences
4. Ask the user which approach they prefer

---
name: feature-dev

## Phase 5: Implementation

**Goal**: Build the feature.

**Actions**:
1. If user has not responded to the Phase 4 architecture proposal, proceed with your recommended approach — state the choice clearly in a comment first. Only block if there is genuine unresolved ambiguity that would change the implementation significantly.
2. Re-read all relevant files identified in previous phases
3. Implement following the chosen architecture
4. Follow codebase conventions strictly (naming, structure, patterns)
5. Write clean, well-documented code where logic is non-obvious
6. Update todos as you progress through implementation
7. Surface any unexpected complexity or deviations from the plan immediately

---
name: feature-dev

## Phase 6: Test Writing

**Goal**: Write comprehensive tests for the implemented feature following project test patterns.

**Actions**:

Launch a **`code-tester` agent** with the list of all files created or modified in Phase 5. The agent should:
- Analyze the implementation and understand its expected behavior
- Find existing test patterns in the codebase to use as templates
- Write tests covering happy paths, error paths, and edge cases

After the agent returns:
1. Read all test files it created or modified
2. Run the test suite to confirm tests pass
3. Present the coverage summary and any gaps to the user
4. If there are meaningful gaps, decide with the user whether to address them now or defer

---
name: feature-dev

## Phase 7: Quality Review

**Goal**: Ensure code is simple, DRY, elegant, readable, and correct.

**Actions**:

Launch **3 reviewer agents in parallel** with different focuses. Use the `code-reviewer` agent profile. Provide both the implementation files and the test files:
- **Simplicity/DRY/elegance**: Duplication, unnecessary complexity, readability
- **Bugs/correctness**: Logic errors, edge cases, error handling gaps
- **Conventions/abstractions**: Consistency with project patterns, proper use of existing abstractions

After agents return:
1. Consolidate findings, grouped by severity (critical / recommended / minor)
2. Present findings to the user
3. Ask what they want to do: fix now, track for later, or proceed as-is
4. Address issues per user decision

---
name: feature-dev

## Phase 8: PR Creation and Summary

**Goal**: Open the pull request, then document what was accomplished.

**Actions**:
1. Create the pull request using `gh pr create`. **This is mandatory — do not skip, do not treat it as a "next step".** Use the format from `.claude/standards/git.md`:
   ```
   gh pr create --title "<type>(<scope>): <description>" --body "..."
   ```
   PR body must include:
   - **Summary**: What changed and why
   - **Test plan**: How to verify the acceptance criteria
   - Any breaking changes
2. Copy the PR URL from the output.
3. Mark all todos complete
4. Present final summary:
   - **What was built**: Feature description
   - **Key decisions**: Architecture choices made and why
   - **Files modified**: List with brief description of changes
   - **Tests written**: Test files added and what they cover
   - **Known limitations**: Anything deferred or out of scope
   - **Suggested next steps**: Follow-on work, deployment considerations
5. Update the Paperclip ticket: set status to `in_review`, assign to QA, and include the PR URL in the comment (see HEARTBEAT.md §7).
