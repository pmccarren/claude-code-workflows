---
description: Work on a Linear issue and open a PR. Branch and PR naming are delegated to the branch-naming and create-pr skills.
argument-hint: <linear-issue-id>
---

You are being asked to work on Linear issue `$ARGUMENTS` end-to-end.

## Steps

1. **Fetch the issue** with the Linear MCP `get_issue` tool (`mcp__plugin_linear_linear__get_issue`, or `mcp__claude_ai_Linear__get_issue` if Linear is connected as a claude.ai connector) using `$ARGUMENTS` as the ID. Read the title, description, and comments to understand scope. Preserve the issue ID exactly as Linear returns it (e.g. `T-688`). Mark the issue as In Progress only if it is currently TODO.

2. **Create a branch** off the current base (usually `main`). The Linear issue ID from step 1 is the `<issue-id>`.

3. **Implement the work.** Follow project conventions in `CLAUDE.md` and `.claude/rules/`. Run repo tooling, e.g. tests and linting before commiting. Fix failures — don't commit broken code. Ask clarifying questions if you have any.

4. **Commit**

5. **Open the PR**

6. **Pause for confirmation** before any destructive or shared-state action (force push, closing issues, marking Linear done, etc.). Do not mark the Linear issue done — leave that to the user.

## Notes

- If `$ARGUMENTS` is empty, ask for the Linear issue ID before proceeding.
- If the issue is ambiguous or under-specified, ask clarifying questions before writing code.
- If the working tree is dirty, surface that and ask before stashing or branching.
