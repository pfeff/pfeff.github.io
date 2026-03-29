---
date:
  created: 2026-02-16
categories:
  - Dev Log
tags:
  - ai-agents
  - build-in-public
  - elixir
---

# Building a Project Manager for AI Coding Agents

I manage a small fleet of AI coding agents. Claude Code, Codex, Gemini — each with different strengths, different costs, different failure modes. And every day I hit the same problem: there's no good way to manage them.

<!-- more -->

## The management gap

If you're using AI coding agents, you probably recognize this pattern: you assign work through a chat window, check back later, and hope for the best. Maybe the agent burned through your API budget on a yak shave. Maybe it silently went off-track. Maybe it produced great work and you have no idea what it cost or how long it took.

Now multiply that by three or four agents across multiple repositories.

The tools that exist — Jira, Linear, GitHub Projects — were designed for human developers. They track tickets and sprints, not token spend and agent routing decisions. The AI coding tools themselves — Copilot, Claude Code, Devin — are single-agent. They don't talk to each other, don't share context, and don't report back to a unified dashboard.

There's a missing layer: the management plane.

## What I'm building

I'm building a command-and-control plane for AI coding agents — the layer between the people who direct work and the AI that executes it. (The project is called AgentCoordinator for now. Working on a better name.)

The core capabilities:

- **Mission coordination** — break down goals into tasks, assign to agents, track completion
- **Multi-agent routing** — dispatch work to the right agent based on capability and cost
- **Cost observability** — know what you're spending per task, per agent, per mission
- **Policy enforcement** — declarative rules for what agents can and cannot do
- **Audit trail** — every agent action logged with integrity checksums
- **Real-time dashboard** — watch agents work as it happens

I'm building it because I need it. I run multiple agents daily on a multi-repo project and the coordination overhead is real. My current workflow involves manually switching between terminals, checking on agents one by one, and keeping track of costs in my head. That doesn't scale — even for a team of one.

## Why Elixir

The tech stack is Elixir/Phoenix with OTP, PostgreSQL, and pgvector. This isn't a typical choice for a developer tool, so it's worth explaining.

AI agent coordination is fundamentally a concurrency problem. You have multiple agents running in parallel, each with their own lifecycle, and you need to supervise them, handle failures gracefully, and maintain state across restarts. This is exactly what Erlang/OTP was designed for.

Each active mission runs as a supervised process tree:

```
MissionSupervisor (DynamicSupervisor)
├─ Mission:abc (GenServer)
│   ├─ TaskExecutor (Task.Supervisor)
│   └─ PolicyChecker (GenServer)
└─ Mission:def (GenServer)
    ├─ TaskExecutor (Task.Supervisor)
    └─ PolicyChecker (GenServer)
```

If a task executor crashes, only that task restarts — not the whole mission. If a mission server goes down, the supervisor brings it back with its last known state. This kind of fault tolerance is baked into the runtime, not bolted on.

Phoenix LiveView gives us real-time dashboards without a separate frontend build. And pgvector lets agents share context through semantic search — stored artifacts, past decisions, lessons learned — all in Postgres.

## The market gap

I evaluated the landscape before committing to this. The AI coding agent space is well-funded — Devin raised $696M, Copilot has 20M+ users — but they're all building better agents, not better management for agents.

Nobody is building the control plane. Jira is adding AI features to human-centric project management. GitHub lets you assign issues to Copilot. But there's no tool that:

- Shows a manager what three different agents did this week and what it cost
- Routes work to the cheapest capable agent automatically
- Enforces policies (no production deploys, review required for database changes)
- Provides audit trails for regulated industries
- Creates feedback loops where rejected PRs improve future routing

Gartner predicts 40% of enterprise apps will embed AI agents by end of 2026. As adoption grows, so does the management problem. Right now engineering teams are flying blind.

## Building in public

I'm building this in the open for a specific reason: I need to know if this problem is common enough to sustain a product.

I'm the first customer. I know the tool is useful because I need it daily. The question is whether enough other teams have the same pain point. So rather than building in stealth and launching to crickets, I'm sharing the journey — what works, what doesn't, what I'm learning about managing AI agents — and letting organic interest answer the question.

If people start asking "can I use this?" — that's the signal to invest in multi-tenancy, polish, and a proper release. If it's crickets after a couple months of sharing — that's useful data too.

## What's next

The foundation is in place — domain model, OTP supervision tree, agent adapter framework, policy engine. The next priorities are:

1. Get the core mission lifecycle working end-to-end (create → assign → execute → complete)
2. Build the first agent adapter (Claude Code) and route real work through it
3. Stand up the LiveView dashboard so there's something to show

I'll be posting regular dev logs here as things progress. If you're managing AI coding agents and hitting the same coordination problems, I'd like to hear about it.

---

<small>Made with AI</small>
