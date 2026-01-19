# Planning Plugin for Claude Code

A lightweight feature planning workflow that helps you plan, track, and implement features from conception to completion.

## Installation

```bash
/plugin marketplace add /path/to/claude-tasks
/plugin install planning@claude-tasks
```

## Commands

| Command | Description |
|---------|-------------|
| `/planning:feature` | Start a new feature with guided planning |
| `/planning:step` | Work on the next step of the active feature |
| `/planning:set` | Switch active feature or list all features |

---

## Quick Start

```bash
# Start a new feature
/planning:feature

# Work on the next step
/planning:step

# Switch between features
/planning:set
```

---

## Feature Types

The plugin supports two types of features:

### Simple Features
- Single implementation session
- No multi-step plan needed
- Creates only `feature.md`

### Complex Features
- Multiple distinct phases
- Requires step-by-step plan
- Creates both `feature.md` and `plan.md`

---

## Examples

### Example 1: Simple Feature (No Steps)

A login form that can be implemented in one session.

```bash
/planning:feature login-form "Add a basic login form to the app"
```

**What happens:**
1. Claude asks 3-5 clarifying questions:
   - "Where should the login form appear?"
   - "What fields are required (email/password, username/password)?"
   - "Should it include 'forgot password' link?"
2. Claude explores your codebase for existing patterns
3. Creates `.planning/features/login-form/feature.md`
4. Sets as active feature

**Generated `feature.md`:**
```markdown
# Feature: login-form

## Summary
Add a basic login form to the app

## Type
simple

## Goals
- Allow users to authenticate with email and password
- Redirect to dashboard on successful login

## Requirements
### Functional
- Email input with validation
- Password input with show/hide toggle
- Submit button with loading state
- Error message display

### Non-functional
- Must match existing form styling
- Accessible (proper labels, focus states)

## Out of Scope
- Social login (OAuth)
- Remember me functionality
- Password reset flow

## Technical Context
- **Patterns**: Use existing `FormInput` component from `src/components/ui`
- **Key Files**: `src/pages/auth/`, `src/hooks/useAuth.ts`

## Testing
- Form renders with all fields
- Validation errors display correctly
- Successful login redirects to dashboard
```

**Implementing:**
```bash
/planning:step
```

Claude reads the feature.md and guides you through implementation in a single session.

---

### Example 2: Complex Feature (With Steps)

A full authentication system requiring multiple phases.

```bash
/planning:feature auth-system "Complete authentication with OAuth, sessions, and role-based access"
```

**What happens:**
1. Claude asks clarifying questions:
   - "Which OAuth providers? (Google, GitHub, etc.)"
   - "How should sessions be managed? (JWT, server-side)"
   - "What roles are needed? (admin, user, guest)"
   - "Do you need email verification?"
2. Claude explores codebase for auth patterns, middleware, database schema
3. Determines this is complex (multiple distinct phases)
4. Creates `feature.md` with full requirements
5. Proposes a step-by-step plan for approval
6. Creates `plan.md` after user approves

**Generated `plan.md`:**
```markdown
# Plan: auth-system

## Steps

### Step 1: Database Schema & Models
**Status**: pending

**Scope**: Set up user model with roles and session storage

**Tasks**:
- [ ] Create User model with email, password hash, role fields
- [ ] Create Session model for server-side sessions
- [ ] Add database migrations
- [ ] Create seed data for testing

**Done when**: Models created, migrations run, can create users via REPL

**Test**: `npm run db:seed` creates test users successfully

---

### Step 2: OAuth Provider Setup
**Status**: pending

**Scope**: Configure Google and GitHub OAuth

**Tasks**:
- [ ] Set up OAuth credentials in provider dashboards
- [ ] Add environment variables for client IDs/secrets
- [ ] Create OAuth callback routes
- [ ] Implement token exchange logic

**Done when**: Can initiate OAuth flow and receive tokens

**Test**: Click "Login with Google" → redirects → returns with user info

---

### Step 3: Session Management
**Status**: pending

**Scope**: Implement session creation, validation, and logout

**Tasks**:
- [ ] Create session on successful login
- [ ] Add session validation middleware
- [ ] Implement logout (destroy session)
- [ ] Add session refresh logic

**Done when**: Sessions persist across requests, logout works

**Test**: Login → refresh page → still logged in → logout → redirected to login

---

### Step 4: Role-Based Access Control
**Status**: pending

**Scope**: Add role checking to routes and UI

**Tasks**:
- [ ] Create role-checking middleware
- [ ] Add role guards to protected routes
- [ ] Conditionally render UI based on role
- [ ] Create admin-only test route

**Done when**: Admin can access admin routes, regular users cannot

**Test**: Login as user → /admin returns 403 → login as admin → /admin works

---

### Step 5: Integration & Polish
**Status**: pending

**Scope**: Connect all pieces and handle edge cases

**Tasks**:
- [ ] Add loading states during auth flows
- [ ] Implement proper error messages
- [ ] Add rate limiting to auth endpoints
- [ ] Write integration tests

**Done when**: Full auth flow works end-to-end with good UX

**Test**: Complete user journey from signup to using protected features
```

**Working through steps:**

```bash
# Start step 1
/planning:step

# After completing step 1, continue to step 2
/planning:step

# Jump to a specific step
/planning:step 3
```

Each `/planning:step` call:
1. Shows the current step's scope and tasks
2. Uses the Plan agent for implementation strategy
3. Guides you through the tasks
4. Marks tasks complete as you go
5. Updates the step status when done

---

### Example 3: Switching Between Features

When working on multiple features:

```bash
# List all features
/planning:set
```

Output:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 Available Features
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. auth-system  ← active
   "Complete authentication with OAuth, sessions, and role-based access"
   Type: complex | Progress: 2/5 steps

2. login-form
   "Add a basic login form to the app"
   Type: simple | Status: complete

3. dark-mode
   "Theme toggle for the application"
   Type: simple | Status: pending

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

```bash
# Switch to dark-mode
/planning:set dark-mode
```

Output:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Switched to: dark-mode
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

**Summary**: Theme toggle for the application
**Type**: simple
**Status**: pending

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Run `/planning:step` to start implementation.
```

---

## Project Structure

The plugin creates a `.planning/` directory in your project:

```
your-project/
└── .planning/
    ├── .active              # Current feature name (plain text)
    ├── .gitignore           # Contains: * (ignores all planning files)
    └── features/
        ├── auth-system/
        │   ├── feature.md   # Requirements and scope
        │   └── plan.md      # Steps (complex features only)
        ├── login-form/
        │   └── feature.md   # Simple features only need this
        └── dark-mode/
            └── feature.md
```

---

## Workflow Patterns

### Pattern 1: Quick Feature
For small, well-defined tasks:

```bash
/planning:feature fix-header "Fix header alignment on mobile"
# Answer 1-2 quick questions
# Claude creates simple feature.md
/planning:step
# Implement in one session
# Done!
```

### Pattern 2: Exploratory Feature
When you're not sure of scope:

```bash
/planning:feature improve-perf "Improve app performance"
# Claude asks probing questions to understand scope
# "What feels slow? Page loads? Interactions?"
# "Have you profiled? What did you find?"
# Exploration reveals this needs multiple steps
# Claude creates plan.md with investigation + fix phases
/planning:step  # Step 1: Profile and identify bottlenecks
/planning:step  # Step 2: Fix critical issues
/planning:step  # Step 3: Optimize remaining areas
```

### Pattern 3: Resume Later
Come back to a feature after days/weeks:

```bash
# Check where you left off
/planning:set auth-system

# Output shows: Progress: 2/5 steps, Step 3 is next
/planning:step
# Claude reads plan.md, shows Step 3 context
# Continue right where you left off
```

### Pattern 4: Parallel Features
Work on multiple features:

```bash
# Start main feature
/planning:feature api-v2 "API version 2 with breaking changes"

# Get blocked, start quick fix
/planning:feature hotfix-login "Fix login timeout bug"
/planning:step  # Quick fix
# Done with hotfix

# Return to main feature
/planning:set api-v2
/planning:step  # Continue where you left off
```

---

## Tips

- **Let Claude ask questions** - Don't over-specify upfront. The Q&A process helps refine scope.
- **Trust the type detection** - Claude determines simple vs complex based on your answers.
- **Edit files directly** - feature.md and plan.md are plain markdown. Edit them if needed.
- **Don't over-plan** - Simple features don't need elaborate plans. Let them be simple.
- **Steps are guidelines** - You can skip, combine, or reorder steps as you work.

---

## Troubleshooting

**Skills not appearing?**
- Restart Claude Code after installing the plugin
- Verify with `/planning:feature` (type it directly)

**Wrong feature active?**
- Run `/planning:set` to see and switch features

**Want to start over on a feature?**
- Delete the feature folder: `rm -rf .planning/features/feature-name`
- Run `/planning:feature feature-name "..."` again

**Plan too complex?**
- Edit `.planning/features/name/plan.md` directly
- Remove steps you don't need
- Combine steps that are too granular
