---
description: Convert DETAILED-TODO items into standup TODO bullets with issue links
---

Convert the DETAILED-TODO section in standup.md into a standup TODO list for today.

Process:
1. Read standup.md and identify all DETAILED-TODO subsections
2. For each subsection (skip NOPOST sections):
   - Search past standup days for the most relevant gh link
   - If no relevant issue exists, create one via `gh issue create` under the relevant repo
   - Add to relevant project board if applicable
   - Assign relevant team members
3. Consolidate multiple items referencing the same issue into a single bullet
4. Synthesize tasks into one coherent, high-level description per issue

Format for TODO bullets:
```
- <high-level description> <https://github.com/org/repo/issues/N>
```

Rules:
- Skip sections/tasks starting with NOPOST
- Check if standup item already exists before creating duplicates
- Match the format of existing entries
- Ask for review before posting

Standup file: <YOUR_STANDUP_FILE_PATH>

$ARGUMENTS
