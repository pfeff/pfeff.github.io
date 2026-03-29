# Architecture

## Design Philosophy

AgentCoordinator is a **passive information system**. It records, monitors, and advises — it never directs. Agents and humans make decisions; the coordinator provides context so those decisions are well-informed.

Key principles:

- **Agent-agnostic**: Works with any AI agent (Claude Code, Cursor, Copilot) and human operators through a uniform REST API
- **Fault-tolerant**: Erlang/OTP supervision trees with "let it crash" recovery
- **Real-time**: Phoenix LiveView dashboard for live visibility into agent work
- **Policy enforcement**: Policies evaluate actions with block, warn, and escalate outcomes

## System Layers

```
┌───────────────────────────────────────────────────┐
│               Phoenix Web Layer                    │
│  ┌─────────────┐  ┌────────────┐  ┌───────────┐ │
│  │  REST API    │  │  LiveView  │  │ WebSocket │ │
│  │  (Agents)    │  │ (Humans)   │  │ (Future)  │ │
│  └─────────────┘  └────────────┘  └───────────┘ │
└───────────────────────────────────────────────────┘
                        │
┌───────────────────────────────────────────────────┐
│            Coordination Core (OTP)                 │
│  MissionSupervisor (DynamicSupervisor)            │
│    └─ MissionProcess (per mission)                │
│        ├─ MissionServer (GenServer)               │
│        ├─ TaskExecutor (Task.Supervisor)          │
│        └─ PolicyChecker (GenServer)               │
└───────────────────────────────────────────────────┘
                        │
┌───────────────────────────────────────────────────┐
│              Domain Services                       │
│  Missions · Agents · Sprints · Policies           │
│  Traceability · Knowledge · Accounts              │
└───────────────────────────────────────────────────┘
                        │
┌───────────────────────────────────────────────────┐
│           PostgreSQL + pgvector                    │
└───────────────────────────────────────────────────┘
```

## Project Management Model

The coordinator adapts Scrum concepts for AI agent work:

| Concept | Purpose | Relationship |
|---------|---------|-------------|
| **Sprint** | Time container | A time-boxed iteration with a goal |
| **Mission** | Goal container | A body of work with multiple principals (agents/humans) |
| **Task** | Work unit | Belongs to a Mission (why) and optionally a Sprint (when) |
| **Objective** | Strategic goal | Has many KeyResults |
| **KeyResult** | Measurable outcome | Has many Requirements |
| **Requirement** | Tracked requirement | Has many Tasks (advisory linkage) |

Sprint and Mission are orthogonal. A mission can span sprints; a sprint can contain tasks from multiple missions.

The **traceability chain** (Objective → KeyResult → Requirement → Task) provides full coverage visibility from strategic goals to implementation tasks.

## OTP Process Architecture

Each active mission spawns a supervision tree:

- **MissionServer** — GenServer holding in-memory mission state. Loads from the database at init, persists changes on a periodic timer.
- **TaskExecutor** — Task.Supervisor for concurrent task execution within a mission.
- **PolicyChecker** — GenServer evaluating policy compliance for task transitions.

These processes are managed by `MissionSupervisor`, a `DynamicSupervisor` that starts/stops mission process trees on demand.

## Agent Adapter Framework

Agents communicate via the REST API. The adapter framework provides:

- **AgentAdapter** behaviour — Common interface for agent integrations
- **AgentRegistry** — Tracks registered agents and their capabilities
- **AdapterManager** — Periodic health checks against registered adapters

Built-in adapters: Claude Code, OpenAI, Claude CLI.

## Real-Time Updates

PubSub topics drive LiveView updates:

| Topic | Subscribers | Events |
|-------|------------|--------|
| `missions` | Mission index, Dashboard | Created, updated |
| `tasks` | Mission index, Dashboard | Created, updated |
| `agents` | Dashboard | Created, updated, activity, heartbeat |
| `mission:{id}` | Mission show | Mission/task updates |
| `task:{id}` | Task show | Task updates |
| `objectives` | Objective index, Traceability | Created, updated |
| `requirements` | Requirement index, Traceability | Created, updated |

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Language | Elixir 1.16 / Erlang/OTP 26 |
| Web | Phoenix 1.8, LiveView 1.1 |
| Database | PostgreSQL 15+ with pgvector |
| ORM | Ecto 3.x |
| HTTP Client | Req |
| CSS | Tailwind CSS |
| Auth | bcrypt + session tokens |
