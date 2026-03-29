# Agent Coordinator

**A command-and-control plane for AI coding agents.**

Assign work to AI agents, track cost and quality across backends, approve outputs, and get full audit trails. The missing management layer between "we have AI tools" and "leadership can direct them."

## The Problem

Engineering teams are adopting AI coding agents — Copilot, Claude Code, Devin, and others — but no tool answers the basic management questions:

- **What did the agents do?** No unified view across backends.
- **What did it cost?** No cost-per-task visibility or ROI tracking.
- **Was it worth it?** No quality metrics or feedback loops.
- **Who approved it?** No governance or audit trail for regulated industries.

There's no project management layer purpose-built for AI agents — just human PM tools with AI bolted on.

## What We're Building

AgentCoordinator is an AI-native project management platform — "Jira for Agents":

- **Mission coordination** — Define goals, decompose into tasks, assign to agents
- **Multi-agent routing** — Dispatch work across agent backends based on capability and cost
- **Cost observability** — Track spend per task, per agent, per mission
- **Policy enforcement** — Declarative rules governing what agents can and cannot do
- **Audit trail** — Immutable trace logs with integrity checksums for every agent action
- **Real-time dashboard** — Phoenix LiveView interface for monitoring agent work

## Documentation

- [Getting Started](getting-started.md)
- [Architecture](architecture.md)
- [API Reference](api-reference.md)

## Follow the Build

We're building in public. Check the [blog](../../blog/index.md) for dev logs, technical deep-dives, and lessons learned.
