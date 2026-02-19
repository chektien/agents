# Agents

Configuration files and instructions for AI coding assistants like opencode, claude-code, and similar tools.

**⚠️ SECURITY NOTICE**: This repository contains example configuration files with placeholder API keys. **Never commit real API keys to git.** Use environment variables (`{env:VAR_NAME}`) or the `/connect` command in opencode instead. See [Security Warning](#security-warning) below.

## Contents

- `AGENTS.md` - General instructions for AI coding agents to follow when working on tasks
- `opencode/` - Configuration for [opencode](https://opencode.ai/) TUI
  - `opencode.json` - Main configuration file with providers, MCP servers, permissions, and agents
  - `commands/` - Slash commands for common workflows (standup, mail, orchestration, review)
  - `prompts/` - Agent system prompts (superboss, hardworker, boss, worker, coworker, reviewer, thinker, vision, build)
  - `skills/` - Reusable instruction modules for common tasks (git-checkpoint, mac-mail, standup-coordination, task-execution, frontend-design)

## Installation

### Quick Setup

1. **Clone this repo** to a location of your choice (replace `<REPO_CLONE_PATH>` with your preferred directory):
   ```bash
   git clone <repo-url> <REPO_CLONE_PATH>/agents
   # Example: git clone <repo-url> ~/repos/agents
   ```

2. **Install opencode config files** to the proper location (`~/.config/opencode/`):
   
   **Option A: Copy files (simplest)**
   ```bash
   # Create the opencode config directory if it doesn't exist
   mkdir -p ~/.config/opencode/commands
   
    # Copy configuration files from this repo (replace <REPO_CLONE_PATH> with your actual path)
    cp <REPO_CLONE_PATH>/agents/opencode/opencode.json ~/.config/opencode/
    cp <REPO_CLONE_PATH>/agents/opencode/commands/*.md ~/.config/opencode/commands/
    
    # Copy prompts and skills
    mkdir -p ~/.config/opencode/prompts
    cp <REPO_CLONE_PATH>/agents/opencode/prompts/*.md ~/.config/opencode/prompts/
    
    # Copy skills (preserving directory structure)
    cp -r <REPO_CLONE_PATH>/agents/opencode/skills/* ~/.config/opencode/skills/
   
   # Example if cloned to ~/repos/:
   # cp ~/repos/agents/opencode/opencode.json ~/.config/opencode/
   ```
   
   **Option B: Create symlinks (keeps files in sync with repo)**
   ```bash
   # Create the config directory structure
   mkdir -p ~/.config/opencode/commands
   
    # Create symlinks (replace <REPO_CLONE_PATH> with your actual path)
    ln -sf <REPO_CLONE_PATH>/agents/opencode/opencode.json ~/.config/opencode/opencode.json
    
    # Commands
    for f in <REPO_CLONE_PATH>/agents/opencode/commands/*.md; do
        ln -sf "$f" ~/.config/opencode/commands/
    done
    
    # Prompts
    mkdir -p ~/.config/opencode/prompts
    for f in <REPO_CLONE_PATH>/agents/opencode/prompts/*.md; do
        ln -sf "$f" ~/.config/opencode/prompts/
    done
    
    # Skills
    mkdir -p ~/.config/opencode/skills
    for d in <REPO_CLONE_PATH>/agents/opencode/skills/*/; do
        skill_name=$(basename "$d")
        ln -sf "$d" ~/.config/opencode/skills/"$skill_name"
    done
   
   # Example if cloned to ~/repos/:
   # ln -sf ~/repos/agents/opencode/opencode.json ~/.config/opencode/opencode.json
   ```

3. **Configure placeholder values** in `~/.config/opencode/opencode.json`:
   - `<YOUR_PREFERRED_TASK_MODEL>` - Your preferred model for task execution
   - API keys - See [Security Warning](#security-warning) below

### Security Warning

**DO NOT paste API keys directly into `opencode.json` if this file is in a git repository or shared location.**

Instead, use one of these secure methods:

**Option 1: Environment Variables (Recommended)**
```json
{
  "provider": {
    "google": {
      "options": { "apiKey": "{env:GOOGLE_GEMINI_API_KEY}" },
      "models": { "gemini-2.5-flash": { "name": "Gemini 2.5 Flash" } }
    }
  },
  "mcp": {
    "brave-search": {
      "environment": { "BRAVE_API_KEY": "{env:BRAVE_API_KEY}" }
    }
  }
}
```

Then set environment variables in your shell profile:
```bash
export GOOGLE_GEMINI_API_KEY="your-key-here"
export BRAVE_API_KEY="your-key-here"
```

**Option 2: Separate, Non-Tracked Config File**
Create a local config at `~/.config/opencode/opencode.local.json` that is NOT in git, then reference it from the main config or use the `/config` command in opencode to switch to it.

**Option 3: Use the `/connect` Command**
In the opencode TUI, use the `/connect` command to authenticate with providers interactively without storing keys in config files.

4. **Update paths in slash commands**:
   - Replace `<YOUR_STANDUP_FILE_PATH>` in command files with your actual standup file path
   - Example: `/Users/username/Library/Mobile Documents/com~apple~CloudDocs/notes/standup.md`

5. **Place AGENTS.md** in your working directory or notes folder where AI assistants can reference it (replace `<REPO_CLONE_PATH>`):
   ```bash
   cp <REPO_CLONE_PATH>/agents/AGENTS.md ~/Library/Mobile\ Documents/com~apple~CloudDocs/notes/
   # or
   cp <REPO_CLONE_PATH>/agents/AGENTS.md ~/notes/
   
   # Example:
   # cp ~/repos/agents/AGENTS.md ~/Library/Mobile\ Documents/com~apple~CloudDocs/notes/
   ```

### File Locations

| File in Repo | Installs To |
|--------------|-------------|
| `opencode/opencode.json` | `~/.config/opencode/opencode.json` |
| `opencode/commands/*.md` | `~/.config/opencode/commands/` |
| `opencode/prompts/*.md` | `~/.config/opencode/prompts/` |
| `opencode/skills/*/` | `~/.config/opencode/skills/` |
| `AGENTS.md` | Your notes/working directory (e.g., `~/notes/` or `~/Library/Mobile Documents/com~apple~CloudDocs/notes/`) |

## Agents

| Agent | Mode | Model | Purpose |
|-------|------|-------|---------|
| `superboss` | primary | `<YOUR_STRONGER_MODEL>` | Task orchestration (solo or delegate), quality verification |
| `hardworker` | subagent (hidden) | `<YOUR_WORKER_MODEL>` | Serial task execution (50 steps), per-task git commits |
| `boss` | primary | `<YOUR_STRONGER_MODEL>` | Task orchestration with Plan/Delegate/Validate/Re-delegate cycle |
| `worker` | subagent (hidden) | `<YOUR_WORKER_MODEL>` | Cost-effective task execution (50 steps) |
| `coworker` | subagent (hidden) | `<YOUR_FALLBACK_MODEL>` | Fallback for complex tasks (50 steps) |
| `reviewer` | subagent (hidden) | `<YOUR_REVIEW_MODEL>` | Independent quality verification with fresh context |
| `thinker` | subagent (hidden) | `<YOUR_THINKING_MODEL>` | Read-only strategic planning and failure diagnosis |
| `vision` | subagent (hidden) | `<YOUR_VISION_MODEL>` | Visual tasks, screenshots, PDF analysis |
| `build` | primary | `<YOUR_STRONGER_MODEL>` (max) | Code implementation and writing |
| `simple` | primary | `<YOUR_WORKER_MODEL>` | Simple tasks with minimal overhead |
| `plan` | primary | `<YOUR_STRONGER_MODEL>` (max) | Planning and architecture |
| `task` | primary | `<YOUR_PREFERRED_TASK_MODEL>` | Task execution |

### Using the Multi-Agent Orchestration Systems

#### Superboss-Hardworker (V3 - Recommended)

The new superboss system provides optimized orchestration with solo/delegate modes:

```bash
# Start superboss to work through DETAILED-TODO (RECOMMENDED)
/go-superboss
```

**How it works:**
1. **Superboss** (stronger model) reads standup.md and analyzes all tasks
2. **Superboss evaluates**: Task count and complexity
3. **Solo mode**: Superboss executes all tasks directly (fits in context, or mostly complex)
4. **Delegate mode**: Superboss spawns Hardworker for routine tasks (Phase 1), then finishes remaining work (Phase 2)
5. **Superboss verifies** all work before completion

**Benefits:**
- Optimized for large task queues (delegate mode)
- Two-phase execution with no respawning
- Quality verification by stronger model
- Per-task git commits enable granular review
- Maximum 2 GitHub Copilot requests per session

#### Boss-Worker-Coworker (V3 Architecture)

The three-tier orchestration system now uses a Plan, Delegate, Validate, Re-delegate cycle with reviewer/thinker/vision subagents:

```bash
# Start the boss with V3 architecture
/go-boss
```

**How it works:**
1. **Boss** (stronger model) reads standup.md, consults **@thinker** for strategic planning
2. **Boss delegates** to **Worker** (worker model) for cost-effective execution
3. **Boss validates** results via **@reviewer** (independent fresh-eyes quality check)
4. If **Reviewer** finds issues, **Boss** re-delegates fixes (max 10 rounds per task)
5. If **Worker** fails, **Boss** escalates to **Coworker** (fallback model) for complex tasks
6. **Boss** uses **@vision** for any visual verification needs
7. **Boss verifies** all work before proceeding

**Benefits:**
- 80% of work done by cheaper worker model
- Quality verification by independent reviewer subagent
- Re-delegation loop ensures issues get fixed (max 10 rounds)
- Strategic planning via thinker subagent
- Visual verification via vision subagent
- Automatic recovery and escalation on failures
- Progress tracked in standup.md with timestamped markers

## Slash Commands

### Orchestration & Task Management

| Command | Description |
|---------|-------------|
| `/go-superboss` | Start superboss orchestration (solo or delegate mode) - RECOMMENDED |
| `/go-boss` | Start boss orchestration with Plan/Delegate/Validate/Re-delegate cycle |
| `/review-loop` | Fresh-eyes review loop: N workers sequentially review/fix subtasks |
| `/add-todo` | Create GitHub issues from DETAILED-TODO items (today/tomorrow) |
| `/add-done` | Add DONE items from closed issues/PRs (today/tomorrow) |
| `/standup-create` | Convert DETAILED-TODOs to standup TODO bullets |
| `/standup-update` | Update standup.md after completing tasks |
| `/open-standup` | Open Safari/arc with project boards and issue/PR links |
| `/post-standup` | Post standup to Discord (test/prod/dry-run modes) |

### Mail & Communication

| Command | Description |
|---------|-------------|
| `/mail-search` | Search Apple Mail for student emails and correspondence |

**Note**: Some commands reference external scripts (e.g., in your dotfiles). See command files for required scripts and update `<SCRIPTS_PATH>` placeholders.

## Customizing Paths

The configuration uses several path placeholders that you must customize to your setup:

### Path Placeholders in opencode.json
- `<YOUR_WORKING_DIR>` → Your projects directory (e.g., `~/repos/`, `~/projects/`)

### Model Placeholders in opencode.json
- `<YOUR_STRONGER_MODEL>` → Stronger model for orchestration and verification
- `<YOUR_WORKER_MODEL>` → Worker model for cost-effective task execution
- `<YOUR_FALLBACK_MODEL>` → Fallback model for complex tasks (Legacy V2 only)

### Path Placeholders in Commands
Some commands reference scripts in another directory. Replace these placeholders:

- `<MAIL_SCRIPT_PATH>` → Path to scripts directory (e.g., `~/repos/dotfiles`, `~/repos/scripts`)
- `<SCRIPTS_PATH>` → Path to scripts directory (same as above)
- `<NOTES_PATH>` → Path to your notes directory (e.g., `~/Library/Mobile Documents/com~apple~CloudDocs/notes`, `~/notes`)

**Example setup**:
```bash
# If you keep scripts in ~/repos/dotfiles
export MAIL_SCRIPT_PATH="~/repos/dotfiles"
export SCRIPTS_PATH="~/repos/dotfiles/scripts"
export NOTES_PATH="~/Library/Mobile Documents/com~apple~CloudDocs/notes"
```

Then update the command files to use these environment variables, or replace the placeholders directly.

## Customizing Your Working Directory

The `opencode.json` config uses `<YOUR_WORKING_DIR>` as a placeholder for your projects folder. **You must replace this with your actual working directory path** (e.g., `~/repos/`, `~/projects/`, `~/code/`, etc.).

Edit `~/.config/opencode/opencode.json` and replace all instances of:
- `"<YOUR_WORKING_DIR>/**": "allow"` → `"~/your-actual-folder/**": "allow"`

For example:
```json
{
  "permission": {
    "read": {
      "~/repos/**": "allow",
      "~/Downloads/**": "allow"
    }
  }
}
```

## Updating Configurations

To update configurations after pulling new changes:

```bash
# If using symlinks (Option B above), changes are automatic
# Just restart opencode to reload config

    # If using copies (Option A above), re-copy the files:
    cp <REPO_CLONE_PATH>/agents/opencode/opencode.json ~/.config/opencode/
    cp <REPO_CLONE_PATH>/agents/opencode/commands/*.md ~/.config/opencode/commands/
    cp <REPO_CLONE_PATH>/agents/opencode/prompts/*.md ~/.config/opencode/prompts/
    cp -r <REPO_CLONE_PATH>/agents/opencode/skills/* ~/.config/opencode/skills/

    # Example:
    # cp ~/repos/agents/opencode/opencode.json ~/.config/opencode/
```

## Git Aliases

This repo includes a `.gitconfig` file with helpful aliases. To use it:

```bash
# In the repo directory
git config --local include.path ../.gitconfig

# Available aliases:
git diff-config      # Compare repo vs installed config
git backup-config    # Copy system config to repo
git install-config   # Copy repo config to system
git link-config      # Create symlinks from repo to ~/.config/opencode/
```

## Customization

- Add project-specific AGENTS.md files to individual repos for repo-specific instructions
- Create additional slash commands in `opencode/commands/` following the existing format
- Adjust permissions in `opencode.json` based on your security requirements
- Modify agent configurations (models, prompts, tools) as needed

## Troubleshooting

**Config not loading:**
- Verify files are in `~/.config/opencode/`
- Check JSON syntax: `python -m json.tool ~/.config/opencode/opencode.json`
- Restart opencode after config changes

**Commands not appearing:**
- Ensure command files are in `~/.config/opencode/commands/`
- Check that files have `.md` extension and proper frontmatter

**Agent not found:**
- Verify agent names in config match what you're trying to invoke
- Check `mode` is set to `primary` for main agents or `subagent` for @mention agents

## License

MIT
