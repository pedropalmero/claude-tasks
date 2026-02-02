---
name: planning-set
description: Switch the active feature or list all available features. Use when the user invokes /planning:set to manage .planning feature context.
---

# /planning:set - Switch Active Feature

## Usage

```
/planning:set [feature_name]
```

Examples:
- `/planning:set` - List all features and select interactively
- `/planning:set dark-mode` - Switch to the dark-mode feature

## Instructions

CRITICAL: Path Handling
- ONLY read from exact paths specified below
- NEVER use Glob/Search to find plan files
- If `.planning/` directory doesn't exist, immediately suggest `/planning:feature` - do not search for alternatives

You are switching the active feature context. Follow these steps:

### Step 1: Check Planning Directory

First, check if `.planning/` directory exists using `ls .planning/`.

If `.planning/` does NOT exist:
- Output: `No planning directory found. Run /planning:feature <name> "<description>" to start a new feature.`
- Stop execution - do not search for alternatives

Then verify `.planning/features/` exists.

If not:
- Inform user no features have been created yet
- Suggest using `/planning:feature` to create one
- Exit

### Step 2: Handle No Argument (List Mode)

If no feature name provided:

1. List all directories in `.planning/features/`
2. For each feature, read `feature.md` to get:
   - Summary (first line after `## Summary`)
   - Type (simple/complex)
   - Status (if present)
3. Present list and ask the user to pick one in chat

Display format:

```
Available Features:
1. user-auth - "OAuth login with Google" (complex, 2/5 steps)
2. dark-mode - "Theme toggle" (simple, pending)
3. login-form - "Basic login UI" (simple, complete)
```

### Step 3: Validate Feature Name

If feature name provided:
1. Check if `.planning/features/<name>/` exists
2. If not, show error with list of valid features

### Step 4: Update Active Feature

Write the feature name to `.planning/.active`:

```
<feature_name>
```

### Step 5: Show Feature Status

Read and display feature information:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Switched to: <feature_name>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**Summary**: <from feature.md>
**Type**: <simple | complex>
```

For complex features, also show:

```
**Progress**: <N>/<Total> steps complete

Steps:
1. ✅ <Step 1 title>
2. ✅ <Step 2 title>
3. ⏳ <Step 3 title> ← current
4. ⬜ <Step 4 title>
```

### Step 6: Suggest Next Action

Based on status:
- If steps remain: `Run /planning:step to continue`
- If complete: `Feature complete! Consider archiving or starting new feature`
- If simple and pending: `Run /planning:step to implement`

## Output Format

### List Mode (no argument)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Available Features
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. user-auth
   "OAuth login with Google and GitHub"
   Type: complex | Progress: 2/5 steps

2. dark-mode  ← active
   "Theme toggle for the application"
   Type: simple | Status: pending

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Select a feature to switch to, or press Enter to stay on current.
```

### Switch Mode (with argument)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Switched to: user-auth
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**Summary**: OAuth login with Google and GitHub
**Type**: complex
**Progress**: 2/5 steps complete

Steps:
1. ✅ Set up OAuth providers
2. ✅ Create auth endpoints
3. ⏳ Build login UI ← next
4. ⬜ Add session management
5. ⬜ Write tests

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Run /planning:step to continue with Step 3.
```

## Helper Functions

### Parse Step Status

To determine step status from `plan.md`:
- `**Status**: complete` → ✅
- `**Status**: pending` with incomplete tasks → ⏳ (if first pending)
- `**Status**: pending` → ⬜

### Count Progress

Count steps with `**Status**: complete` vs total steps.

## Error Handling

- No `.planning/` directory: Suggest `/planning:feature`
- Feature not found: List valid features
- Corrupted `feature.md`: Suggest recreating with `/planning:feature`
