# Tools Reference

Skills and agents available in this project. All files live under `.claude/`.

---

## Skills

### `/feature-dev <description>`
**File**: `.claude/skills/feature-dev.md`

8-phase workflow for implementing new features.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Discovery | Clarify the request | — |
| 2. Codebase Exploration | Understand existing code | `code-explorer` ×2-3 parallel |
| 3. Clarifying Questions | Resolve ambiguities | — |
| 4. Architecture Design | Design implementation approaches | `code-architect` ×2-3 parallel |
| 5. Implementation | Build the feature | — |
| 6. Test Writing | Write comprehensive tests | `code-tester` ×1 |
| 7. Quality Review | Review for bugs, quality, conventions | `code-reviewer` ×3 parallel |
| 8. Summary | Document what was built | — |

---

### `/fix <description>`
**File**: `.claude/skills/fix.md`

8-phase workflow for fixing bugs.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Triage | Understand the bug report | — |
| 2. Investigation | Trace root cause from two angles | `bug-investigator` ×2 parallel |
| 3. Root Cause Confirmation | Confirm cause with user | — |
| 4. Fix Design | Design the minimal fix | — |
| 5. Implementation | Apply the fix | — |
| 6. Regression Testing | Write a test that catches the bug | `code-tester` ×1 |
| 7. Quality Review | Review fix for correctness | `code-reviewer` ×2 parallel |
| 8. Summary | Document the bug and fix | — |

---

## Agents

### `bug-investigator`
**File**: `.claude/agents/bug-investigator.md`
**Used by**: `/fix` Phase 2

Traces execution paths to find root cause and assess blast radius. Launched in pairs with different angles:
- **Execution trace**: Maps the call chain step-by-step to the failure point
- **Causal analysis**: Examines surrounding code, assumptions, and related components

---

### `code-explorer`
**File**: `.claude/agents/code-explorer.md`
**Used by**: `/feature-dev` Phase 2

Deep codebase exploration before building. Maps architecture, traces execution flows, identifies patterns and integration points. Returns entry points, execution flow, key components, and a prioritized file list.

---

### `code-architect`
**File**: `.claude/agents/code-architect.md`
**Used by**: `/feature-dev` Phase 4

Designs implementation approaches with different trade-off lenses:
- **Minimal**: Smallest diff, maximum reuse
- **Clean**: Long-term maintainability, proper abstractions
- **Pragmatic**: Balanced speed and quality

Outputs: patterns found, architecture decision, component design, implementation map, build sequence.

---

### `code-tester`
**File**: `.claude/agents/code-tester.md`
**Used by**: `/feature-dev` Phase 6, `/fix` Phase 6

Writes tests following existing project test patterns. For features: covers happy paths, error paths, and edge cases. For bugs: writes a regression test that fails before the fix and passes after.

---

### `code-reviewer`
**File**: `.claude/agents/code-reviewer.md`
**Used by**: `/feature-dev` Phase 7 (×3), `/fix` Phase 7 (×2)

Reviews code with a specific lens. Only reports issues with ≥ 80% confidence. Lenses:
- **Simplicity/DRY/Elegance**: Duplication, complexity, readability
- **Bugs/Correctness**: Logic errors, edge cases, error handling
- **Conventions/Abstractions**: Project patterns, abstraction reuse, consistency
