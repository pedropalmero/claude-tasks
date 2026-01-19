---
name: step
description: Work on the next step of the active feature plan
---

# /planning:step - Work on Next Step

## Usage

```
/planning:step [step_number]
```

**Examples:**
- `/planning:step` - Work on the next incomplete step
- `/planning:step 2` - Jump to step 2 specifically

## Instructions

You are continuing work on a feature. Follow these steps:

### Step 1: Get Active Feature

Read `.planning/.active` to get the current feature name.

If the file doesn't exist or is empty:
- List available features in `.planning/features/`
- Use AskUserQuestion to let user select one
- Write selection to `.planning/.active`

### Step 2: Load Feature Context

Read the feature files:
1. `.planning/features/<name>/feature.md` - Requirements and scope
2. `.planning/features/<name>/plan.md` - Steps (if exists)

**If feature.md doesn't exist:** Error - feature not properly initialized. Suggest `/planning:feature`.

**If plan.md doesn't exist:** This is a simple feature. Proceed directly to implementation guidance based on feature.md.

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

For steps that involve significant implementation, use the **Task** tool with `subagent_type: "Plan"` to:
- Analyze the step requirements against the codebase
- Identify specific files to create or modify
- Propose implementation approach
- Flag potential issues or dependencies

Present the plan to the user before proceeding.

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

For features without a plan.md (simple features):

1. Display feature.md requirements
2. Use Plan agent if implementation is non-trivial
3. Guide through implementation
4. Mark feature as complete in feature.md by adding:
   ```
   ## Status
   complete
   ```

## Error Handling

- No active feature: List available and prompt selection
- Step number out of range: Show valid range
- All steps complete: Suggest finalizing feature
- Feature files missing: Suggest `/planning:feature` to initialize
