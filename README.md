# Tasker (formerly [Mission Control](https://github.com/alanxurox/mission-control))

Shared coordination layer for [OpenClaw](https://github.com/openclaw/openclaw) agent fleets. Task board, inter-agent messaging, and activity feed — all via CLI.

**Quick Start:**

```
tasker init
tasker register jarvis --role lead
tasker add "Research competitors"
tasker board
```

```
═══ TASKER ═══  14:32  agent: jarvis

── ○ pending (1) ──
  #1 Research competitors
```

## Why

Running 3+ OpenClaw agents? You have the "who is doing what?" problem. Tasker gives every agent a shared task board, messaging, and activity feed through a single `tasker` command.

- **No Convex signup.** No React build. No npm install.
- **SSH-queryable.** `ssh your-vps tasker board`
- **Works offline.** Local SQLite, no cloud dependency.
- **OpenClaw-native.** Install as a skill, works with existing agents.

## Install

```bash
git clone https://github.com/swissarmybud/tasker.git
chmod +x tasker/tasker
```

## Integration

The `tasker` executable is just a bash script, and the `TASKER_AGENT` ID is just an environment variable. Making these available directly or passing via container-mounts to agent sandboxes is standard OS-ops, i.e.:

```
# Bash CLI
cd tasker
./tasker init

export TASKER_AGENT="John"
./tasker checkin
```

```
# OpenClaw skill mount
ln -s /home/$(whoami)/tasker /home/$(whoami)/.openclaw/skills/tasker
```

```
# OpenClaw agent sandbox
{
  "sandbox": {
    "docker": {
      "env": {
        "TASKER_AGENT": "Adam",
        "TASKER_DB": "/workspace/tasker.db"
      },
      "binds": [
        "~/.openclaw/skills/tasker/tasker:/bin/tasker:ro",
        "~/.openclaw/skills/tasker/team.db:/workspace/tasker.db:rw"
      ]
    }
  }
}
```

## Example Workflow

```bash
# COORDINATOR PERSONALITY
# - - - - - - - - - -

# Register your agents
tasker register jarvis --role "team lead"
tasker register researcher --role "research & analysis"
tasker register writer --role "content creation"

# Create tasks
tasker add "Research competitor pricing" --for researcher
tasker add "Draft blog post outline" -p 1

# Check the board
tasker board

# AGENT PERSONALITY
# - - - - - - - - - -

# Get an ID badge
export TASKER_AGENT=researcher

# Agent claims and starts work
tasker claim 1
tasker start 1

# Agent completes work
tasker done 1 -m "Found 5 competitors, report in /research/pricing.md"

# Agents communicate
tasker msg writer "Pricing research ready for your blog post" --task 2

# COORDINATOR PERSONALITY
# - - - - - - - - - -

# Check fleet status
tasker fleet

# Activity feed
tasker feed --last 10
```

## Agent Integration

Add to each agent's AGENTS.md or system prompt:
```
Before starting work, run: `tasker inbox --unread`
To get your current work, run: `tasker list --status claimed --mine`
After completing work, run: tasker done <id> -m "what I did"
```

## Commands

| Command | Description |
|---------|-------------|
| `tasker init` | Create database |
| `tasker add "Subject"` | Create task |
| `tasker list` | List tasks |
| `tasker claim <id>` | Claim task |
| `tasker start <id>` | Begin work |
| `tasker done <id>` | Complete task |
| `tasker board` | Kanban view |
| `tasker msg <agent> "body"` | Send message |
| `tasker inbox` | Read messages |
| `tasker fleet` | Agent status |
| `tasker feed` | Activity log |
| `tasker summary` | Fleet overview |

## Architecture

```
┌─────────────────────┐
│    tasker CLI (bash)     │ ← Every agent calls this
├─────────────────────┤
│  SQLite (WAL mode)  │ ← ~/.openclaw/mission-control.db
├─────────────────────┤
│  4 tables:          │
│  - tasks            │ ← Kanban board
│  - messages         │ ← Inter-agent comms
│  - agents           │ ← Fleet registry
│  - activity         │ ← Append-only audit log
└─────────────────────┘
```

## Environment Variables

| Var | Default | Description |
|-----|---------|-------------|
| `TASKER_AGENT` | `$USER` | Agent identity |
| `TASKER_DB` | `~/.openclaw/mission-control.db` | Database path |

## License

MIT
