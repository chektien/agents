---
name: git-checkpoint
description: Best practices for frequent git commits to enable recovery when agents crash
---

# Git Checkpoint Protocol

## When to Commit

**FREQUENCY: After every meaningful change**
- After completing each task
- Before switching between repos
- After fixing a bug
- Before reporting back to boss
- Every 10-15 minutes during long tasks

## Commit Message Format

Use structured commit messages with brief subject and detailed body:

```bash
git commit -m "brief summary of changes

Detailed description of all amendments:
- Specific change 1 and why
- Specific change 2 and impact
- Edge cases handled
- Any breaking changes or notes"
```

**Format rules:**
- First line: Brief summary (50 chars or less), imperative mood ("Add" not "Added")
- Blank line after summary
- Body: Detailed bullet list of all changes, unfolds when viewing git log
- Each bullet should explain what changed and why

**Examples:**

```bash
git commit -m "create HMD slide in week06.tex

- Add new slide covering HMD hardware components
- Shift existing images to images/ folder
- Update week06.tex with new section reference
- Add proper figure captions for accessibility"
```

```bash
git commit -m "fix past members alignment in CSS

- Change flexbox direction from row-reverse to row
- Remove !important from margin-right override
- Add media query for mobile responsiveness
- Verified in Chrome DevTools at 320px-1920px widths"
```

```bash
git commit -m "update opencode agent configuration

- Add new boss-worker-coworker orchestration agents
- Configure worker model for cost-effective task execution
- Set up stronger model for quality verification fallback
- Add 50-step limits to prevent runaway costs"
```

## Checkpoint Commands

### Quick Checkpoint
```bash
git add .
git commit -m "WIP: task name

- Current progress on feature
- Known issues or blockers
- Next steps needed"
```

### Before Task Switch
```bash
git add .
git commit -m "completed: task name

- Summary of all changes made
- Files modified and why
- Testing performed"
git push  # if needed
```

### Recovery Check
If you need to verify last commit:
```bash
git log --oneline -5
```

This shows last 5 commits to verify your checkpoint worked.

## Recovery Protocol

When an agent crashes, the boss agent:
1. Checks `git log` to see last commit
2. Reads standup.md for last progress marker
3. Compares to see what was saved vs lost
4. Respawns worker with: "Resume from [last completed task]"

## Critical Rule

**NEVER work for more than 15 minutes without committing.**

If you crash after 2 hours of work with no commits, everything is lost. Frequent commits ensure minimal loss on crashes.
