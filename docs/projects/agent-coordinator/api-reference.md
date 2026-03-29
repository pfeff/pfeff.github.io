# API Reference

All API endpoints require authentication via Bearer token in the `Authorization` header.

```
Authorization: Bearer YOUR_API_TOKEN
```

## Missions

### List missions

```
GET /api/missions
```

### Create mission

```
POST /api/missions
```

```json
{
  "mission": {
    "mission_id": "mission-001",
    "goal": "Build authentication module",
    "status": "planning"
  }
}
```

### Get mission

```
GET /api/missions/:id
```

### Update mission

```
PATCH /api/missions/:id
```

### Start mission

Starts the OTP process tree for a mission.

```
POST /api/missions/:id/start
```

### Pause mission

Stops the OTP process tree for a mission.

```
POST /api/missions/:id/pause
```

## Tasks

### Create task (under a mission)

```
POST /api/missions/:mission_id/tasks
```

```json
{
  "task": {
    "task_id": "task-001",
    "objective": "Implement login endpoint",
    "status": "pending"
  }
}
```

### Get task

```
GET /api/tasks/:id
```

### Update task

```
PATCH /api/tasks/:id
```

### Execute task

Dispatches a task to its assigned agent.

```
POST /api/tasks/:id/execute
```

### Report task progress

```
POST /api/tasks/:id/report
```

```json
{
  "progress": {
    "status": "in_progress",
    "outputs": { "files_changed": 3 },
    "logs": [{ "message": "Tests passing", "timestamp": "2026-02-26T12:00:00Z" }]
  }
}
```

**Valid status values:** `pending`, `in_progress`, `blocked`, `pending_review`, `rejected`, `completed`, `failed`

Status transitions are enforced by a state machine. Invalid transitions return 422.

### Approve task

Transitions a task from `pending_review` to `completed`. Merges the associated GitHub PR if `task.outputs.pr_url` is set.

```
POST /api/tasks/:id/approve
```

```json
{
  "comment": "LGTM"
}
```

### Reject task

Transitions a task from `pending_review` to `rejected`. Records reviewer feedback in the trace log.

```
POST /api/tasks/:id/reject
```

```json
{
  "comment": "Tests failing, please fix"
}
```

## Mission Agents

### List agents on a mission

```
GET /api/missions/:mission_id/agents
```

### Add agent to mission

```
POST /api/missions/:mission_id/agents
```

```json
{
  "agent_id": 1
}
```

### Remove agent from mission

```
DELETE /api/missions/:mission_id/agents/:id
```

## Agents

### List agents

```
GET /api/agents
```

### Create agent

```
POST /api/agents
```

```json
{
  "agent": {
    "agent_id": "claude-code-1",
    "name": "Claude Code Agent",
    "adapter_module": "claude_code",
    "capabilities": ["coding", "testing"]
  }
}
```

### Get agent

```
GET /api/agents/:id
```

### Update agent

```
PATCH /api/agents/:id
```

### Report agent activity

Reports agent state changes. Agents are auto-registered on first activity report if not already known.

```
POST /api/agents/:agent_id/activity
```

```json
{
  "event": "task_start",
  "context": {
    "task_id": "task-001",
    "description": "Working on login endpoint"
  }
}
```

**Valid events**: `session_start`, `task_start`, `task_complete`, `blocked`, `completed`, `error`, `stop`, `waiting_for_input`, `heartbeat`

## Sprints

### List sprints

```
GET /api/sprints
```

### Create sprint

```
POST /api/sprints
```

```json
{
  "sprint": {
    "sprint_id": "sprint-1",
    "name": "Sprint 1",
    "goal": "MVP features",
    "start_date": "2026-02-24",
    "end_date": "2026-03-07"
  }
}
```

### Get sprint

```
GET /api/sprints/:id
```

### Update sprint

```
PATCH /api/sprints/:id
```

## Policies

### List policies

```
GET /api/policies
```

### Create policy

```
POST /api/policies
```

```json
{
  "policy": {
    "policy_id": "no-force-push",
    "description": "Prevent force pushing to protected branches",
    "scope": "global",
    "conditions": { "action_type": "git_push", "force": true },
    "actions": [{ "block": "Force push not allowed" }]
  }
}
```

### Get policy

```
GET /api/policies/:id
```

### Update policy

```
PATCH /api/policies/:id
```

## Traceability

### List trace logs

```
GET /api/trace
```

### Get mission trace

```
GET /api/missions/:mission_id/trace
```

### Replay mission trace

```
POST /api/missions/:mission_id/replay
```

## Knowledge Base

### Store document

```
POST /api/knowledge/documents
```

```json
{
  "title": "Architecture Decision Record",
  "content": "We chose PostgreSQL because...",
  "metadata": { "type": "adr" }
}
```

### Get document

```
GET /api/knowledge/documents/:id
```

### Semantic search

```
POST /api/knowledge/search
```

```json
{
  "query": "authentication design decisions",
  "limit": 5
}
```

## Objectives

### List objectives

```
GET /api/objectives
```

### Create objective

```
POST /api/objectives
```

```json
{
  "objective": {
    "objective_id": "S1",
    "title": "Generate Revenue",
    "description": "AI-Native PM",
    "priority": 1
  }
}
```

### Get objective

```
GET /api/objectives/:id
```

### Update objective

```
PATCH /api/objectives/:id
```

### List key results

```
GET /api/objectives/:objective_id/key_results
```

### Create key result

```
POST /api/objectives/:objective_id/key_results
```

```json
{
  "key_result": {
    "kr_id": "KR1.1",
    "title": "Launch PM Tool"
  }
}
```

## Requirements

### List requirements

```
GET /api/requirements
```

### Create requirement

```
POST /api/requirements
```

```json
{
  "requirement": {
    "requirement_id": "AC-CORE-01",
    "title": "Mission Coordination",
    "priority": "p1",
    "horizon": "h1"
  }
}
```

### Get requirement

```
GET /api/requirements/:id
```

### Update requirement

```
PATCH /api/requirements/:id
```

## Traceability Matrix

### Get traceability matrix

```
GET /api/traceability
```

Returns the full OKR→Requirement→Task tree with coverage statistics including total objectives, requirements, tasks, linked/unlinked task counts, and task coverage percentage.

## GitHub Integration

### List synced issues

```
GET /api/github/issues
```

### Get issue

```
GET /api/github/issues/:id
```

### Trigger sync

```
POST /api/github/sync
```
