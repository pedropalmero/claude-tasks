---
name: planning-feature
description: Start a new feature with guided planning, clarifying questions, and codebase exploration. Use when the user invokes /planning:feature or wants to create a feature plan in .planning.
---

# /planning:feature - Start a New Feature

## Usage

```
/planning:feature <name> "<description>"
```

Examples:
- `/planning:feature user-auth "Add OAuth login with Google and GitHub"`
- `/planning:feature dark-mode "Theme toggle for the application"`

## Instructions

CRITICAL: Path Handling
- ONLY use exact paths specified below (`.planning/`, `.planning/features/<name>/`)
- NEVER use Glob/Search to find existing plan files
- This skill creates the `.planning/` directory structure - do not search for alternatives

You are starting a new feature planning session. Follow these steps carefully:

### Step 1: Parse Arguments

Extract from the command:
- `name`: Feature identifier (kebab-case, e.g., `user-auth`)
- `description`: Brief description in quotes

If arguments are missing, ask the user directly in chat:
- Ask for feature name if not provided
- Ask for brief description if not provided

### Step 2: Initialize Planning Directory

Create the planning structure if it doesn't exist:

```
.planning/
├── .active
├── .gitignore
└── features/
    └── <name>/
```

1. Create `.planning/` directory if missing
2. Create `.planning/.gitignore` with content `*` (ignore all planning files from git)
3. Create `.planning/features/<name>/` directory

### Step 3: Clarifying Questions (3-5 rounds)

Ask questions in chat to clarify:

Round 1 - Core Understanding:
- What problem does this solve?
- Who is the primary user?

Round 2 - Scope:
- What are the must-have requirements?
- What is explicitly out of scope?

Round 3 - Technical Context:
- Are there existing patterns to follow?
- Any specific constraints (performance, compatibility)?

Round 4-5 (if needed):
- Edge cases or error handling
- Integration points with existing systems

Keep questions focused and actionable. Stop when you have enough clarity.

### Step 4: Explore Codebase

Inspect the codebase using shell tools (prefer `rg`, `ls`, `cat`) to find:
- Existing patterns relevant to this feature
- Key files that will be affected or referenced
- Similar implementations to learn from

### Step 5: Determine Complexity

Based on gathered information, determine if this is:

Simple Feature (one-shot implementation):
- Can be completed in a single focused session
- Clear, contained scope
- Few files affected
- No complex dependencies

Complex Feature (multi-step plan needed):
- Multiple distinct phases
- Affects many parts of the codebase
- Requires careful sequencing
- Has dependencies between tasks

### Step 6: Write feature.md

Create `.planning/features/<name>/feature.md` using this template:

```markdown
# Feature: <NAME>

## Summary
<One-liner description>

## Type
<simple | complex>

## Goals
- <Primary goal>
- <Secondary goals>

## Requirements

### Functional
- <What the feature must do>

### Non-functional
- <Performance, security, UX constraints>

## Out of Scope
- <What this feature explicitly won't do>

## Technical Context
- **Patterns**: <Existing patterns to follow>
- **Key Files**: <Files to reference or modify>

## Testing
- <How to verify the feature works>
```

### Step 7: For Complex Features - Create plan.md

If the feature is complex, create `.planning/features/<name>/plan.md`:

1. Propose 3-7 steps that logically sequence the work
2. Present the proposed steps to the user in chat
3. Refine based on feedback
4. Write the plan using this template:

```markdown
# Plan: <NAME>

## Steps

### Step 1: <Title>
**Status**: pending

**Scope**: <What this step accomplishes>

**Tasks**:
- [ ] <Specific task>
- [ ] <Specific task>

**Done when**: <Clear completion criteria>

**Test**: <How to verify this step>

---

### Step 2: <Title>
...
```

### Step 8: Set Active Feature

Write the feature name to `.planning/.active`:

```
<name>
```

### Step 9: Summary

Output a summary:
- Feature name and type (simple/complex)
- Number of steps (if complex)
- Recommended next action (`/planning:step` to begin)

## Output Format

After completing all steps, provide:

1. Confirmation of files created
2. Feature summary from feature.md
3. If complex: list of planned steps
4. Next action recommendation

## Error Handling

- If `.planning/features/<name>/` already exists, ask user if they want to overwrite
- If codebase exploration fails, continue with user-provided context
- Always validate feature name is kebab-case (lowercase, hyphens only)
