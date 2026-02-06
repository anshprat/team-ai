# Team AI - Multi-Agent Communication System

A file-based inter-agent communication system that allows multiple AI agents to coordinate and communicate across different IDEs and tools.

**Supported IDEs:**
- Claude Code (full plugin support)
- Cursor (MCP server)
- VSCode/Copilot/Codex (MCP server)
- Google Antigravity (MCP server)
- Continue (context provider)

**Key Features:**
- Register agents and share metadata
- Discover other active agents
- Send/receive messages between agents
- Broadcast messages to multiple agents
- Find agents by capability
- Attach artifacts (screenshots, plans, etc.) to messages
- Shared task list with dependencies and claiming
- Lightweight team management
- Plan approval workflows
- Delegate mode for coordination-only agents

## Installation

```bash
./install.sh
```

This will:
1. Create the `~/.team-ai/` directory structure
2. Copy scripts to `~/.team-ai/bin/`
3. Add the bin directory to your PATH

After installation, either open a new terminal or run:
```bash
source ~/.zshrc  # or ~/.bashrc
```

## Quick Start

### Register an agent
```bash
AGENT_ID=$(ai-register --name "my-agent" --command "Working on feature X")
echo "Registered as: $AGENT_ID"
```

### List active agents
```bash
ai-list
ai-list -v  # Verbose output
```

### Send a message to another agent
```bash
ai-send <target-agent-id> --subject "Update" --body "API endpoint changed"
```

### Check for incoming messages
```bash
ai-check $AGENT_ID
```

### Process and complete messages
```bash
ai-process $AGENT_ID <message-file>
ai-complete $AGENT_ID <message-file> --response "Done"
```

### Deregister when done
```bash
ai-deregister $AGENT_ID
```

## Commands Reference

### Agent Lifecycle

| Command | Description |
|---------|-------------|
| `ai-register` | Register a new agent session |
| `ai-heartbeat` | Update agent's heartbeat timestamp |
| `ai-status` | Update agent state/progress/task |
| `ai-deregister` | Remove agent from the system |

### Discovery

| Command | Description |
|---------|-------------|
| `ai-list` | List all active agents |
| `ai-cleanup` | Remove stale agents (no heartbeat >1hr) |

### Messaging

| Command | Description |
|---------|-------------|
| `ai-send` | Send message to another agent |
| `ai-check` | Check incoming messages |
| `ai-process` | Move message from todo → wip |
| `ai-complete` | Move message from wip → done |

### Task Management

| Command | Description |
|---------|-------------|
| `ai-task-create` | Create a new task with optional dependencies |
| `ai-task-list` | List/filter tasks (`--available` for claimable) |
| `ai-task-claim` | Claim a task (atomic, validates dependencies) |
| `ai-task-complete` | Mark a task as completed |
| `ai-task-update` | Update task fields (priority, deps, etc.) |

### Team Management

| Command | Description |
|---------|-------------|
| `ai-team-create` | Create a team with a lead agent |
| `ai-team-list` | List teams (optionally filter by member) |
| `ai-team-join` | Add an agent to a team |
| `ai-team-leave` | Remove an agent from a team |

### Plan Approval

| Command | Description |
|---------|-------------|
| `ai-plan-submit` | Submit a plan for review |
| `ai-plan-review` | Approve or reject a submitted plan |

### Watching

| Command | Description |
|---------|-------------|
| `ai-watch` | Watch for new messages (foreground or daemon) |
| `ai-watch-stop` | Stop the watcher daemon |

## Directory Structure

```
~/.team-ai/
├── bin/                        # CLI scripts
├── agents/
│   └── <agent-id>/
│       ├── metadata.json       # Agent registration info
│       ├── plan.md             # Current plan (optional)
│       └── incoming/
│           ├── todo/           # Pending messages
│           ├── wip/            # Messages being processed
│           └── done/           # Completed messages
├── tasks/                      # Shared task list (one JSON file per task)
├── teams/                      # Team configs
│   └── <team-id>/
│       └── config.json         # Team metadata and members
├── plans/                      # Plan approval metadata (one JSON per plan)
├── artifacts/                  # Shared artifacts (screenshots, plans, etc.)
├── integrations/               # IDE-specific integration files
│   ├── shared/                 # Shared MCP tools library
│   ├── cursor/                 # Cursor MCP server
│   ├── vscode/                 # VSCode MCP server
│   └── antigravity/            # Google Antigravity MCP server
├── registry.json               # Index of all agents
├── watcher.pid                 # Daemon PID (if running)
└── watcher.log                 # Daemon log file
```

## Command Details

### ai-register

Register a new agent in the system.

```bash
ai-register --name "agent-name" --command "task description" [options]

Options:
  -n, --name NAME           Agent name (required)
  -c, --command COMMAND     Task being executed (required)
  -s, --state STATE         Initial state: active|idle|waiting
  -t, --tags TAGS           Comma-separated tags
  --capabilities CAPS       Comma-separated capabilities
  --model MODEL             AI model being used
  --session-id ID           Claude Code session ID
  --parent AGENT_ID         Parent agent ID
  --role ROLE               Agent role: worker|lead|delegate (default: worker)

# Returns the new agent ID
```

### ai-heartbeat

Update the heartbeat timestamp.

```bash
ai-heartbeat <agent-id>
```

### ai-status

Update agent status fields.

```bash
ai-status <agent-id> [options]

Options:
  -s, --state STATE         Update state
  -t, --task TASK           Update current task
  -p, --progress PERCENT    Update progress (0-100)
  --files FILES             Comma-separated files in progress
  --git-branch BRANCH       Update git branch
  --role ROLE               Update role: worker|lead|delegate
```

### ai-list

List registered agents.

```bash
ai-list [options]

Options:
  -a, --all       Show all agents including completed
  -v, --verbose   Show detailed information
  -j, --json      Output as JSON
```

### ai-send

Send a message to another agent.

```bash
ai-send <target> --subject "Subject" --body "Message" [options]

Options:
  -f, --from FROM_ID       Sender agent ID
  -s, --subject SUBJECT    Message subject (required)
  -b, --body BODY          Message body (required unless --file)
  --file FILE              Read body from file
  -p, --priority PRIORITY  Priority: low|normal|high
  -t, --type TYPE          Type: request|info|query
  --context CONTEXT        Additional context
  --expected RESPONSE      Expected response description
  --artifact PATH          Attach artifact file (screenshot, plan, etc.)
```

Example with artifact:
```bash
ai-send backend --subject "Screenshot" --body "See UI bug" --artifact ./screenshot.png
```

### ai-check

Check incoming messages.

```bash
ai-check <agent-id> [options]

Options:
  -q, --quiet    Only output message count
  -j, --json     Output as JSON
  -w, --wip      Also show messages in progress
  -a, --all      Show all messages (todo + wip + done)
```

### ai-process

Start processing a message (moves todo → wip).

```bash
ai-process <agent-id> <message-file>
```

### ai-complete

Complete a message (moves wip → done).

```bash
ai-complete <agent-id> <message-file> [options]

Options:
  -r, --response TEXT      Add response text
  --response-file FILE     Read response from file
```

### ai-watch

Watch for new messages.

```bash
ai-watch <agent-id> [options]

Options:
  -i, --interval SECONDS   Poll interval (default: 5)
  -d, --daemon             Run in background
```

### ai-watch-stop

Stop the watcher daemon.

```bash
ai-watch-stop
```

### ai-cleanup

Remove stale agents and unclaim orphaned tasks.

```bash
ai-cleanup [options]

Options:
  -t, --threshold SECONDS  Stale threshold (default: 3600)
  -n, --dry-run           Show what would be removed
  -f, --force             Remove without confirmation
```

### ai-task-create

Create a new task.

```bash
ai-task-create --title "Task title" [options]

Options:
  --title TITLE               Task title (required)
  --description DESC          Task description
  --priority PRIORITY         Priority: low|normal|high (default: normal)
  --depends-on ID1,ID2        Comma-separated dependency task IDs
  --tags TAG1,TAG2            Comma-separated tags
  --team TEAM_ID              Associated team
  --created-by AGENT_ID       Creator agent ID
```

### ai-task-list

List and filter tasks.

```bash
ai-task-list [options]

Options:
  --status STATUS            Filter by status (pending|in_progress|completed|blocked)
  --available                Show only claimable tasks (pending + deps met)
  --assignee AGENT_ID        Filter by assignee
  --team TEAM_ID             Filter by team
  --tag TAG                  Filter by tag
  -j, --json                 Output as JSON
```

### ai-task-claim

Claim a task (atomic with dependency validation).

```bash
ai-task-claim TASK_ID --agent AGENT_ID
```

### ai-task-complete

Complete a task.

```bash
ai-task-complete TASK_ID [--result "Summary of work done"]
```

### ai-task-update

Update task fields.

```bash
ai-task-update TASK_ID [options]

Options:
  --title TITLE              Update title
  --status STATUS            Update status
  --priority PRIORITY        Update priority
  --add-dep TASK_ID          Add dependency
  --remove-dep TASK_ID       Remove dependency
```

### ai-team-create

Create a team.

```bash
ai-team-create --name "team-name" --lead AGENT_ID [--description "..."]
```

### ai-team-list

List teams.

```bash
ai-team-list [--agent AGENT_ID] [-j]
```

### ai-team-join

Join a team.

```bash
ai-team-join TEAM_ID --agent AGENT_ID
```

### ai-team-leave

Leave a team.

```bash
ai-team-leave TEAM_ID --agent AGENT_ID
```

### ai-plan-submit

Submit a plan for approval.

```bash
ai-plan-submit --title "Plan title" --file plan.md --reviewer AGENT_ID [--from AGENT_ID]
```

### ai-plan-review

Approve or reject a plan.

```bash
ai-plan-review PLAN_ID --action approve
ai-plan-review PLAN_ID --action reject --feedback "Needs test coverage"
```

## MCP Tools (Cursor, VSCode, Antigravity)

The MCP server integrations provide the following tools:

| Tool | Description |
|------|-------------|
| `team-ai-list` | List all registered agents |
| `team-ai-register` | Register this session as an agent |
| `team-ai-send` | Send message to another agent |
| `team-ai-check` | Check incoming messages |
| `team-ai-status` | Get Team AI installation status |
| `team-ai-agents-by-capability` | Find agents with specific capabilities |
| `team-ai-broadcast` | Send message to multiple agents |
| `team-ai-watch-start` | Start watching for incoming messages |
| `team-ai-task-create` | Create a new task |
| `team-ai-task-list` | List/filter tasks |
| `team-ai-task-claim` | Claim a task |
| `team-ai-task-complete` | Complete a task |
| `team-ai-task-update` | Update task fields |
| `team-ai-team-create` | Create a team |
| `team-ai-team-list` | List teams |
| `team-ai-team-join` | Join a team |
| `team-ai-team-leave` | Leave a team |
| `team-ai-plan-submit` | Submit a plan for review |
| `team-ai-plan-review` | Approve or reject a plan |
| `team-ai-plan-list` | List all plans |

### Setting up Google Antigravity

1. Run `./install.sh` to install the MCP server
2. Run `npm install` in `~/.team-ai/integrations/antigravity/mcp-server/`
3. Restart Antigravity

The installer automatically creates/updates `~/.gemini/antigravity/mcp_config.json` with absolute paths, preserving any existing MCP server configurations.

The agent will auto-register on startup and auto-deregister on exit.

## Message Format

Messages are stored as Markdown files with YAML frontmatter:

```markdown
---
id: message-uuid
from: sender-agent-id
to: receiver-agent-id
priority: normal
created: 2026-01-19T10:00:00Z
type: request
artifacts:
  - type: screenshot
    id: artifact-uuid
    path: ~/.team-ai/artifacts/artifact-uuid.png
---

# Subject: Brief title

## Context
Why this message is being sent

## Request
What the sender needs

## Expected Response
What kind of response is expected

## Attached Artifact
- **Type:** screenshot
- **ID:** artifact-uuid
- **Path:** ~/.team-ai/artifacts/artifact-uuid.png
```

### Artifact Types

| Extension | Type |
|-----------|------|
| png, jpg, gif, webp | screenshot |
| md, markdown | plan |
| pdf | document |
| json | data |
| txt, log | text |
| other | file |

## Example: Multi-Agent Workflow

### Terminal 1 - Backend Agent
```bash
# Register backend agent
BACKEND_ID=$(ai-register --name "backend" --command "API development")
echo "Backend ID: $BACKEND_ID"

# Work on the API...
ai-status $BACKEND_ID --task "Implementing /users endpoint" --progress 50

# Check for messages
ai-check $BACKEND_ID

# Deregister when done
ai-deregister $BACKEND_ID
```

### Terminal 2 - Frontend Agent
```bash
# Register frontend agent
FRONTEND_ID=$(ai-register --name "frontend" --command "UI development")
echo "Frontend ID: $FRONTEND_ID"

# Send message to backend
ai-send backend --subject "API Question" --body "What's the schema for /users?" --type query

# Watch for responses
ai-watch $FRONTEND_ID
```

## Heartbeat System

Team AI uses heartbeats to track agent liveness and automatically clean up stale agents.

### Automatic Heartbeat

All integrations support automatic heartbeat:

| Integration | Heartbeat Trigger |
|-------------|-------------------|
| Claude Code | On session start + every user prompt + every tool call |
| Cursor | On startup + every tool call + every 5 minutes |
| VSCode | On startup + every tool call + every 5 minutes |
| Antigravity | On startup + every tool call + every 5 minutes |
| Perplexity | On startup + every tool call + every 5 minutes |

### Manual Heartbeat

```bash
# Update heartbeat for an agent
ai-heartbeat <agent-id>
```

### Stale Agent Cleanup

```bash
ai-cleanup                # Remove agents stale >1 hour
ai-cleanup -t 1800        # Remove agents stale >30 minutes
ai-cleanup -n             # Dry run (show what would be removed)
```

## Notes

- Agents automatically update heartbeat on activity (tool calls for all agents, plus every 5 minutes for MCP servers)
- Stale agents (no heartbeat >1 hour) can be cleaned up with `ai-cleanup`
- The system uses file locking for safe concurrent access to registry.json
- Python 3 is used for reliable JSON parsing when available, with bash fallbacks

## License

MIT
