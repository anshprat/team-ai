---
description: Submit or review plans for approval
argument-hint: [submit|review] [args...]
allowed-tools:
  - Bash
  - Read
---
# Team AI: Plan Approval

Submit plans for review or approve/reject submitted plans.

## Instructions

Parse the user's intent and run the appropriate command:

### Submit a plan for approval
```bash
ai-plan-submit --title "Plan title" --file plan.md --reviewer AGENT_ID --from MY_AGENT_ID
```

Or with inline content:
```bash
ai-plan-submit --title "Plan title" --body "Plan content here..." --reviewer AGENT_ID
```

### Review a plan (approve or reject)
```bash
ai-plan-review PLAN_ID --action approve
ai-plan-review PLAN_ID --action reject --feedback "Needs test coverage"
```

## How It Works
1. An agent submits a plan with a designated reviewer
2. The reviewer receives a message in their inbox
3. The reviewer approves or rejects with optional feedback
4. The submitter is notified of the decision

## Plan States
- `pending` - Awaiting review
- `approved` - Reviewer approved
- `rejected` - Reviewer rejected (with feedback)
