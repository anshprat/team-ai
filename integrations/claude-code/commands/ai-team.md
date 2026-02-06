---
description: Manage teams (create, list, join, leave)
argument-hint: [create|list|join|leave] [args...]
allowed-tools:
  - Bash
  - Read
---
# Team AI: Team Management

Manage teams for coordinating groups of agents.

## Instructions

Parse the user's intent and run the appropriate command:

### Create a team
```bash
ai-team-create --name "team-name" --lead AGENT_ID --description "What this team works on"
```

### List teams
```bash
ai-team-list                    # All teams
ai-team-list --agent AGENT_ID   # Teams an agent belongs to
```

### Join a team
```bash
ai-team-join TEAM_ID --agent AGENT_ID
```

### Leave a team
```bash
ai-team-leave TEAM_ID --agent AGENT_ID
```

## Notes
- The lead is informational (the team creator), not a hard hierarchy
- Any team member can create tasks, send messages, etc.
- Teams are optional groupings for organizing work
- Tasks can be associated with a team via `--team TEAM_ID`
