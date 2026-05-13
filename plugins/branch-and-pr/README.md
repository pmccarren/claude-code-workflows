# branch-and-pr

A Claude Code plugin with two skills for the front and back of a git workflow: picking a branch name, then opening the pull request. Both reuse the [Conventional Commits](https://www.conventionalcommits.org) vocabulary so the branch, the eventual squash commit, and the PR title all line up.

## What it provides

| Skill | Triggers on |
| --- | --- |
| [`branch-naming`](skills/branch-naming/SKILL.md) | Requests like "make a branch", "start a branch for X", or another skill about to call `git checkout -b`. |
| [`create-pr`](skills/create-pr/SKILL.md) | Requests like "open a PR", "create a pull request", "ship this branch", or another skill about to call `gh pr create`. |

## Format at a glance

Branch name (issue ID first when one exists):

```
<issue-id>/<type>/<slug>
<type>/<slug>
```

PR title (issue ID in trailing parens when one exists):

```
<type>: <title> (<issue-id>)
<type>: <title>
```

`<type>` is drawn from the same set as Conventional Commits: `feat`, `fix`, `rf`, `perf`, `docs`, `style`, `test`, `build`, `ci`, `chore`, `revert`. See the individual skill files for the full rules, examples, and anti-patterns.

## Install

Install via Claude Code's plugin system — for example, add this repository as a marketplace and install `branch-and-pr`, or symlink the plugin directory into `~/.claude/plugins/`.

## Layout

```
plugins/branch-and-pr/
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── skills/
    ├── branch-naming/
    │   └── SKILL.md
    └── create-pr/
        └── SKILL.md
```
