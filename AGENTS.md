# Instructions for AI Coding Agents

This file provides guidance for opencode, claude-code, kimi code, and similar AI coding assistants.

## General Principles

- **Default Context**: When prompts are talking about DETAILED-TODOs or is ambiguous, check `standup.md` in this directory first
- **File Locations**: Standup file is `standup.md`; repos/projects are under `<YOUR_WORKING_DIR>/` (e.g., `~/repos/`, `~/projects/`, etc. - customize to your setup)
- **No Emojis**: Avoid emojis in all outputs unless explicitly requested

## Standup Format

- Match the format of existing entries when adding new items
- Every DONE/TODO bullet **MUST** include an issue URL in angle brackets (e.g., `<https://github.com/...>`)
- **BREVITY**: Use high-level descriptions only (e.g., "cosmetic updates for RN client" not "renamed app, removed logo, added icons")
- **NO COMMIT/PUSH**: Omit routine git operations from standup items

### Standup Archiving

- standup.md should only contain the last 7 days of standups for easier access
- Entries older than 7 days should be moved to standup-archive.md
- When archiving: cut old entries from standup.md and prepend to standup-archive.md (after its header)
- The archive header explains its purpose; preserve it when updating

### Converting DETAILED-TODOs to Standup TODOs

- Do not create standup items or issues with sections or tasks starting with NOPOST
- When asked to "create standup for today", each subsection in the header DETAILED-TODO should be summarized into a single short bullet point phrase with an issue link, unless there are no tasks for that subsection
- Search past standup days for the most relevant gh link first, then pull existing issues to check through gh api
- If no relevant one exists, create a new gh issue for it under the relevant repo
- **CONSOLIDATION RULE**: When multiple TODO items reference the same issue number, consolidate them into a single bullet point that summarizes all related tasks, linking to that issue only once
- **SYNTHESIS**: Do not list individual tasks; instead, synthesize them into one coherent, high-level description that captures the overall objective
- Ask for review before posting the standup

### Performing tasks in DETAILED-TODO

- When asked to "do everything here", or follow-up on reviewed tasks, or perform the DETAILED-TODOs in general, go to the relevant repo folder, check their AGENTS.md first
- DO NOT skip the NOPOST sections, just do not create standup or issues for them
- When completing tasks, update the relevant gh issue by checking off any relevant checkboxes, and add a concise comment
- **AUTONOMOUS EXECUTION**: Work through ALL tasks across ALL sections continuously without stopping to ask for permission. Only pause at the very end to present a consolidated summary.
- Check off completed tasks in the DETAILED-TODO, and commit the updates in the respective gh repos
- As you complete each task, summarize what is done under each checkbox in the DETAILED-TODO
- **REAL-TIME UPDATES**: As you complete tasks, when retrieving standup.md to update, if there are new tasks added after reviews, go and do them as well without stopping

### Adding DONE items

- When asked to add DONE items to standup.md, check the relevant gh issues first for recent closed issues or PRs that match the tasks completed today

## Available Commands (opencode)

The following opencode commands automate standup and email workflows. Commands reference placeholders for script and notes paths - customize these to your setup:

### Orchestration Commands

- **`/go-superboss`** - Start superboss orchestration with solo/delegate modes to execute DETAILED-TODO (RECOMMENDED). See `opencode/commands/go-superboss.md`
- **`/go-boss`** - Start boss-worker-coworker orchestration to execute DETAILED-TODO (legacy). See `opencode/commands/go-boss.md`

### Standup & Task Management

- **`/standup-create`** - Convert DETAILED-TODO items into standup TODO bullets with issue links. See `opencode/commands/standup-create.md`
- **`/add-todo`** - Create GitHub issues from DETAILED-TODO items and link to standup. See `opencode/commands/add-todo.md`
- **`/add-done`** - Add DONE items to standup.md from recent closed issues/PRs. See `opencode/commands/add-done.md`
- **`/review-loop`** - Fresh-eyes review loop where N workers sequentially review/fix each subtask. See `opencode/commands/review-loop.md`
- **`/standup-update`** - Update standup.md after completing tasks - mark checkboxes and add completion notes. See `opencode/commands/standup-update.md`
- **`/open-standup`** - Open Safari with project boards and recent issue/PR links. See `opencode/commands/open-standup.md`
- **`/post-standup`** - Post standup to Discord (test, production, or dry-run mode). See `opencode/commands/post-standup.md`
- **`/mail-search`** - Search Apple Mail for student emails and module correspondence. See `opencode/commands/mail-search.md`

For non-opencode agents: refer to the specific command file for workflow details and adjust paths accordingly.

## Multi-Agent Orchestration Systems

### Current System (V3): Superboss-Hardworker

The superboss/hardworker system provides optimized orchestration for large task queues:

**Architecture Decision:**
- **Solo Mode**: All tasks fit in context, or most are complex → superboss executes everything (1 request)
- **Delegate Mode**: Too many tasks or mostly routine → superboss spawns hardworker once (Phase 1), then finishes remainder (Phase 2) (2 requests)

**Key Design Constraints:**
1. **Synchronous/Blocking**: When superboss spawns hardworker, superboss blocks and cannot intervene
2. **Two Phases Maximum**: Hardworker runs once with full task queue; superboss finishes remaining work
3. **No Real-time Coordination**: Standup.md is a record for review, not a communication channel during execution
4. **Per-task Git Commits**: Hardworker commits after every task (superboss uses this to review and fix issues)
5. **Cost Optimization**: Maximum 2 GitHub Copilot requests per session

**Execution Flow:**
1. Superboss reads standup.md and evaluates task count + complexity
2. Superboss decides: solo mode or delegate mode
3. **Delegate mode only:**
   - Superboss provides full task queue and context to hardworker
   - Hardworker executes serially, writes progress to standup.md, commits per task
   - Superboss is BLOCKED (cannot monitor)
   - Hardworker marks FAILED tasks and continues (doesn't stop)
4. **Phase 2 (delegate only) or immediately (solo):**
   - Superboss reads standup.md for hardworker results AND any user updates
   - Superboss verifies all DONE tasks (syntax, logic, references, etc.)
   - Superboss fixes any verification failures immediately
   - Superboss picks up any FAILED tasks or remaining work
   - No more spawning — superboss finishes everything

**Progress Markers:**
- `[superboss HH:MM] PLAN: Solo/Delegate` - Decision made
- `[hardworker HH:MM] STARTED/DONE/FAILED Task X` - Task status
- `[superboss HH:MM] FIXING/PICKING UP Task X` - Remedial work
- `[superboss HH:MM] SESSION COMPLETE` - Summary

### Legacy System (V2): Boss-Worker-Coworker

The original three-tier system for backward compatibility. Now updated (V3) with Plan, Delegate, Validate, Re-delegate cycle using reviewer/thinker/vision subagents:

**Architecture:**
- **Boss** (stronger model): Orchestrator, reads standup, plans, verifies quality
- **Worker** (cheaper model): Executes most tasks cost-effectively
- **Coworker** (fallback model): Handles complex tasks when worker fails
- **Reviewer** (subagent): Independent quality verification with fresh context
- **Thinker** (subagent): Strategic planning and failure diagnosis (read-only)
- **Vision** (subagent): Visual tasks, screenshots, PDF analysis

**Execution Flow:**
1. Boss reads standup.md and creates detailed execution plan (consults @thinker for complex planning)
2. Boss delegates to worker for each task batch
3. Boss validates results via @reviewer (fresh-eyes quality check)
4. If reviewer finds issues, boss re-delegates fixes (max 10 rounds per task)
5. If worker fails after retries, boss escalates to coworker
6. Boss uses @vision for any visual verification needs
7. Boss verifies all work before marking complete

**Key Differences from V3 Superboss:**
- Boss can monitor and intervene during worker execution (not blocking)
- Escalation chain: worker → coworker → boss
- Automatic respawning of workers for remaining tasks after failure
- Re-delegation loop with @reviewer validation (max 10 rounds)
- @thinker consultation for planning and failure diagnosis
- @vision for visual tasks and verification

## Path Placeholders

Commands use the following path placeholders that you should replace:
- `<MAIL_SCRIPT_PATH>` - Path to your dotfiles/scripts directory containing mail.sh (e.g., `~/repos/dotfiles`)
- `<SCRIPTS_PATH>` - Path to your scripts directory (e.g., `~/repos/dotfiles/scripts`)
- `<NOTES_PATH>` - Path to your notes directory (e.g., `~/Library/Mobile Documents/com~apple~CloudDocs/notes`)

## Issue Linking

- Whenever asked to link an issue, use gh api to find the right one given the prompt.

## Issue Creation

- For new issues, assign to the correct person based on the task using gh issue edit --add-assignee <login>
- Add to the relevant GitHub Project by first fetching projects with gh project list --owner <org>, then using gh issue edit --add-project "<project title>"

## Discussion Posts & Comments

- When posting to GitHub Discussions or commenting on issues, keep posts concise, friendly, and professional
- Focus on clarity and actionable information
- Avoid unnecessary elaboration or emotions unless specifically requested
- DO NOT use emojis or fancy icons

## Mail Search and Student Emails

**For opencode**: Use `/mail-search <term>` command. See `opencode/commands/mail-search.md`

**For all agents**:
- Search using `<MAIL_SCRIPT_PATH>/scripts/mail.sh search "<search-term>" [limit]
- Common search terms: course codes, module names, student names, student IDs
- Open specific emails: `<MAIL_SCRIPT_PATH>/scripts/mail.sh open <number>`
- Read programmatically: `find ~/Library/Mail -name "*.emlx" -type f -mtime -7 | xargs grep -l "<term>" | head -N | while read f; do cat "$f" | head -150; done`
- Draft replies only - **NEVER send automatically**. User must review and send via Mail.app.
- Related resources:
  - Mail scripts: `<MAIL_SCRIPT_PATH>/scripts/mail*.sh`
  - Workflow docs: `<NOTES_PATH>/MAIL-WORKFLOW.md`

## Chrome DevTools Usage

- **RESIZE BEFORE SCREENSHOTS**: Before taking screenshots, resize the page to a smaller viewport (e.g., 800x600 or 1024x768) to reduce image size and prevent context overflow
- **PREFER SNAPSHOTS**: Use `take_snapshot` over `take_screenshot` when possible - text snapshots are much smaller than images
- **AVOID FULL PAGE SCREENSHOTS**: Never use `fullPage: true` for screenshots; capture only the visible viewport or specific elements
- **BATCH OPERATIONS**: When filling forms or clicking through multi-step UIs, batch operations and minimize intermediate screenshots
- **USE ELEMENT TARGETING**: When only a specific element needs verification, use the `uid` parameter to screenshot just that element instead of the whole page

## Visual Tasks & Image Analysis

### Using the @vision Subagent

For all visual tasks, **prefer using the `@vision` subagent** before attempting with the main agent. The vision subagent is specialized for:

- **Reading PDFs with embedded images** - Extract and analyze content from PDFs containing diagrams, charts, or screenshots
- **Image analysis** - Describe, interpret, or analyze any images, screenshots, or diagrams
- **Image generation** - Generate images based on descriptions (if supported by the model)
- **Visual debugging** - Analyze UI screenshots, error messages in images, visual glitches
- **Diagram understanding** - Interpret flowcharts, architecture diagrams, wireframes
- **OCR tasks** - Extract text from images or scanned documents

**Invocation:**
```
@vision analyze this screenshot and tell me what's wrong with the UI
@vision read this PDF and extract the text from the diagrams
@vision describe the image at /path/to/image.png
```

### Fallback Strategy

If the `@vision` subagent fails or is unavailable:

1. **Retry with the main agent** - The primary agent (build/plan) can also handle visual tasks directly
2. **Simplify the request** - Break down complex visual analysis into smaller, clearer prompts
3. **Use alternative tools** - For PDFs, use command-line tools (`pdftotext`, `pdfimages`) to extract content first
4. **Provide context** - When asking the main agent, include as much context as possible about what to look for in the image

**Note:** Always try `@vision` first for visual tasks as it's optimized for these operations, but don't hesitate to fall back to the main agent if needed.
