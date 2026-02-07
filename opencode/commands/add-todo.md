---
description: Create GitHub issues from DETAILED-TODO items and link them to standup.md
---

Create GitHub issues for DETAILED-TODO items that don't have issue links yet.

Usage:
- `/add-todo today` - Process today's DETAILED-TODO items
- `/add-todo tomorrow` - Process tomorrow's DETAILED-TODO items

Process:
1. Check the current date to determine which standup section to target (today or tomorrow based on $ARGUMENTS)
2. Read DETAILED-TODO section in standup.md for that date
3. For each unchecked item `[ ]` without a GitHub issue URL:
   - Search existing GitHub issues in relevant repos to avoid duplicates
   - If no existing issue found, create a new issue with:
     * Title: Brief task description
     * Body: Context from DETAILED-TODO, any notes from the description
     * Labels: appropriate labels based on repo (check existing labels first)
     * Assignee: determine based on task context or repo defaults
   - Add the issue to relevant GitHub Project if applicable
4. Update standup.md DETAILED-TODO item with the new issue URL
5. Add the newly created issue to the TODO section in the relevant standup post (not DETAILED-TODO, the regular TODO bullet list):
   - Format: `- [ ] <brief description> <https://github.com/org/repo/issues/N>`
   - Use high-level description
   - Post to the correct date section (today or tomorrow)
6. Cross-reference with existing TODO items to avoid creating duplicates

Format:
```
DETAILED-TODO:
- [ ] <detailed task description with context> <https://github.com/org/repo/issues/N>

TODO:
- [ ] <brief description> <https://github.com/org/repo/issues/N>
```

Rules:
- Only process unchecked items `[ ]` - skip already checked `[x]`
- Only create issues for items WITHOUT existing GitHub URLs
- Search existing issues first using `gh issue list` with relevant keywords
- Use high-level descriptions for issue titles
- Add appropriate context in issue body from DETAILED-TODO notes
- Assign to correct person based on task type (use `gh issue edit --add-assignee`)
- Add to relevant Project using `gh project list --owner <org>` then `gh issue edit --add-project`
- Update standup.md with the new issue URL immediately after creation
- Do not create issues for NOPOST tasks
- Check whether the target date already exists in standup.md
- Match the date format used in standup.md headers (e.g., "Thu Feb 5, 2026")

Issue Creation Strategy:
- For bugs: label as "bug" or "fix"
- For features: label as "enhancement" or "feature"
- For documentation: label as "documentation"
- For research: label as "research" or "investigation"
- If unsure, check the repo's existing label scheme first

Standup file: <YOUR_STANDUP_FILE_PATH>

$ARGUMENTS
