---
name: create-linear-project
description: Turn a design doc or implementation plan into a Linear project with issues. Use when asked to break a plan or doc into Linear issues, create a Linear project from a doc, or scope a body of work for Linear — including when the ask is only "split this into issues" or "how would we ticket this".
---

# Creating a Linear project from a plan

A plan or design doc says what to build and why. The Linear project turns that into units of work someone can pick up, finish, and close — often somewhere the doc isn't checked out.

## Workflow

1. **Read the source doc end to end.** It's the input to every issue.
2. **Verify the claims the split depends on** against the code, `gh`, and Linear before writing them into issues. Docs go stale, and some claims are inferred rather than checked.
3. **Propose before creating.** Present the project, its issues, what sits outside it, and the decisions still open. Settle what you can by checking; leave only real decisions. Create nothing until asked.
4. **Check for duplicates** — a `list_issues` query per theme in the team.
5. **Read one recent project and one recent issue** in the team, plus its statuses and labels, and match them.
6. **Create, in order:** project → attach the source doc → issues in dependency order (a `blockedBy` target must already exist) → patch the project's Issues section with the new IDs.
7. **Verify:** list the project's issues; check relations on the blocked ones.
8. **Report:** a table of ID, title, repos, status; the defaults you chose for open decisions; unknowns left in issues.

## What an issue is

- **A unit of work: an outcome that is true when it's done.** Not a repo, not a step, not a PR.
- **It spans repos freely.** One issue can carry PRs in several repos; Linear attaches each one.
- **Done means done everywhere.** "Extract module X" covers every consumer switching over and the old copies being deleted, templates included. Don't split it into "create module" / "migrate service A" / "migrate service B": those intermediate states aren't outcomes, and closing one leaves the duplication the work exists to remove.
- **Split when the pieces differ** in done-state, risk or deploy profile, or can be scheduled independently. Extracting a shared auth client (a refactor on the live auth path, medium-risk deploy) and building a matching auth template (a new repo, nothing to deploy) are separate, related issues.
- **Don't bundle unrelated small findings** into one issue to keep the count down. Each gets its own small issue, or none.
- **Order with `blockedBy`**, and say why in the blocked issue's body ("so the release workflow is proven on a low-risk module first").

## In the project, or outside it

The project holds the work that finishes it. It should be completable.

Outside it, in the team backlog, referenced by ID from the project's **Out of scope**:

- **Deferred "wait for X" work.** One issue that names the trigger, with a done-when that is a decision: "each candidate is extracted or explicitly left per repo, with the reason written down". Inside the project it would hold it open indefinitely.
- **Side findings** the doc turned up that aren't the project's goal — dead code, a small within-repo dedupe.

Conditional work ("if we decide to build X now") goes inside the project in Backlog at low priority, where it's cheap to cancel.

## Attach the source doc

Plans and design docs live in local directories (`docs/`, `plans/`) that won't be there when the work happens in another repo, on another machine, or in a cloud session. Attach the source doc to the project as a Linear document: `save_document` with `project`, the full text, the H1 dropped since the document title replaces it, and a first line `> Copied from <path> on <date>.`

Attach it at creation time, before the issues, so the project description links to it from the start. Read it back with `get_document` and confirm the last section arrived.

## Verify before you write it down

Every specific in an issue — a count, a caller, a file, "this is a bug" — gets checked first. From one breakdown:

- A doc's "bug worth fixing" was a shadowed package variable that nothing read: dead code, so the issue became "remove the global", not "fix it".
- "Inferred from the builds, not checked" module visibility was one `gh repo view` away, which settled an open decision.
- A grep counting two functions together read as "one caller each". It was one caller for one function and zero for the other; the issue had to be corrected after creation.
- A repo carried boot scripts but no Dockerfile, so the issue says to find out how it's built before changing it.

When something can't be verified, put it in the issue's **Do** as a first step rather than asserting it.

## Project format

- **Name:** a short noun phrase. **Summary** (≤255 chars): one sentence on what gets built.
- **Status:** Planned on creation.
- **Description:**
  - a context paragraph — why this, why now
  - `Design:` naming the attached doc; `## Plans` listing the plan files' paths when there are plans
  - `## Decisions` — a bold lead-in and its rationale per bullet, including the defaults chosen for open questions
  - `## Issues` — numbered: `ID — **title** (repos)`, plus an ordering note
  - `## Depends on` — blockers in other projects, if any
  - `## Out of scope` — with the IDs of the outside issues

## Issue format

- **Title:** imperative, sentence case, naming the thing — "Extract the Sentry setup into a shared module".
- **Body:**
  - a context paragraph: what's wrong now and why it matters
  - **Do** — the pieces, repo by repo when it spans repos, including what deliberately stays where it is
  - **Done when** — observable outcomes, not tasks: "Sentry events carry each service's own `release`", not "update `Init`"
  - a `Design:` or `Plan:` line naming the section it came from
- **Labels:** the team's existing ones. When a label group is single-select, a multi-repo issue takes its primary label.
- **Status:** Todo for committed work; Backlog for conditional, deferred, and side-finding issues.
- **Priority:** Medium for project work; Low for backlog.
- **Assignee:** none unless asked.

## Linear MCP gotchas

- **Stored markdown is normalised:** `_x_` becomes `*x*`, `~` becomes `\~`, table separators shrink to `--`. A `patch` anchored on the text you sent can fail — `get_project` / `get_issue` and anchor on the stored text.
- **Issue IDs typed in bodies** (`ENG-123`) become links, including in attached documents.
- **Relations are append-only** on save; removing one needs the `remove*` fields.
- **`save_project` truncates the description** in its response; read it back with `get_project`.
