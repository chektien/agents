---
description: Add DONE items to standup.md from recent closed issues/PRs for today or tomorrow
---

Add DONE items to standup.md based on recently completed work.

Usage:
- `/add-done today` - Add DONE items to today's standup section
- `/add-done tomorrow` - Add DONE items to tomorrow's standup section

Process:
1. Check the current date to determine which standup section to target (today or tomorrow based on $ARGUMENTS)
2. Check relevant gh repos for recent closed issues or merged PRs matching the target date's tasks
3. Cross-reference with existing DONE items in that date's section to avoid duplicates
4. Format new DONE bullets with high-level descriptions and issue URLs
5. Post to the correct date section in standup.md

Format:
```
- <brief description of completed work> <https://github.com/org/repo/issues/N>
```

Rules:
- Every DONE bullet MUST include an issue URL in angle brackets
- Use high-level descriptions only
- Omit routine git operations
- Do not add items for NOPOST tasks
- Check whether the target date already exists in standup.md to avoid duplicates
- Match the date format used in standup.md headers (e.g., "Thu Feb 5, 2026")

Standup file: <YOUR_STANDUP_FILE_PATH>

$ARGUMENTS
