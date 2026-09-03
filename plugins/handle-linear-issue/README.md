# handle-linear-issue

A Claude Code plugin with a single slash command that takes a Linear issue from "fetch it" to "PR is open", in one pass.

## What it provides

| Command | Usage |
| --- | --- |
| [`/handle-linear-issue`](commands/handle-linear-issue.md) | `/handle-linear-issue T-688` — fetch the issue, branch, implement, commit, open the PR. |

The command deliberately does **not** spell out branch or PR naming. Those steps hand off to the [`branch-and-pr`](../branch-and-pr) skills (`branch-naming`, `create-pr`), so the issue ID, branch, squash commit, and PR title all line up.

## Requirements

- A Linear MCP server, for the `get_issue` call in step 1. The command accepts either the `linear` plugin's server (`mcp__plugin_linear_linear__*`) or Linear connected as a claude.ai connector (`mcp__claude_ai_Linear__*`).
- `gh` on `PATH` for opening the PR.
- The [`branch-and-pr`](../branch-and-pr) plugin, for the naming steps.

## Guardrails

- Empty argument → asks for the issue ID instead of guessing.
- Under-specified issue → asks clarifying questions before writing code.
- Dirty working tree → surfaces it before branching or stashing.
- Never marks the Linear issue done, and pauses before any force push or other shared-state change.

## Install

Install via Claude Code's plugin system — for example, add this repository as a marketplace and install `handle-linear-issue`, or symlink the plugin directory into `~/.claude/plugins/`.

## Layout

```
plugins/handle-linear-issue/
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── commands/
    └── handle-linear-issue.md
```
