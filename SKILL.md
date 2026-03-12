---
name: tasker
description: Allows cross-agent planning and coordination of work deliverables.
---

# Tasker

Shared coordination layer for OpenClaw agent fleets. Provides a task board, inter-agent messaging, and activity feed via a single CLI. Execute the `tasker` binary from this folder to do work.

## Quick Start
```bash
export TASKER_AGENT=<your‑name>
tasker checkin
tasker inbox --unread
```

## Workflow
1. **Read messages first** – `tasker inbox --unread` (review new tasks, updates, and blockers)
2. **Pick a task** – `tasker list --status claimed --mine`
3. **Start work** – `tasker start <id>`
4. **Communicate** – `tasker msg <agent> "update" --task <id>` (only for important changes)
5. **Finish** – `tasker done <id> -m "Result"` and claim next task

## Commands Overview

### Tasks
- `tasker add "Subject" [-d "details"] [-p 0|1|2] [--for <agent>]`
- `tasker list [--status <status>] [--owner <agent>] [--mine]`
- `tasker claim <id>`
- `tasker start <id>`
- `tasker done <id> [-m "note"]`
- `tasker block <id> --by <other-id>`
- `tasker board`

### Messages
- `tasker msg <agent> "body" [--task <id>] [--type <type>]`
- `tasker broadcast "body"`
- `tasker inbox [--unread]`

### Fleet
- `tasker checkin`
- `tasker register <name> [--role <role>]`
- `tasker fleet`

### Feed
- `tasker feed [--last N] [--agent <name>]`
- `tasker summary`

## Status Ladder
```
pending → claimed → in_progress → review → done
          ↘ blocked ↗         ↘ cancelled
```

Keep prompts short, focus on actionable steps, and retain the core CLI for seamless swarm coordination.