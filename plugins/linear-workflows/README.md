# linear-workflows

A Claude Code plugin for working with Linear: planning a body of work into a project, and taking a single issue from "fetch it" to "PR is open".

## What it provides

| Kind | Name | Usage |
| --- | --- | --- |
| Skill | [`create-linear-project`](skills/create-linear-project/SKILL.md) | Triggers on requests like "turn this plan into a Linear project", "split this doc into issues", or "how would we ticket this". Proposes the project and its issues, then creates them only once asked. |
| Command | [`/handle-linear-issue`](commands/handle-linear-issue.md) | `/handle-linear-issue T-688` (namespaced: `/linear-workflows:handle-linear-issue`) — fetch the issue, branch, implement, commit, open the PR. |

### `create-linear-project`

Turns a design doc or implementation plan into a Linear project with issues:

- Verifies the doc's claims against the code, `gh`, and Linear before writing them into issues.
- Proposes the project, its issues, what sits outside it, and the open decisions — and creates nothing until asked.
- Matches the team's existing projects, issues, statuses, and labels, and checks for duplicates.
- Attaches the source doc to the project as a Linear document, so the plan travels with the work.
- Creates issues in dependency order with `blockedBy` relations, then reports a table of what it made.

### `/handle-linear-issue`

The command deliberately does **not** spell out branch or PR naming. Those steps hand off to the [`branch-and-pr`](../branch-and-pr) skills (`branch-naming`, `create-pr`), so the issue ID, branch, squash commit, and PR title all line up.

Guardrails:

- Empty argument → asks for the issue ID instead of guessing.
- Under-specified issue → asks clarifying questions before writing code.
- Dirty working tree → surfaces it before branching or stashing.
- Never marks the Linear issue done, and pauses before any force push or other shared-state change.

## Requirements

- A Linear MCP server. Either the `linear` plugin's server (`mcp__plugin_linear_linear__*`) or Linear connected as a claude.ai connector (`mcp__claude_ai_Linear__*`) works.
- `gh` on `PATH`, for opening PRs and for verifying claims while planning.
- The [`branch-and-pr`](../branch-and-pr) plugin, for the naming steps in `/handle-linear-issue`.

## Install

Install via Claude Code's plugin system — for example, add this repository as a marketplace and install `linear-workflows`, or symlink the plugin directory into `~/.claude/plugins/`.

This plugin replaces the earlier `handle-linear-issue` plugin; uninstall that one when switching over.

## Layout

```
plugins/linear-workflows/
├── .claude-plugin/
│   └── plugin.json
├── README.md
├── commands/
│   └── handle-linear-issue.md
└── skills/
    └── create-linear-project/
        └── SKILL.md
```
