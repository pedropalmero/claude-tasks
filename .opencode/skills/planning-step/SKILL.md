---
name: planning-step
description: Work on the next step of the active feature plan. Use when the user invokes /planning:step to implement a planned feature in .planning.
---

# /planning:step - Work on Next Step

## Usage

```
/planning:step [step_number]
```

Examples:
- `/planning:step` - Work on the next incomplete step
- `/planning:step 2` - Jump to step 2 specifically

## Instructions

CRITICAL: Path Handling
- ONLY read from exact paths specified below
- NEVER use Glob/Search to find plan files
- If `.planning/` directory doesn't exist, immediately suggest `/planning:feature` - do not search for alternatives

You are continuing work on a feature. Follow these steps:

### Step 1: Verify Planning Directory

First, check if `.planning/` directory exists using `ls .planning/`.

If `.planning/` does NOT exist:
- Output: `No planning directory found. Run /planning:feature <name> "<description>" to start a new feature.`
- Stop execution - do not search for alternatives

If `.planning/` exists, read `.planning/.active` to get the current feature name.

If `.active` doesn't exist or is empty:
- List available features in `.planning/features/`
- Ask the user to select one in chat
- Write selection to `.planning/.active`

### Step 2: Load Feature Context

Read the feature files:
1. `.planning/features/<name>/feature.md` - Requirements and scope
2. `.planning/features/<name>/plan.md` - Steps (if exists)

If `feature.md` doesn't exist: Error - feature not properly initialized. Suggest `/planning:feature`.

If `plan.md` doesn't exist: This is a simple feature. Proceed directly to implementation guidance based on `feature.md`.

### Step 3: Find Current Step

If `plan.md` exists, parse it to find:
- The next step with `**Status**: pending`
- Or the specific step number if provided as argument

If all steps are complete:
- Congratulate the user
- Suggest marking feature as done or adding more steps

### Step 4: Display Step Information

Show the user:

```
## Current Step: <Step Number> - <Title>

**Scope**: <What this accomplishes>

**Tasks**:
<List of tasks>

**Done when**: <Completion criteria>

**Test**: <Verification method>
```

### Step 5: Plan Implementation (Complex Steps)

For steps that involve significant implementation:
- Outline a concrete approach
- Identify specific files to create or modify
- Call out dependencies or risks
- Ask the user to confirm before editing files

### Step 6: Guide Implementation

Provide implementation guidance:
1. List specific files to create/modify
2. Show code patterns to follow (from Technical Context)
3. Highlight testing requirements
4. Offer to implement or guide the user

### Step 7: Mark Progress

As tasks are completed, update `plan.md`:
- Change `- [ ]` to `- [x]` for completed tasks
- When all tasks done, change `**Status**: pending` to `**Status**: complete`

### Step 8: Next Actions

After step completion, suggest:
- Run `/planning:step` for next step
- Or indicate feature is complete if all steps done

## Output Format

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Feature: <name>
📍 Step <N> of <Total>: <Title>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**Scope**: <What this accomplishes>

**Tasks**:
- [ ] Task 1
- [ ] Task 2

**Done when**: <Criteria>
**Test**: <Verification>

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Simple Features

For features without a `plan.md` (simple features):

1. Display `feature.md` requirements
2. If implementation is non-trivial, outline the plan and confirm before editing
3. Guide through implementation
4. Mark feature as complete in `feature.md` by adding:

```
## Status
complete
```

## Error Handling

- No active feature: List available and prompt selection
- Step number out of range: Show valid range
- All steps complete: Suggest finalizing feature
- Feature files missing: Suggest `/planning:feature` to initialize
