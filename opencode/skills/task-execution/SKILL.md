---
name: task-execution
description: Best practices for autonomous task completion without asking clarifying questions
---

# Autonomous Task Execution

## Core Principle

**NEVER ask clarifying questions.** Use your best judgment and proceed.

## Decision Framework

When encountering ambiguity:

1. **Check existing patterns** - Look at how similar things are done in the codebase
2. **Check AGENTS.md** - Each repo has AGENTS.md with guidance
3. **Choose reasonable default** - Pick the most common/standard approach
4. **Document your choice** - Note in standup.md why you chose X over Y

## Common Scenarios

### Ambiguous Requirements
- Example: "Fix the styling" without specifics
- Action: Look at existing CSS, match the pattern used elsewhere
- Document: [worker 10:15] IN PROGRESS: fixing styling - using existing flexbox pattern

### Missing Information
- Example: "Add a new component" without design specs
- Action: Create minimal functional version with standard structure
- Document: [worker 10:15] IN PROGRESS: creating Button component - using standard React structure

### Unexpected Errors
- Example: Command fails with cryptic error
- Action: Try alternative approach, search for similar issues in codebase
- If stuck after 2-3 tries: Report failure to boss

### Unclear Scope
- Example: Task description is vague
- Action: Implement what seems reasonable, document what you did
- Better to do something than ask what to do

## Best Practices

1. **Start with reading** - Always read the file before modifying
2. **Match existing patterns** - Consistency over perfection
3. **Minimal changes** - Change only what's needed
4. **Test if possible** - Run tests or verify manually
5. **Commit frequently** - After each logical change
6. **Be decisive** - Spending 5 minutes deciding is worse than picking either option

## When to Report Failure

Only report failure to boss when:
- You've tried 2-3 reasonable approaches and all failed
- The task requires information that truly doesn't exist (API keys, external decisions)
- You've reached your step limit and must checkpoint
- The codebase is in an unrecoverable state

Everything else: proceed with best judgment.
