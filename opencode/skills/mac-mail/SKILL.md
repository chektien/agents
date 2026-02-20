---
name: mac-mail
description: Best practices for searching and interacting with Apple Mail on macOS
---

# Apple Mail Operations on macOS

## Overview

This skill provides guidelines for effectively searching, opening, and navigating Apple Mail on macOS using osascript automation.

## Mail Search Workflow

### Using mail-search.sh

The mail-search.sh script searches Apple Mail for specific sender emails:

```bash
osascript <MAIL_SCRIPT_PATH>/scripts/mail.sh search "search terms"
```

**Search patterns:**
- Use sender names, project codes, or keywords
- Searches recent mailboxes (inbox, sent, archives)
- Returns matching email subjects and sender info
- Results displayed in terminal for quick scanning

**Example searches:**
```bash
osascript <MAIL_SCRIPT_PATH>/scripts/mail.sh search "project-name"
osascript <MAIL_SCRIPT_PATH>/scripts/mail.sh search "sender-name"
```

### Interpreting Search Results

Results typically show:
- Message subject
- Sender name and email
- Date received
- Mailbox location

Use these to identify the specific email you need.

## Opening Mail Messages

### Using mail-open.sh

Once you identify the email from search results, open it directly:

```bash
osascript <MAIL_SCRIPT_PATH>/scripts/mail.sh open <number>
```

This brings Apple Mail to foreground with the specific message selected.

### Manual Navigation

If needed, you can guide users to:
1. Activate Apple Mail (Cmd+Tab or click Dock)
2. Use Cmd+F to search within current mailbox
3. Use arrow keys to navigate message list
4. Press Enter or Space to open/view message

## Mail Data Safety

**Privacy considerations:**
- Only search mail when explicitly requested
- Do not summarize or extract content from personal emails
- Focus on identifying presence/location of messages, not content
- Respect confidentiality of personal and colleague communications

**Automation limits:**
- Scripts can search and open, but cannot read message content programmatically
- Content extraction requires manual user review
- This is by design for privacy protection

## Integration with Standup Workflow

When processing `/mail-search` command:
1. Execute search with provided terms
2. Present results to user
3. Do not automatically open or extract from found messages
4. Let user decide which emails to review manually

## Troubleshooting

**Mail not responding:**
- Check if Apple Mail is running (launch if needed)
- Allow script access in System Preferences > Security & Privacy > Automation
- Grant Mail access if prompted

**No search results:**
- Verify search terms are correct
- Check if messages are in indexed mailboxes
- Try broader search terms
- Ensure Mail app has completed syncing

**Script errors:**
- Verify script paths are correct
- Check file permissions (chmod +x)
- Review AppleScript syntax if modified

## Best Practices

1. **Search before asking** - Try mail search before requesting clarifying information
2. **Use specific terms** - Project codes, identifiers, dates work better than vague terms
3. **Respect privacy** - Don't extract or summarize email content automatically
4. **Guide, don't automate** - Help users find emails, let them read the content
5. **Check multiple mailboxes** - Senders may email from different accounts
