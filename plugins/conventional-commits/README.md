# conventional-commits

A Claude Code plugin that helps you write git commit messages conforming to the [Conventional Commits](https://www.conventionalcommits.org) specification.

## What it provides

| Skill | Triggers on |
| --- | --- |
| [`conventional-commits`](skills/conventional-commits/SKILL.md) | Requests to create a commit, draft a commit message, or check that a message conforms to Conventional Commits. |

When the skill activates, Claude will:

1. Read the staged diff (`git diff --cached`) — falling back to `git status` / `git diff` if nothing is staged.
2. Pick the right type (`feat`, `fix`, `refactor`, …) and an optional scope.
3. Write an imperative-mood description, plus a body and footers when warranted.
4. Flag breaking changes with `!` and/or a `BREAKING CHANGE:` footer.

## Format reference

```
<type>[optional scope][!]: <description>

[optional body]

[optional footer(s)]
```

Examples:

```
fix: prevent racing of requests
feat(lang): add Polish language
feat!: send an email to the customer when a product is shipped
chore!: drop support for Node 6
```

See the full type list, footer conventions, and worked examples in the [skill body](skills/conventional-commits/SKILL.md).

## Install

Install via Claude Code's plugin system — for example, add this repository as a marketplace and install `conventional-commits`, or symlink the plugin directory into `~/.claude/plugins/`.

## Layout

```
plugins/conventional-commits/
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── skills/
    └── conventional-commits/
        └── SKILL.md
```
