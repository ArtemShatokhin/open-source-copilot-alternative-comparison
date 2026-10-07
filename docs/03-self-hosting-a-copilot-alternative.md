# Self-Hosting a Copilot Alternative

Microsoft Copilot cannot be self-hosted. Kortix can: it is the open-source AI Operating System, and it runs on your own machines, in your VPC or on-prem, or on the managed cloud. This guide is the shortest path from nothing to a running open-source agent system.

## 1. Install the CLI and scaffold a project

```bash
curl -fsSL https://kortix.com/install | bash
kortix init
```

`kortix init` creates `kortix.yaml` plus your agents, skills and runtime config. That repository is the company: agents and skills are Markdown, memory is files that accumulate, and `kortix.yaml` declares the machine image, the connectors and the triggers.

## 2. Self-host on your own box

```bash
kortix self-host start   # one Docker Compose stack on your own machine
```

Run it on a laptop, a VPS, your VPC or an on-prem network. Secrets are encrypted at rest, and connector credentials are brokered server-side — they never enter the agent's machine.

## 3. Ship and start a session

```bash
kortix ship
kortix sessions new --prompt "Summarize this week's commits and open a change request"
```

Every session gets its own isolated cloud computer — a disposable Linux sandbox on a branch named after the session. The agent can install, run and break anything; only what it commits survives. Work reaches the default branch only through a change request you review:

```bash
kortix cr ls   # review what an agent proposes, then merge to keep it
```

## 4. Choose your models, keep your keys

Kortix is model-agnostic. Pick the model per agent, per session or per message, and bring an API key from any major provider — or point it at your own OpenAI-compatible endpoint. Nothing here is tied to one lab's cloud.

## Why this is the Copilot alternative that lasts

Self-hosting is not a fallback here; it is the point. You get the code, the configuration and the choice of infrastructure, and you can fork the whole system if you need to. Start at [kortix.com](https://kortix.com), or read the [detailed comparison](02-platform-comparison.md) first.
