---
name: standup-coordination
description: Protocol for reading and writing progress markers in standup.md for multi-agent coordination
---

# Standup.md Coordination Protocol

## Reading standup.md

Always read standup.md at your configured standup location (e.g., `~/standup.md` or your preferred path).

Look for:
1. **DETAILED-TODO section** - Current tasks to work on
2. **Progress markers** - Previous agent work (look for [worker HH:MM], [coworker HH:MM], [boss HH:MM])
3. **Last completed task** - Where to resume work
4. **HARD TASKS section** - Tasks that need escalation

## Writing Progress Markers

When updating standup.md, use these exact formats:

### In Progress
```
- [ ] task description <https://github.com/...>
  - [worker 10:15] IN PROGRESS: what you're doing
```

### Done
```
- [x] task description <https://github.com/...>
  - [worker 10:23] DONE: brief completion summary, committed abc123
```

### Failed
```
- [ ] task description <https://github.com/...>
  - [worker 10:45] FAILED: specific obstacle encountered, attempting retry
```

### Boss Switch Decision
```
- [ ] task description <https://github.com/...>
  - [worker 10:45] FAILED: [obstacle description]
  - [boss 10:46] DECISION: switching to @coworker for [reason]
  - [coworker 10:47] IN PROGRESS: handling complex issue
```

## Coordination Rules

1. **Always prefix with [agent HH:MM]** - Prevents conflicts between agents
2. **Update after EACH task** - Not at the end, immediately after completion
3. **Include commit hash** - Helps track what was saved
4. **Be specific** - "Fixed CSS flexbox" not "Fixed issue"
5. **Check for new instructions** - Read standup.md again if working for >10 minutes

## Finding Next Task

Look for unchecked items `[ ]` without recent progress markers. If all items in a section have markers, check if they're complete or need retry.

## Hard Tasks Section

If a task fails multiple times with both worker and coworker, add it to:

```markdown
## HARD TASKS (Require user attention)

- [ ] [failed task description]
  - [worker 11:00] ATTEMPT 1: FAILED - reason
  - [coworker 11:30] ATTEMPT 2: FAILED - reason
  - STATUS: Needs user decision on approach
```
