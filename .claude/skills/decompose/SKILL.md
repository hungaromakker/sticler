---
name: decompose
description: "Break complex problems into smaller, manageable tasks. Use when a feature or problem needs to be split into right-sized stories for the Ralph loop."
---

# Decompose Skill

Break complex problems into smaller, manageable tasks following the Ralph pattern.

## Purpose

Analyze a complex feature or problem and decompose it into tasks that:
- Each can be completed in ONE context window (~10 min of focused AI work)
- Have clear dependencies and ordering
- Have specific, verifiable acceptance criteria

## Instructions

When the user provides a problem or feature request:

1. **Understand the Scope**: Ask clarifying questions if the requirements are ambiguous

2. **Identify Components**: Break down into logical components (data model, backend, frontend, etc.)

3. **Create Tasks**: For each component, create tasks that are:
   - Small enough for a single context window
   - Have explicit inputs and outputs
   - Include testable acceptance criteria

4. **Order by Dependencies**: Arrange tasks so dependencies are completed first:
   - Schema/data model changes first
   - Backend API second
   - Frontend/UI last
   - Tests alongside each layer

5. **Output Format**: Present tasks with RICH CONTEXT (workers need motivation!):

```markdown
## Task 1: [Task Name]
**Dependencies**: None | Task X, Task Y
**Description**: [What this task does and WHY it matters]

**Why This Matters**:
[2-3 sentences explaining the purpose and how it fits the larger system]

**Goal**: [One sentence summarizing the achievement]

**Acceptance Criteria**:
- [ ] Criterion 1 (must be verifiable, include technical detail)
- [ ] Criterion 2
- [ ] Typecheck passes

**Success Looks Like**: [What can the worker verify when done?]

## Task 2: [Task Name]
...
```

### Why Rich Task Descriptions Matter:

Workers are AI agents iterating in fresh context windows. They need:
- **Context**: Why am I doing this?
- **Goal**: What am I achieving (one sentence)?
- **Success Picture**: How do I know when I'm done?

**Bad task**: Just a checklist → Worker doesn't understand → Poor work
**Good task**: Rich context + goal → Worker understands deeply → Quality work

## Bad vs Good Decomposition

**Too Big** (bad):
- "Implement user authentication" (spans multiple layers, too complex)

**Just Right** (good):
- "Add User model with email/password fields"
- "Create /auth/register POST endpoint"
- "Create /auth/login POST endpoint with JWT"
- "Add login form component"

## Saving Tasks

**IMPORTANT: Finding the Project Directory**

Before saving tasks, find the correct project directory:
1. Check `.magicm/inventory/projects.json` for the `project_dir` field
2. **NEVER save files inside MagicM directory**
3. Save tasks inside the project's state file

### Save Tasks to Project State

After decomposition, save tasks to the project state using the API or state file:

```python
# Tasks should be saved to .magicm/state/{project_id}_state.json
# Each task should have:
{
    "id": "US-001",
    "title": "Task title",
    "description": "As a [user], I want [feature] so that [benefit]",
    "acceptance_criteria": [
        "Criterion 1",
        "Criterion 2",
        "Typecheck passes"
    ],
    "status": "pending",  # pending | in_progress | completed
    "dependencies": []  # List of task IDs this depends on
}
```

The tasks array should be saved to the project state:
```json
{
    "project_id": "food",
    "tasks": [
        {"id": "US-001", ...},
        {"id": "US-002", ...}
    ]
}
```

## Files Required for Phase Detection

The MagicM app detects decompose phase completion by looking for:
- **Tasks in state file** - `state.get("tasks", [])` must be non-empty

The app checks `.magicm/state/{project_id}_state.json` for the `tasks` array.
If tasks exist, the phase advances to ORCHESTRATING.

## Key Principles

### Size:
- If you can't describe the change in 2-3 sentences, it's too big

### Rich Descriptions (CRITICAL):
- **EVERY task must have "Why This Matters" context**
- **EVERY task must have a "Goal" statement (one sentence)**
- **EVERY task should have "Success Looks Like" picture**
- Workers need motivation, not just checklists!

### Acceptance Criteria:
- Every task must have "Typecheck passes" as acceptance criterion
- UI tasks should include "Verify changes work in browser"
- Avoid vague criteria: "Works correctly", "Good UX", "Handles edge cases"

### Saving:
- **Always save tasks to the project state file for phase detection**
