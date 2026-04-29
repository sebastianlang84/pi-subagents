# pi-subagents

Pi extension for delegating work to specialized subagents with isolated context windows.

This package exposes a `subagent` tool that can run agents in three modes:

- **single**: run one agent on one task
- **parallel**: run multiple agents concurrently
- **chain**: run agents sequentially and pass `{previous}` output forward

## Install

From GitHub:

```bash
pi install git:github.com/sebastianlang84/pi-ext-subagents
```

For local development:

```bash
pi -e ~/dev/pi-extensions/pi-ext-subagents
```

## Agent files

Global user agents live in:

```text
~/.pi/agent/agents/
```

Project-local agents live in:

```text
.pi/agents/
```

Each agent is a Markdown file with frontmatter:

```md
---
name: reviewer
description: Reviews code changes for correctness and risks
tools: read,grep,bash
model: anthropic/claude-sonnet-4-5
---
You are a careful code reviewer. Focus on correctness, edge cases, and concise findings.
```

## Example usage

Single agent:

```json
{
  "agent": "reviewer",
  "task": "Review the current changes and list risks."
}
```

Parallel agents:

```json
{
  "tasks": [
    { "agent": "reviewer", "task": "Review correctness." },
    { "agent": "tester", "task": "Suggest focused tests." }
  ]
}
```

Chain:

```json
{
  "chain": [
    { "agent": "researcher", "task": "Investigate the issue." },
    { "agent": "writer", "task": "Summarize this for the user: {previous}" }
  ]
}
```

## Notes

- By default, the tool loads global agents from `~/.pi/agent/agents`.
- Set `agentScope` to `project` or `both` to use project-local agents.
- Project-local agents require confirmation by default because they are repo-controlled.
