---
name: prd
description: "Generate a Product Requirements Document (PRD) for a new feature. Use when planning a feature, starting a new project, or when asked to create a PRD. Triggers on: create a prd, write prd for, plan this feature, requirements for, spec out."
---

# PRD Generator

Create detailed Product Requirements Documents that are clear, actionable, and suitable for autonomous AI implementation via the Ralph loop.

---

## The Job

1. Receive a feature description from the user
2. **Ask for a project name** (lowercase, no spaces, use underscores - e.g., `user_auth`, `bootybay`)
3. Ask 3-5 essential clarifying questions (with lettered options)
4. Generate a structured PRD based on answers
5. Save to `PRD_<projectname>.md` (e.g., `PRD_bootybay.md`)
6. Create empty `progress_<projectname>.txt`

**Important:** Do NOT start implementing. Just create the PRD.

---

## Step 1: Clarifying Questions

Ask only critical questions where the initial prompt is ambiguous. Focus on:

- **Problem/Goal:** What problem does this solve?
- **Core Functionality:** What are the key actions?
- **Scope/Boundaries:** What should it NOT do?
- **Success Criteria:** How do we know it's done?

### Format Questions Like This:

```
1. What is the primary goal of this feature?
   A. Improve user onboarding experience
   B. Increase user retention
   C. Reduce support burden
   D. Other: [please specify]

2. Who is the target user?
   A. New users only
   B. Existing users only
   C. All users
   D. Admin users only

3. What is the scope?
   A. Minimal viable version
   B. Full-featured implementation
   C. Just the backend/API
   D. Just the UI
```

This lets users respond with "1A, 2C, 3B" for quick iteration.

---

## Step 2: Story Sizing (THE NUMBER ONE RULE)

**Each story must be completable in ONE context window (~10 min of AI work).**

Ralph spawns a fresh instance per iteration with no memory of previous work. If a story is too big, the AI runs out of context before finishing and produces broken code.

### Right-sized stories:
- Add a database column and migration
- Add a single UI component to an existing page
- Update a server action with new logic
- Add a filter dropdown to a list

### Too big (MUST split):
| Too Big | Split Into |
|---------|-----------|
| "Build the dashboard" | Schema, queries, UI components, filters |
| "Add authentication" | Schema, middleware, login UI, session handling |
| "Add drag and drop" | Drag events, drop zones, state update, persistence |
| "Refactor the API" | One story per endpoint or pattern |

**Rule of thumb:** If you cannot describe the change in 2-3 sentences, it is too big.

---

## Step 3: Story Ordering (Dependencies First)

Stories execute in priority order. Earlier stories must NOT depend on later ones.

**Correct order:**
1. Schema/database changes (migrations)
2. Server actions / backend logic
3. UI components that use the backend
4. Dashboard/summary views that aggregate data

**Wrong order:**
```
US-001: UI component (depends on schema that doesn't exist yet!)
US-002: Schema change
```

---

## Step 3b: Maximize Parallelism (CRITICAL FOR SPEED)

The orchestrator can run **up to 10 workers in parallel**, but ONLY if tasks have their dependencies satisfied. Tasks waiting on dependencies cannot start.

### How the Orchestrator Assigns Work

```
Worker Slots Available: 10
Ready Tasks (dependencies met): 2  → Only 2 workers assigned!
```

If only 2 tasks are "ready", 8 worker slots sit idle. **Structure your PRD to maximize ready tasks.**

### Bad Structure (Sequential Bottleneck)
```
US-001: Create user table         (no deps)
US-002: Create auth middleware    (depends: US-001)  ← waits
US-003: Create login endpoint     (depends: US-002)  ← waits
US-004: Create signup endpoint    (depends: US-002)  ← waits
US-005: Create profile endpoint   (depends: US-001)  ← waits
```
Result: Only 1 task runs at a time. 10 workers but using only 1!

### Good Structure (Parallel Execution)
```
Phase 1 - Foundation (NO inter-dependencies, all run in parallel):
US-001: Create user table schema      (no deps)
US-002: Create session table schema   (no deps)
US-003: Create auth config file       (no deps)
US-004: Create API route structure    (no deps)
US-005: Create error handling utils   (no deps)

Phase 2 - Core Logic (depends only on Phase 1):
US-006: Auth middleware       (depends: US-001, US-003)
US-007: Login endpoint        (depends: US-001, US-002)
US-008: Signup endpoint       (depends: US-001)
US-009: Session validation    (depends: US-002, US-003)
US-010: Profile endpoint      (depends: US-001)
```
Result: Phase 1 = 5 tasks run in parallel. Phase 2 = 5 tasks run in parallel. **10x faster!**

### Rules for Maximum Parallelism

1. **Group independent tasks together** - If tasks don't need each other's output, they should have no dependencies between them
2. **Minimize dependency chains** - Long chains (A→B→C→D→E) force sequential execution
3. **Fan out, then fan in** - Many tasks depend on one foundation, then one summary depends on many
4. **Schema tasks have NO dependencies** - Database tables can be created in parallel
5. **UI components are often independent** - Button, Modal, Card can be built in parallel

### Dependency Declaration Format

In each story, explicitly declare dependencies:
```markdown
### US-007: Login endpoint
**Dependencies:** US-001, US-002
**Description:** ...
```

The orchestrator reads these to determine which tasks can run in parallel.

---

## Step 3c: Validation Checkpoints (QUALITY GATES)

After each major phase, include a **validation task** that checks all files created in that phase for coherence, naming conventions, and type safety. This catches issues early before they compound.

### Add Validation Tasks Between Phases

```markdown
Phase 1 - Foundation (parallel):
US-001: Create user table schema
US-002: Create session table schema
US-003: Create API route structure

### US-004: Phase 1 Validation Checkpoint
**Dependencies:** US-001, US-002, US-003
**Description:** Validate all Phase 1 files for consistency and type safety.

**Acceptance Criteria:**
- [ ] All files pass typecheck (tsc --noEmit or mypy)
- [ ] Naming conventions are consistent (snake_case for files, PascalCase for classes)
- [ ] No circular imports between modules
- [ ] All exports are properly typed
- [ ] Database schema files use consistent column naming
- [ ] Typecheck passes

Phase 2 - Core Logic (parallel, depends on US-004):
US-005: Auth middleware (depends: US-004)
US-006: Login endpoint (depends: US-004)
...

### US-010: Phase 2 Validation Checkpoint
**Dependencies:** US-005, US-006, ...
...
```

### What Validation Tasks Should Check

1. **Type Safety**
   - All TypeScript/Python files pass type checking
   - No `any` types used without justification
   - Function signatures have proper type annotations

2. **Naming Coherence**
   - File names follow project convention
   - Class/function names are descriptive and consistent
   - Database columns/tables use same naming style

3. **Import Structure**
   - No circular dependencies
   - Imports organized consistently
   - No unused imports

4. **Integration Points**
   - API endpoints match expected routes
   - Database schemas align with models
   - Types exported match what consumers expect

### Validation Task Template

```markdown
### US-XXX: Phase N Validation Checkpoint
**Dependencies:** [all tasks in Phase N]
**Description:** Validate all files created in Phase N for type safety, naming coherence, and integration readiness.

**Acceptance Criteria:**
- [ ] All new files pass typecheck
- [ ] Naming conventions match project standard
- [ ] No circular imports introduced
- [ ] All new types/interfaces properly exported
- [ ] Integration points verified (routes match, schemas align)
- [ ] Typecheck passes
```

---

## Step 4: Write RICH Story Descriptions (CRITICAL FOR WORKER MOTIVATION)

Workers are AI agents that iterate on tasks. They need CONTEXT and GOALS, not just checklists!

### Bad Story (Just Checklists):
```markdown
### US-004: Create grid data structures
**Description:** As a developer, I need data structures for the grid system.

**Acceptance Criteria:**
- [ ] Create `src/grid.rs`
- [ ] Add `GridVertex` struct with x, y, z fields
- [ ] Typecheck passes
```

### Good Story (Rich Context + Real Goal):
```markdown
### US-004: Create grid data structures
**Dependencies:** None
**Description:** As a developer, I need data structures that represent the core innovation: the **isometric grid drawing system**.

**The Core Concept:** Players draw by connecting vertices (intersection points) with lines. This is like a 3D "connect the dots" - you can only draw lines between grid vertices, creating structures like engineering blueprints.

**Why This Data Structure Matters:**
- `GridVertex` represents a point in 3D isometric space that players click and connect
- `LineSegment` represents a beam between two vertices - becomes a physical 3D beam
- Vertex coordinates are INTEGER (snap to grid, no floating point)
- When lines share a vertex, that vertex becomes a physics JOINT!

**Goal:** Create data structures enabling vertex-to-vertex drawing that later converts to 3D physics beams.

**Acceptance Criteria:**
- [ ] Create `src/grid.rs` with `GridVertex` struct
- [ ] `GridVertex` uses integer coords `x: i32, y: i32, z: i32`
- [ ] Implement `PartialEq, Eq, Hash` for HashMap key usage (needed for joint detection)
- [ ] Create `LineSegment` connecting two `GridVertex` points
- [ ] Typecheck passes

**Success looks like:** A worker can create vertices, create line segments, and use vertices as HashMap keys. Clear foundation for the drawing system.
```

### What Makes a Good Story:

1. **Context Block**: Explain WHAT this is and WHY it matters
2. **Technical Details**: HOW it should work (algorithms, data flow)
3. **Goal Statement**: One sentence summarizing the achievement
4. **Success Looks Like**: Paint a picture of the completed work

### Workers Need Motivation!

Think of workers as engineers who need to understand the WHY:
- "Why am I creating this struct?" → It enables the core game mechanic
- "What's the reward?" → A working system that does X
- "How do I know I succeeded?" → Success looks like Y

---

## Step 5: Acceptance Criteria (Must Be Verifiable)

Each criterion must be something the worker can CHECK, not something vague.

### Good criteria (verifiable):
- "Add `status` column to tasks table with default 'pending'"
- "Filter dropdown has options: All, Active, Completed"
- "Clicking delete shows confirmation dialog"
- "Typecheck passes"
- "Tests pass"

### Bad criteria (vague):
- "Works correctly"
- "User can do X easily"
- "Good UX"
- "Handles edge cases"

### Always include as final criterion:
```
"Typecheck passes"
```

### For stories that change UI, also include:
```
"Verify changes work in browser"
```

---

## PRD Structure

Generate the PRD with these sections:

### 1. Introduction
Brief description of the feature and the problem it solves.

### 2. Goals (With "Achieved When" Criteria)
Not just bullet points - include concrete success criteria:
```markdown
## Goals

### Primary Goal: [Main Feature Name]
[Description of what this achieves]

**Achieved when:**
- [Concrete, testable criterion]
- [Another concrete criterion]
- The system feels [qualitative description]

### Secondary Goals:
- [Other objectives with brief descriptions]
```

### 3. User Stories (RICH FORMAT - CRITICAL!)
Each story needs context, goals, and success criteria - NOT just checklists!

**Required Format:**
```markdown
### US-001: [Title]
**Dependencies:** [US-XXX, US-YYY] or None
**Description:** As a [user], I want [feature] - [additional context about WHY].

**[The Core Concept / Why This Matters / Technical Details]:**
[2-5 sentences explaining the purpose, context, and importance of this story]

**Goal:** [One sentence summarizing what this story achieves]

**Acceptance Criteria:**
- [ ] Specific verifiable criterion
- [ ] Another criterion with technical detail
- [ ] Typecheck passes
- [ ] [UI stories] Verify changes work in browser

**Success looks like:** [Paint a picture of the completed work - what can the worker do/see when done?]
```

### Key Elements for Every Story:
| Element | Purpose | Required? |
|---------|---------|-----------|
| Dependencies | Order stories correctly | Yes |
| Context Block | Explain WHY this matters | Yes |
| Goal Statement | One-sentence achievement | Yes |
| Acceptance Criteria | Verifiable checklist | Yes |
| Success Looks Like | Picture of completion | Recommended |

### 4. Non-Goals
What this feature will NOT include. Critical for scope.

### 5. Technical Considerations (Optional)
- Known constraints
- Existing components to reuse

---

## Example PRD

```markdown
# PRD: Task Priority System

## Introduction

Add priority levels to tasks so users can focus on what matters most. Tasks can be marked as high, medium, or low priority, with visual indicators and filtering.

## Goals

### Primary Goal: Priority-Based Task Organization
Enable users to categorize tasks by importance and quickly find what needs attention.

**Achieved when:**
- Every task has a priority level (high/medium/low)
- Priority is visually distinct at a glance
- Users can filter to see only high-priority tasks
- The system feels intuitive and helpful

### Secondary Goals:
- Default new tasks to medium priority (sensible default)
- Persist filter state in URL (shareable filtered views)

## User Stories

### US-001: Add priority field to database
**Dependencies:** None
**Description:** As a developer, I need to store task priority so it persists across sessions.

**Why This Matters:**
Priority is a core data attribute that affects how users interact with their tasks. Without persistent storage, users would lose their priority assignments on page refresh.

**Goal:** Add priority column to tasks table with sensible default.

**Acceptance Criteria:**
- [ ] Add priority column to tasks table: 'high' | 'medium' | 'low'
- [ ] Default value: 'medium' (most tasks are medium priority)
- [ ] Generate and run migration successfully
- [ ] Existing tasks get 'medium' priority (backfill)
- [ ] Typecheck passes

**Success looks like:** Query any task, it has a priority field. New tasks default to 'medium'.

### US-002: Display priority indicator on task cards
**Dependencies:** US-001
**Description:** As a user, I want to see task priority at a glance so I know what needs attention first.

**The Visual Design:**
Priority should be immediately visible without interaction:
- Red badge = HIGH priority (urgent, needs attention)
- Yellow badge = MEDIUM priority (normal importance)
- Gray badge = LOW priority (can wait)

**Goal:** Make priority visible on every task card instantly.

**Acceptance Criteria:**
- [ ] Each task card shows colored priority badge in corner
- [ ] Colors: red=high, yellow=medium, gray=low
- [ ] Badge has text label: "HIGH", "MED", "LOW"
- [ ] Priority visible without hovering or clicking
- [ ] Badge doesn't obstruct task title or actions
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Open task list, immediately see which tasks are high priority by the red badges.

### US-003: Add priority selector to task edit
**Dependencies:** US-001
**Description:** As a user, I want to change a task's priority when editing it.

**Interaction Design:**
Users should be able to change priority quickly without saving the whole form:
- Dropdown shows current priority
- Selecting new priority saves immediately
- Visual feedback confirms change

**Goal:** Enable quick priority changes from the edit modal.

**Acceptance Criteria:**
- [ ] Priority dropdown in task edit modal
- [ ] Dropdown shows current priority as selected
- [ ] Selecting new priority saves immediately (no save button needed)
- [ ] Badge on card updates in real-time after change
- [ ] Success toast: "Priority updated to [level]"
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Open task edit, change priority to HIGH, close modal, card now shows red HIGH badge.

### US-004: Filter tasks by priority
**Dependencies:** US-002
**Description:** As a user, I want to filter the task list to see only high-priority items when I'm focused.

**Filter UX:**
When users are in "focus mode", they want to see ONLY high-priority tasks:
- Filter dropdown above task list
- Selecting "High" hides medium and low tasks
- URL updates so filter can be bookmarked/shared

**Goal:** Let users focus on what matters by hiding lower-priority tasks.

**Acceptance Criteria:**
- [ ] Filter dropdown with options: All | High | Medium | Low
- [ ] Selecting filter immediately updates visible tasks
- [ ] Filter state persists in URL params (?priority=high)
- [ ] Page load with URL param applies filter automatically
- [ ] Empty state message when no tasks match filter
- [ ] "Clear filter" button to reset to All
- [ ] Typecheck passes
- [ ] Verify changes work in browser

**Success looks like:** Click "High" filter, only high-priority tasks visible. Copy URL, paste in new tab, same filter applied.

## Non-Goals

- No priority-based notifications or reminders
- No automatic priority assignment based on due date
- No priority inheritance for subtasks

## Technical Considerations

- Reuse existing badge component with color variants
- Filter state managed via URL search params
```

---

## Output

**Important:**
1. Ask the user for a project name (lowercase, no spaces, use underscores)
2. **CRITICAL: Determine the PROJECT DIRECTORY before saving files**

### Finding the Project Directory

The project directory is **NOT** the MagicM directory. Look for these patterns:
- If working on a project context (e.g., "food" project), the directory is typically `../food/` or at the same level as MagicM
- Check the inventory file at `.magicm/inventory/projects.json` - each project has a `project_dir` field
- Ask the user to confirm the project directory path if unsure

### Save Files to the Project Directory

Save files **INSIDE the project directory**, not in MagicM:

1. **PRD file:** `<project_dir>/PRD_<projectname>.md`
2. **Progress file:** `<project_dir>/progress_<projectname>.txt`

Example for project "food" with directory `/home/user/magicm/food/`:
- PRD file: `/home/user/magicm/food/PRD_food.md`
- Progress file: `/home/user/magicm/food/progress_food.txt`

Also create `progress_<projectname>.txt`:
```markdown
# Progress Log - <projectname>

## Learnings
(Patterns discovered during implementation)

---
```

### Before Saving - Verify Location

1. Read the inventory to find the project directory:
   ```bash
   cat .magicm/inventory/projects.json
   ```
2. Look for the `project_dir` field for your project
3. Save all files to that directory

---

## Files Required for Phase Detection

The MagicM app detects PRD phase completion by looking for:
- `PRD_*.md` (e.g., `PRD_food.md`) - **REQUIRED** (must start with `PRD_`)

Make sure the PRD file starts with `PRD_` prefix for automatic phase detection.

## Checklist Before Saving

### Project Setup:
- [ ] Asked for project name (lowercase, underscores, no spaces)
- [ ] Asked clarifying questions with lettered options
- [ ] Incorporated user's answers

### Story Quality (CRITICAL):
- [ ] User stories use US-001 format with Dependencies field
- [ ] **EVERY story has context block explaining WHY it matters**
- [ ] **EVERY story has a Goal statement (one sentence)**
- [ ] **Stories include "Success looks like" description**
- [ ] Each story completable in ONE iteration (small enough)
- [ ] Stories ordered by dependency (schema → backend → frontend)

### Goals Section:
- [ ] Goals have "Achieved when" criteria (not just bullet points)
- [ ] Primary goal clearly stated with success criteria

### Acceptance Criteria:
- [ ] All criteria are verifiable (not vague)
- [ ] Every story has "Typecheck passes" as criterion
- [ ] UI stories have "Verify changes work in browser"

### Boundaries:
- [ ] Non-goals section defines clear boundaries

### File Saving:
- [ ] Saved `PRD_<projectname>.md` to PROJECT DIRECTORY (not MagicM!)
- [ ] Saved `progress_<projectname>.txt` to PROJECT DIRECTORY
