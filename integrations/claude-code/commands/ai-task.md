---
description: Manage shared tasks (create, list, claim, complete, update)
argument-hint: [create|list|claim|complete|update] [args...]
allowed-tools:
  - Bash
  - Read
---
# Team AI: Task Management

Manage the shared task list for multi-agent coordination.

## Instructions

Parse the user's intent and run the appropriate command:

### Create a task
```bash
ai-task-create --title "Task title" --description "Details" --priority high --depends-on TASK_ID1,TASK_ID2 --tags "backend,auth"
```

### List tasks
```bash
ai-task-list                        # All tasks
ai-task-list --available            # Claimable tasks only
ai-task-list --status in_progress   # Filter by status
ai-task-list --team TEAM_ID         # Filter by team
```

### Claim a task
```bash
ai-task-claim TASK_ID --agent AGENT_ID
```

### Complete a task
```bash
ai-task-complete TASK_ID --result "Summary of what was done"
```

### Update a task
```bash
ai-task-update TASK_ID --priority high --add-dep OTHER_TASK_ID
```

## Task States
- `pending` - Not yet started, available for claiming (if deps met)
- `in_progress` - Claimed by an agent
- `completed` - Done
- `blocked` - Manually blocked

## Dependencies
Tasks can depend on other tasks. A task cannot be claimed until all its dependencies are completed.
