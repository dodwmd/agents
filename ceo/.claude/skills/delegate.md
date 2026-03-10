---
description: Delegation workflow. Creates well-defined tasks via Paperclip and assigns them to the right agent (developer, qa, or other). Use when the CEO needs to hand off work without it bouncing back for clarification.
---

You are delegating a task to the team. Define success before assigning — a task that comes back for clarification was never delegated, it was deferred.

## Core Principle

The CEO's job is to define the outcome and constraints, not the method. Assign what to achieve, not how to achieve it.

---

## Phase 1: Task Definition

**Task**: $ARGUMENTS

**Actions**:
1. Clarify with CEO:
   - What does success look like? (specific, measurable output)
   - What is the hard deadline?
   - What are the constraints? (scope, budget, tech choices the team can't make independently)
   - What decisions can the assignee make without CEO input?
   - What requires CEO approval before proceeding?
2. Identify the right assignee:
   - **Developer agent**: Feature implementation, bug fixes, technical builds
   - **QA agent**: PR review, test coverage, quality gates
   - **Developer + QA**: Feature with quality review (create parent task + subtasks)
   - **New agent**: If no current agent can own this, use `paperclip-create-agent`

---

## Phase 2: Task Creation

**Actions**:
1. Create task via Paperclip: `POST /api/companies/{companyId}/issues`
2. Required fields:
   - `title`: Action + outcome (e.g., "Implement user authentication on onboarding flow")
   - `description`: Context, acceptance criteria, constraints, what requires CEO sign-off
   - `assigneeAgentId`: Target agent ID
   - `parentId`: Link to goal/epic
   - `goalId`: Required — never create a floating task
   - `priority`: `critical` / `high` / `normal` / `low`
3. For multi-phase work (dev + QA): create parent task first, then subtasks with `parentId` set

---

## Phase 3: Handoff Confirmation

**Actions**:
1. Confirm task created — return task ID
2. Summarize for CEO:
   - **Task ID and title**
   - **Assignee**
   - **Acceptance criteria** — the 2–3 specific things that make this done
   - **CEO sign-off triggers** — what must come back to CEO before proceeding
   - **Check-in date** — when to expect an update if none arrives
