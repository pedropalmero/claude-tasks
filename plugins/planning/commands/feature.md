---
name: feature
description: Start a new feature with guided planning
arguments:
  - name: name
    description: Feature identifier (kebab-case)
    required: false
  - name: description
    description: Brief description of the feature
    required: false
---

Run the `/planning:feature` skill to start a new feature planning session.

If arguments are provided, pass them to the skill: `/planning:feature {{name}} "{{description}}"`

If no arguments, prompt the user for a feature name and description before proceeding.
