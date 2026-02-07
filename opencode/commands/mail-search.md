---
description: Search Apple Mail for student emails and module correspondence
---

Search Apple Mail for emails matching a search term.

Here are search results:

!`<MAIL_SCRIPT_PATH>/scripts/mail.sh search "$ARGUMENTS" 25`

Based on these results:
1. Summarize relevant emails found
2. Identify any that may need replies or follow-up
3. If the user wants to read a specific email, use `<MAIL_SCRIPT_PATH>/scripts/mail.sh open <number>` to open it in Mail.app

Workflow:
1. **Search**: Use the command above to find emails
2. **Open**: If user wants to view an email, run `<MAIL_SCRIPT_PATH>/scripts/mail.sh open <number>`
3. **Read programmatically** (for analysis):
   ```bash
   find ~/Library/Mail/V10 -name "*.emlx" -type f -mtime -7 | xargs grep -l "<search-term>" | head -5 | while read f; do cat "$f" | head -150; done
   ```
4. **Draft replies**: Create draft text for user review - **NEVER send automatically**
   - User must review and send manually via Mail.app

Common search terms: CSD3121, DIA, student names, SIT IDs like 2301234

Related resources:
- Mail scripts: `<MAIL_SCRIPT_PATH>/scripts/mail*.sh`
- Workflow docs: `<NOTES_PATH>/MAIL-WORKFLOW.md`

$ARGUMENTS
