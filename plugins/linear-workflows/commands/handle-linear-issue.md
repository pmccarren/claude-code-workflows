---
description: Work on a Linear issue and open a PR. Branch and PR naming are delegated to the branch-naming and create-pr skills.
argument-hint: <linear-issue-id>
---

You are being asked to work on Linear issue `$ARGUMENTS` end-to-end.

## Steps

1. **Fetch the issue** with the Linear MCP `get_issue` tool (`mcp__plugin_linear_linear__get_issue`, or `mcp__claude_ai_Linear__get_issue` if Linear is connected as a claude.ai connector) using `$ARGUMENTS` as the ID. Read the title, description, and comments to understand scope. Preserve the issue ID exactly as Linear returns it (e.g. `T-688`).

2. **Set the status to In Progress** with the Linear MCP `save_issue` tool (`state: "In Progress"`). Do this from Triage, Backlog, or Todo, and from In Review when you pick the issue back up to change its PR. If the issue is Done, Canceled, or in a stage after In Review, ask before changing it. If the team has no In Progress status, skip this step and say so.

3. **Create a branch** off the current base (usually `main`). The Linear issue ID from step 1 is the `<issue-id>`.

4. **Implement the work.** Follow project conventions in `CLAUDE.md` and `.claude/rules/`. Run repo tooling, e.g. tests and linting before commiting. Fix failures — don't commit broken code. Ask clarifying questions if you have any.

5. **Commit**

6. **Open the PR**

7. **Set the status to In Review** (`state: "In Review"`) once the PR is open, so the issue shows as ready for review. Repeat this for every later round of changes on the PR: In Progress while you work, then In Review again once the changes are pushed. If the team has no In Review status, skip this step and say so.

8. **Pause for confirmation** before any destructive or shared-state action (force push, closing issues, marking Linear done, etc.). Do not mark the Linear issue done — leave that to the user.

## Notes

- If `$ARGUMENTS` is empty, ask for the Linear issue ID before proceeding.
- If the issue is ambiguous or under-specified, ask clarifying questions before writing code.
- If the working tree is dirty, surface that and ask before stashing or branching.
