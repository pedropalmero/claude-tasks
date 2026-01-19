---
name: set
description: Switch active feature or list all features
arguments:
  - name: feature_name
    description: Feature to switch to (optional, lists all if omitted)
    required: false
---

Run the `/planning:set` skill to switch the active feature or list available features.

If a feature name is provided, pass it to the skill: `/planning:set {{feature_name}}`
