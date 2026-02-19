---
description: Summarize pending DETAILED-TODO items into TODO and create missing issues
---

Summarize pending DETAILED-TODO items into the TODO section for the target date. Create missing GitHub issues when needed and link them back into standup.md.

Usage:
- `/add-todo today` - Process today's DETAILED-TODO items
- `/add-todo tomorrow` - Process tomorrow's DETAILED-TODO items

Process:
1. Check the current date to determine which standup section to target (today or tomorrow based on $ARGUMENTS)
2. Read the DETAILED-TODO section in standup.md for that date
3. Identify pending items: unchecked `[ ]` items plus any DETAILED-TODO bullets without a checkbox
4. For each pending item without a GitHub issue URL:
   - Search existing GitHub issues in relevant repos to avoid duplicates
   - If no existing issue found, create a new issue with:
     * Title: Brief task description
     * Body: Context from DETAILED-TODO, any notes from the description
     * Labels: appropriate labels based on repo (check existing labels first)
     * Assignee: determine based on task context or repo defaults
   - Add the issue to relevant GitHub Project if applicable
   - Update standup.md DETAILED-TODO item with the new issue URL
5. Add each pending item (including ones with existing issue links) to the TODO section for that date:
   - Format: `- <brief description> <https://github.com/org/repo/issues/N>`
   - Use high-level description
   - Post to the correct date section (today or tomorrow)
6. Cross-reference with existing TODO items to avoid creating duplicates

Format:
```
DETAILED-TODO:
- [ ] <detailed task description with context> <https://github.com/org/repo/issues/N>

TODO:
- <brief description> <https://github.com/org/repo/issues/N>
```

Rules:
- Only process pending items (unchecked `[ ]` or bullets without checkboxes); skip already checked `[x]`
- Only create issues for items WITHOUT existing GitHub URLs
- Search existing issues first using `gh issue list` with relevant keywords
- Use high-level descriptions for issue titles
- Add appropriate context in issue body from DETAILED-TODO notes
- Assign to correct person based on task type (use `gh issue edit --add-assignee`)
- Add to relevant Project using `gh project list --owner <org>` then `gh issue edit --add-project`
- Update standup.md with the new issue URL immediately after creation
- Add all pending DETAILED-TODO items to the TODO section, even if an issue link already exists
- Do not add checkboxes in standup TODO or DONE sections
- Do not create issues for NOPOST tasks
- Check whether the target date already exists in standup.md
- Match the date format used in standup.md headers (e.g., "Thu Feb 5, 2026")

Standup file: <YOUR_STANDUP_FILE_PATH>

$ARGUMENTS
