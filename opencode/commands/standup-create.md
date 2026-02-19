---
description: Convert DETAILED-TODO items into standup TODO bullets with issue links
---

Convert the DETAILED-TODO section in standup.md into a standup TODO list for today.

Process:
1. Read standup.md and identify all DETAILED-TODO subsections
2. For each subsection (skip NOPOST sections):
   - Search past standup days for the most relevant gh link first
   - Pull existing issues through gh API to check for relevant ones
   - If no relevant issue exists, create via `gh issue create` under the relevant repo
   - Add to the relevant project board using `gh project list --owner <org>` then `gh issue edit --add-project "<project title>"`
   - Assign relevant team members using `gh issue edit --add-assignee <login>` (search for best match github usernames)
   - Create issue with checkbox list matching the sub-bullets from DETAILED-TODO
3. Consolidate multiple items referencing the same issue into a single bullet (CONSOLIDATION RULE)
4. Synthesize tasks into one coherent, high-level description per issue

Synthesis examples:
- Instead of: "fix bug X, add feature Y, update Z"
- Use: "finalize UI/UX enhancements and bug fixes"
- Instead of: "renamed app, removed logo, added icons"
- Use: "cosmetic updates for RN client"

Format for TODO bullets:
```
- <high-level description> <https://github.com/org/repo/issues/N>
```

Rules:
- Skip sections/tasks starting with NOPOST
- Check if standup item already exists before creating duplicates
- Match the format of existing entries
- **NO COMMIT/PUSH**: Omit routine git operations from standup items
- Ask for review before posting to Discord

Standup file: <YOUR_STANDUP_FILE_PATH>

$ARGUMENTS
