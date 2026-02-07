---
description: Post standup to Discord (test, production, or dry-run mode)
---

Post current standup to Discord.

Usage modes:
- `/post-standup test` - Test mode (sends to test channel)
- `/post-standup prod` - Production mode (sends to real channel)
- `/post-standup dry` - Dry-run mode (shows what would be posted without sending)

Running the command:

!`<SCRIPTS_PATH>/scripts/post-standup-to-discord.sh -$ARGUMENTS`

Before posting to production:
1. Verify the standup content is complete and accurate
2. Ensure all issue links are correct
3. Run a dry-run first to preview

$ARGUMENTS
