# linear-workflows

A Claude Code plugin for working with Linear: planning a body of work into a project, taking a single issue from "fetch it" to "PR is open", and orchestrating an issue through an agent implementer and reviewer until it's ready for human review.

## What it provides

| Kind | Name | Usage |
| --- | --- | --- |
| Skill | [`create-linear-project`](skills/create-linear-project/SKILL.md) | Triggers on requests like "turn this plan into a Linear project", "split this doc into issues", or "how would we ticket this". Proposes the project and its issues, then creates them only once asked. |
| Command | [`/handle-linear-issue`](commands/handle-linear-issue.md) | `/handle-linear-issue T-688` (namespaced: `/linear-workflows:handle-linear-issue`) — fetch the issue, branch, implement, commit, open the PR. |
| Skill | [`/orchestrate-linear-issue`](skills/orchestrate-linear-issue/SKILL.md) | `/orchestrate-linear-issue T-688 [extra guidance]` (namespaced: `/linear-workflows:orchestrate-linear-issue`) — an implementer runs `/handle-linear-issue`, a reviewer reviews the PR(s), and they iterate until both are satisfied. Slash-only: Claude never starts it on its own. |

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

Linear status:

- **In Progress** when work starts, including when the issue comes back from In Review for changes. Asks first if the issue is Done, Canceled, or past review.
- **In Review** whenever the PR is ready for review: once it opens, and again after each later round of changes is pushed.
- Skipped, with a note, on teams that don't have a status by that name.

### `/orchestrate-linear-issue`

The session you start it in is the orchestrator. It keeps its context small and starts two teammates:

1. **`impl-<id>`** runs `/handle-linear-issue` and opens the PR(s). When the session isn't in a git repo, it treats the directory as a folder of repos and opens one PR per repo the issue touches.
2. **`review-<id>`** starts once the PRs are open. It reviews them against the issue, the repo's `CLAUDE.md` and rules, correctness, security, and tests, then sends numbered findings straight to the implementer.
3. The two iterate: the implementer fixes or pushes back, pushes new commits, and the reviewer re-checks. Disagreements get one pushback and then go to you, and there are at most 3 rounds.
4. When nothing blocking is open, the orchestrator moves the issue to In Review and hands off. You get the PR links and a short summary of what was fixed, declined, left unresolved, or needs checking by hand.

Notes:

- The issue stays In Progress during the agent review, so In Review in Linear means ready for *your* review.
- Teammates can't ask you questions directly. Their questions reach you through the orchestrator.
- The reviewer is read-only: it never edits, commits, or posts to GitHub or Linear.
- Both teammates stay around after hand-off. PR feedback you give the orchestrator goes to the implementer, who still has full context.
- The teammate briefs, [`implementer.md`](skills/orchestrate-linear-issue/implementer.md) and [`reviewer.md`](skills/orchestrate-linear-issue/reviewer.md), are read by the teammates themselves, never by the orchestrator.

## Requirements

- A Linear MCP server. Either the `linear` plugin's server (`mcp__plugin_linear_linear__*`) or Linear connected as a claude.ai connector (`mcp__claude_ai_Linear__*`) works.
- `gh` on `PATH`, for opening PRs and for verifying claims while planning.
- The [`branch-and-pr`](../branch-and-pr) plugin, for the naming steps in `/handle-linear-issue`.
- For `/orchestrate-linear-issue`: Claude Code agent teams, enabled with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` (shell env or `env` in `settings.json`). The teammates message each other directly.

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
    ├── create-linear-project/
    │   └── SKILL.md
    └── orchestrate-linear-issue/
        ├── SKILL.md
        ├── implementer.md
        └── reviewer.md
```
