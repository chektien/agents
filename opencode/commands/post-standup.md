---
description: Post standup to Discord (test, production, or dry-run mode)
---

Post the current standup to Discord.

Usage modes:
- `/post-standup -t` - Test mode (sends to test channel)
- `/post-standup -p` - Production mode (sends to real channel)
- `/post-standup -d` - Dry-run mode (shows what would be posted without sending)
- `/post-standup -t -d` - Dry-post test (shows curl command for test webhook)
- `/post-standup -p -d` - Dry-post prod (shows curl command for prod webhook)
- `/post-standup` (no args) - Dry-run (prints message to console)

Running the command:

!`<SCRIPTS_PATH>/post-standup-to-discord.sh $ARGUMENTS`

Before posting to production:
1. Verify the standup content is complete and accurate
2. Ensure all issue links are correct
3. Run a dry-run first to preview

$ARGUMENTS
