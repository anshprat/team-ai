# CLAUDE.md Team AI Section

This content is automatically appended to `~/.claude/CLAUDE.md` during installation when Claude Code is detected.

---

## Team AI Integration

You have access to the Team AI multi-agent communication system. This allows coordination with other AI agents (Claude Code sessions, Cursor, Continue, etc.).

### Quick Commands

- `/ai-list` - List all registered agents
- `/ai-register` - Register this session as an agent
- `/ai-send` - Send a message to another agent
- `/ai-check` - Check for incoming messages
- `/ai-task` - Manage shared tasks (create, list, claim, complete)
- `/ai-team` - Manage teams (create, list, join, leave)
- `/ai-plan` - Submit or review plans for approval

### Shell Commands

```bash
ai-list              # List all active agents
ai-register --name "name" --command "task"  # Register
ai-send TARGET --subject "subj" --body "msg"  # Send message
ai-check AGENT_ID    # Check messages
```

### When to Use Team AI

- **Before major changes**: Check if other agents are working on related files
- **For coordination**: Send messages to notify other agents about breaking changes
- **For questions**: Query other agents for information about their work areas
- **After completing work**: Notify relevant agents about completed changes

### Task Management

```bash
ai-task-create --title "Task" --priority high --depends-on ID1,ID2
ai-task-list --available         # Claimable tasks only
ai-task-claim TASK_ID --agent AGENT_ID
ai-task-complete TASK_ID --result "Summary"
ai-task-update TASK_ID --priority high
```

### Team Management

```bash
ai-team-create --name "team-name" --lead AGENT_ID
ai-team-list                     # All teams
ai-team-join TEAM_ID --agent AGENT_ID
ai-team-leave TEAM_ID --agent AGENT_ID
```

### Plan Approval

```bash
ai-plan-submit --title "Plan" --file plan.md --reviewer AGENT_ID
ai-plan-review PLAN_ID --action approve
ai-plan-review PLAN_ID --action reject --feedback "Needs tests"
```

### Delegate Mode

Register with a role to indicate coordination-only behavior:

```bash
ai-register --name "lead" --command "coordinating" --role delegate
```

Roles: `worker` (default), `lead`, `delegate`. Delegate agents focus on coordination rather than code changes.

### Heartbeat System

Team AI uses heartbeats to track agent liveness. Your Claude Code session automatically sends heartbeats:

- **On session start**: Heartbeat is set when you register
- **On each prompt**: Heartbeat is updated whenever you send a message
- **On each tool call**: Heartbeat is updated after every tool use (keeps agent alive during long tasks)
- **Stale detection**: Agents without heartbeats for >1 hour are marked stale

```bash
ai-heartbeat AGENT_ID    # Manually update heartbeat
ai-cleanup               # Remove stale agents (>1 hour without heartbeat)
ai-cleanup -t 1800       # Remove agents stale for >30 minutes
```
