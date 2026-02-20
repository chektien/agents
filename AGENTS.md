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

- **`/go-boss`** - Start boss orchestration with Plan/Delegate/Validate/Re-delegate cycle (RECOMMENDED). See `opencode/commands/go-boss.md`
- **`/go-altboss`** - Alternative boss with different model. See `opencode/commands/go-altboss.md`

### Standup & Task Management

- **`/standup-create`** - Convert DETAILED-TODO items into standup TODO bullets with issue links. See `opencode/commands/standup-create.md`
- **`/add-todo`** - Create GitHub issues from DETAILED-TODO items and link to standup. See `opencode/commands/add-todo.md`
- **`/add-done`** - Add DONE items to standup.md from recent closed issues/PRs. See `opencode/commands/add-done.md`
- **`/review-loop`** - Fresh-eyes review loop where N workers sequentially review/fix each subtask. See `opencode/commands/review-loop.md`
- **`/standup-update`** - Update standup.md after completing tasks - mark checkboxes and add completion notes. See `opencode/commands/standup-update.md`
- **`/open-standup`** - Open Safari with project boards and recent issue/PR links. See `opencode/commands/open-standup.md`
- **`/post-standup`** - Post standup to Discord (test, production, or dry-run mode). See `opencode/commands/post-standup.md`
- **`/mail-search`** - Search Apple Mail for emails and correspondence. See `opencode/commands/mail-search.md`

For non-opencode agents: refer to the specific command file for workflow details and adjust paths accordingly.

## Multi-Agent Orchestration

The boss orchestration system uses a Plan → Delegate → Validate → Re-delegate cycle with specialized subagents:

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      BOSS (Orchestrator)                     │
│  Plan → Delegate → Validate → Re-delegate (max 10 rounds)   │
└─────────────────────────────────────────────────────────────┘
                              │
           ┌──────────────────┼──────────────────┐
           │                  │                  │
           ▼                  ▼                  ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │   WORKER     │  │   COWORKER   │  │   REVIEWER   │
    │  128k/32k    │  │  400k/128k   │  │ Fresh Context│
    │ Cost-effective│  │ Fallback     │  │ Quality Gate │
    └──────────────┘  └──────────────┘  └──────────────┘
                              │
           ┌──────────────────┼──────────────────┐
           │                  │                  │
           ▼                  ▼                  ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │   THINKER    │  │    VISION    │  │   COVISION   │
    │  Read-only   │  │  128k/64k    │  │  256k/96k    │
    │ Planning     │  │ Visual Tasks │  │ Heavy Visual │
    └──────────────┘  └──────────────┘  └──────────────┘
```

### Execution Flow

1. **Plan**: Boss reads standup.md and creates execution plan (consults @thinker for complex scenarios)
2. **Delegate**: Boss spawns subagents in parallel for independent tasks
   - Code/config → `worker` (128k context, cost-effective)
   - Visual tasks → `vision` (128k context) or `covision` for heavy payloads (256k context)
   - Complex tasks → `coworker` (400k context, fallback)
3. **Validate**: Boss delegates to `@reviewer` for independent fresh-eyes quality check
4. **Re-delegate**: If reviewer returns FAIL, boss re-delegates fixes (max 10 rounds)
5. **Escalate**: On round 3, boss consults `@thinker` for diagnosis; on capacity issues, escalates to `coworker`

### Subagent Context/Output Limits

| Agent | Context | Output | Purpose |
|-------|---------|--------|---------|
| worker | 128k | 32k | Cost-effective task execution |
| coworker | 400k | 128k | Fallback for complex tasks |
| reviewer | 128k | 32k | Independent quality verification |
| vision | 128k | 64k | Visual tasks, screenshots, PDFs |
| covision | 256k | 96k | Heavy visual payloads |
| thinker | varies | varies | Read-only strategic planning |

### Progress Markers

- `[worker HH:MM] STARTED/DONE/FAILED Task X` - Task status
- `[boss HH:MM] REDELEGATING (round N/10)` - Fix attempts
- `[boss HH:MM] THINKER CONSULTED` - Strategy consultation
- `[boss HH:MM] ESCALATED TO COWORKER` - Capacity escalation
- `[coworker HH:MM] DONE Task X` - Fallback execution
- `[boss HH:MM] SESSION COMPLETE` - Session summary

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

## Mail Search and Emails

**For opencode**: Use `/mail-search <term>` command. See `opencode/commands/mail-search.md`

**For all agents**:
- Search using `<MAIL_SCRIPT_PATH>/scripts/mail.sh search "<search-term>" [limit]
- Common search terms: project codes, module names, sender names, identifiers
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
