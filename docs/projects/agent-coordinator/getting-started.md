# Getting Started

## Prerequisites

- Elixir 1.16+ / Erlang/OTP 26+
- PostgreSQL 15+ with the [pgvector](https://github.com/pgvector/pgvector) extension
- Node.js (for asset compilation)

## Quick Start

### 1. Clone and install dependencies

```bash
git clone https://github.com/pfeff/agent-coordinator.git
cd agent-coordinator
mix setup
```

`mix setup` runs `deps.get`, creates the database, runs migrations, seeds data, and builds assets.

### 2. Start the database

If you don't have a local PostgreSQL instance, start one with Docker:

```bash
docker run --rm --name agent-coordinator-db \
  -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres \
  -e POSTGRES_DB=agent_coordinator_dev -p 5432:5432 \
  pgvector/pgvector:pg15
```

!!! note
    Use the `pgvector/pgvector` image instead of plain `postgres` — it includes the pgvector extension needed for semantic search.

### 3. Start the server

```bash
mix phx.server
```

Visit [http://localhost:4000](http://localhost:4000) to see the landing page. The dashboard is at `/dashboard` (requires login).

### 4. Create an admin user

```bash
mix gen.admin admin@example.com
```

You'll be prompted for a password (minimum 12 characters).

### 5. Get an API token

API requests require a Bearer token:

```bash
mix gen.api_token
```

### 6. Create your first mission

```bash
curl -X POST http://localhost:4000/api/missions \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "mission": {
      "mission_id": "mission-001",
      "goal": "Build the authentication module",
      "status": "planning"
    }
  }'
```

## Development Workflow

```bash
# Run tests
mix test

# Pre-commit checks (compile warnings, format, tests)
mix precommit

# Generate API documentation
mix docs

# Reset the database
mix ecto.reset
```

## What's Next

- [Architecture](architecture.md) — How the system is designed
- [API Reference](api-reference.md) — REST API endpoints
