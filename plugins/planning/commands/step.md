---
name: step
description: Work on the next step of the active feature
arguments:
  - name: step_number
    description: Specific step number to work on (optional)
    required: false
---

Run the `/planning:step` skill to continue working on the active feature.

If a step number is provided, pass it to the skill: `/planning:step {{step_number}}`
