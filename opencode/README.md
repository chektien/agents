# OpenCode Multi-Agent Configuration

A production-ready multi-agent orchestration system for OpenCode that implements flexible hierarchies for efficient, cost-effective, and high-quality task execution.

## Architecture

### Current System (V3): Superboss-Hardworker

The new superboss/hardworker system provides optimized orchestration for large task queues:

```
┌──────────────────┐
│    Superboss     │  Primary agent (stronger model)
│   (orchestrator) │  Plans, verifies, handles complex work
└────────┬─────────┘
         │
    ┌────┴────┐
    │ DECIDE  │  Evaluate task count + complexity
    └────┬────┘
         │
    ┌────┴──────────────────────────────────────────┐
    │                                               │
    ▼                                               ▼
┌─────────┐                                  ┌───────────┐
│  SOLO   │  Few/complex tasks?              │ DELEGATE  │  Many/routine tasks?
│  MODE   │  1 request total                 │   MODE    │  2 requests total
└─────────┘                                  └─────┬─────┘
                                                   │
                                      Phase 1: Hardworker runs full queue
                                      Phase 2: Superboss picks up remainder
```

### Legacy System (V2): Boss-Worker-Coworker

The original three-tier hierarchy is maintained for backward compatibility:

```
┌─────────────────────────────────────────────────────────┐
│  BOSS - Orchestrator                                    │
│  • Plans and delegates work                             │
│  • Monitors progress                                    │
│  • Verifies quality using stronger model                │
│  • Handles recovery and escalation                      │
└─────────────────────────────────────────────────────────┘
                           │
            ┌──────────────┴──────────────┐
            │                             │
            ▼                             ▼
┌──────────────────────┐      ┌──────────────────────┐
│ WORKER               │      │ COWORKER             │
│ • Cost-effective     │      │ • Fallback agent     │
│ • Fast execution     │      │ • Complex tasks      │
│ • Step limit: 50     │      │ • Step limit: 50     │
│ • Does bulk of work  │      │                      │
└──────────────────────┘      └──────────────────────┘
```

## Why These Architectures?

**Superboss-Hardworker (V3 - Recommended):**
1. **Cost Optimization**: Single worker pass completes most routine tasks
2. **Quality Assurance**: Stronger superboss verifies and handles complex work
3. **Context Efficiency**: Minimal back-and-forth; superboss finishes remaining work
4. **Scalability**: Handles large task queues in two phases maximum

**Boss-Worker-Coworker (V2 - Legacy):**
1. **Cost Optimization**: Use cheaper models for 80% of work
2. **Quality Assurance**: Use stronger models for verification and complex tasks
3. **Fault Tolerance**: Automatic fallback when workers fail
4. **Progress Tracking**: Shared standup.md with timestamped coordination markers

## Installation

1. Copy the configuration files to your OpenCode config directory:
   ```bash
   cp -r opencode/* ~/.config/opencode/
   ```

2. Update placeholders in `opencode.json`:
   - Replace `<YOUR_WORKING_DIR>` with your actual working directory
   - Configure your preferred providers and API keys
   - Set up MCP servers as needed

3. Create a `standup.md` file in your preferred location

4. Update commands to point to your standup file location

## Usage

### Orchestration Commands

```bash
# NEW (V3 - Recommended): Start superboss orchestrator with solo/delegate modes
/go-superboss

# LEGACY (V2): Start boss-worker-coworker orchestrator
/go-boss
```

### Standup Commands

```bash
# Create GitHub issues from DETAILED-TODO items (cross-references existing issues)
/add-todo today    # Process today's TODO items
/add-todo tomorrow # Process tomorrow's TODO items

# Add completed work from GitHub issues to standup
/add-done today    # Add today's completed items
/add-done tomorrow # Add tomorrow's completed items
```

### Starting the System (V3 Superboss)

The superboss will:
1. Read your standup.md DETAILED-TODO section
2. Evaluate task count and complexity
3. **Solo mode**: Execute all tasks yourself (fit in context, or mostly complex)
4. **Delegate mode**: Spawn @hardworker for routine tasks, then finish remaining work yourself
5. Maximum 2 phases — no respawning

### Starting the System (V2 Boss - Legacy)

The boss will:
1. Read your standup.md DETAILED-TODO section
2. Create an execution plan
3. Delegate tasks to the worker
4. Monitor and verify results
5. Escalate to coworker when needed

### Coordination Markers (V3)

Superboss and Hardworker use timestamped markers in standup.md:

```markdown
- [ ] task description <https://github.com/org/repo/issues/1>
  - [superboss HH:MM] PLAN: Solo/Delegate decision and strategy
  - [hardworker HH:MM] STARTED Task X: brief description
  - [hardworker HH:MM] DONE Task X: brief summary
  - [superboss HH:MM] FIXING: Task X [reason]
  - [superboss HH:MM] PICKING UP Task X: continuing work
  - [superboss HH:MM] SESSION COMPLETE: summary
```

### Markers Reference (V3)

**Superboss markers:**
- `[superboss HH:MM] PLAN: Solo/Delegate` - Decision made
- `[superboss HH:MM] FIXING: Task X` - Fixing failed/incomplete work
- `[superboss HH:MM] PICKING UP Task X` - Completing remaining work
- `[superboss HH:MM] SESSION COMPLETE` - Session finished with summary

**Hardworker markers:**
- `[hardworker HH:MM] STARTED Task X` - Task in progress
- `[hardworker HH:MM] DONE Task X` - Task completed
- `[hardworker HH:MM] FAILED Task X` - Task failed, continuing
- `[hardworker HH:MM] CONTEXT: ~XX%` - Context usage report
- `[hardworker HH:MM] SUMMARY` - Final summary before exit

### Markers Reference (V2 Legacy)

- `[worker HH:MM] IN PROGRESS: description` - Worker starts task
- `[worker HH:MM] DONE: summary, commit hash` - Worker completes
- `[worker HH:MM] FAILED: obstacle description` - Worker reports failure
- `[boss HH:MM] DECISION: switching to @coworker` - Boss escalates
- `[boss HH:MM] REVIEW: issues found` - Boss rejects, needs fixes
- `[boss HH:MM] VERIFIED: confirmation` - Boss accepts work
- `[coworker HH:MM] IN PROGRESS: description` - Coworker handling complex task

## Configuration Files

### Agents (`opencode.json`)

**V3 (Current):**
- **superboss**: Primary orchestrator (stronger model)
- **hardworker**: Serial task executor (worker model, hidden)

**V2 (Legacy):**
- **boss**: Primary orchestrator
- **worker**: Cost-effective executor (hidden)
- **coworker**: Fallback for complex tasks (hidden)

### Prompts (`prompts/`)

Detailed system prompts for each agent:

**V3:**
- `superboss.md`: Orchestration, solo/delegate decision, quality verification
- `hardworker.md`: Serial task execution, self-verification

**V2:**
- `boss.md`: Orchestration and quality verification logic
- `worker.md`: Task execution and self-verification
- `coworker.md`: Complex problem handling

### Commands (`commands/`)

**Orchestration (V3):**
- `go-superboss`: Start superboss orchestration (solo or delegate mode) - RECOMMENDED

**Orchestration (V2):**
- `go-boss`: Start boss-worker-coworker orchestration

**Standup Management:**
- `add-todo`: Create GitHub issues from DETAILED-TODO (today/tomorrow)
- `add-done`: Add completed items from GitHub issues (today/tomorrow)

**Standup Management:**
- `standup-create`: Convert DETAILED-TODO to standup format
- `standup-update`: Mark tasks complete with notes
- `open-standup`: Open project boards in browser
- `post-standup`: Post to Discord (test/prod/dry-run)

**Mail & Communication:**
- `mail-search`: Search Apple Mail for correspondence

### Skills (`skills/`)

Reusable instruction modules:
- `git-checkpoint`: Best practices for frequent commits
- `mac-mail`: Apple Mail automation and search guidelines
- `standup-coordination`: Protocol for progress tracking
- `task-execution`: Autonomous execution guidelines

## Quality Verification

Both orchestration systems perform targeted verification leveraging model strength differences:

**For Code:**
- Syntax errors
- Missing imports
- Test failures
- Logic bugs
- Pattern consistency

**For LaTeX (High Priority):**
- DOI verification for paper citations
- Valid web link checking
- No hallucinated references (1st/2nd order)
- Slide margin overflow detection
- Figure reference validation
- No tabs in verbatim environments

**V3 Superboss Quality Checks:**
- Superboss verifies ALL tasks hardworker marked DONE before proceeding
- Uses per-task git commits to review partial work
- Fixes verification failures immediately before moving to next task

**V2 Boss Quality Checks:**
- Boss performs quality verification after worker completion
- Rejects work and retries if issues found
- Escalates to coworker if worker fails multiple times

## Customization

### Adding Your Own Agents

1. Create a new agent in `opencode.json`:
   ```json
   "my-agent": {
     "mode": "subagent",
     "hidden": true,
     "model": "<your-model>",
     "steps": 50,
     "prompt": "{file:./prompts/my-agent.md}"
   }
   ```

2. Add the prompt file in `prompts/my-agent.md`

3. Update boss permissions to allow invoking your agent:
   ```json
   "permission": {
     "task": {
       "my-agent": "allow"
     }
   }
   ```

### Modifying Verification Rules

Edit `prompts/boss.md` to add or remove verification criteria based on your project's needs.

## Best Practices

**All Orchestration Systems:**
1. **Standup Format**: Keep DETAILED-TODO organized with issue links
2. **Git Commits**: Workers commit after every task (prevents work loss)
3. **Recovery**: When agents crash, progress is preserved in git + standup.md
4. **No Clarifying Questions**: Agents use best judgment and proceed
5. **User Updates**: Agents check for user changes to standup.md at every read/write point

**V3 Specific:**
- **Task Batching**: Superboss provides full task specs to hardworker upfront
- **No Monitoring**: Superboss cannot intervene while hardworker runs
- **Blocking Pattern**: Two phases maximum — hardworker runs once, superboss finishes rest

**V2 Specific:**
- **Task Batching**: Boss assigns logical groups of related tasks
- **Real-time Coordination**: Boss monitors and can intervene during worker execution
- **Escalation**: Boss automatically escalates to coworker on repeated failures

## Troubleshooting

### Worker Keeps Failing

The boss will automatically escalate to coworker after 2-3 failures. If coworker also fails, the task is added to a HARD TASKS section for manual review.

### Progress Not Tracking

Ensure all agents can write to your standup.md location. Check permissions in `opencode.json`.

### Steps Limit Reached

Workers have a 50-step limit to prevent runaway costs. The boss will respawn them to continue remaining tasks.

## License

MIT License - See repository root for details.
