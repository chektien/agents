---
description: "Boss plans normally, but each subtask gets N fresh-eyes iterations instead of worker->reviewer"
argument-hint: "ITERATIONS TASK_DESCRIPTION"
agent: boss
---

# Review Loop

You are running a **fresh-eyes review loop**. This replaces your normal worker -> reviewer validation cycle. Instead of a reviewer returning PASS/FAIL, each subtask gets N total worker passes where fresh workers re-evaluate and fix the work.

## Parameters

Parse the first word of `$ARGUMENTS` as the iteration count N (default: 3 if not a number). Everything after is the task description.

```
$ARGUMENTS
```

## Protocol

### Phase 1: Plan

Plan and decompose the task exactly as you normally would. Read relevant files, identify subtasks, define acceptance criteria, categorize by subagent type. This is your normal planning phase.

### Phase 2: Delegate with Review Loop

For each subtask, instead of your normal worker -> reviewer cycle, run a **review loop of N workers sequentially**:

```
for each subtask:
  iteration 1: spawn worker with full subtask spec (initial build)
  wait for completion

  for iteration 2..N:
    spawn a FRESH worker with:
      - the original subtask spec and acceptance criteria
      - instruction to review and fix (see template below)
    wait for completion
```

**Subtasks that are independent of each other can have their review loops run in parallel.** Only the iterations within a single subtask must be sequential.

#### Fresh-Eyes Worker Template (iterations 2..N)

```
You are iteration [X] of [N] reviewing a subtask.

## Original Task
[full subtask description and acceptance criteria]

## Your Job
You are a FRESH SET OF EYES on this subtask. You have never seen this code before.

1. Read all relevant files from scratch
2. Run tests / check the build / preview the UI (whatever applies)
3. Look for: bugs, logic errors, missing edge cases, UI quality issues, incomplete work
4. FIX everything you find -- do not just report issues
5. If the work looks good, make quality improvements
6. Git commit your changes

Focus on what's actually wrong, not style preferences.
Do NOT update standup.md -- this is a review-loop session.
```

### Phase 3: Summary

After all subtasks and their iterations complete, print:

```
REVIEW LOOP COMPLETE -- [S] subtasks x [N] iterations

Subtask 1: [description]
  Iteration 1 (build): [summary]
  Iteration 2 (fresh eyes): [what was found/fixed]
  ...

Subtask 2: [description]
  ...

Files changed: [key files]
```

## Rules

- Plan and decompose tasks as you normally would -- the review loop only changes the validation phase
- Do NOT use the `reviewer` subagent -- fresh workers both review AND fix
- Do NOT skip iterations even if a worker says "everything looks good"
- Iterations within a subtask are sequential -- each worker must see the previous one's commits
- Independent subtasks can run their review loops in parallel
- Each fresh worker gets the FULL original subtask description so they know the intent
- Do NOT update standup.md -- this is a review-loop session
- Visual tasks still go to `vision` for the initial pass, but fresh-eyes iterations use `worker` (they read the files the vision agent produced)
