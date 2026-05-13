---
name: branch-naming
description: Use this skill when creating a new git branch. Produces Conventional-Commits-esque branch names. If a Linear (or other tracker) issue ID is known, the name starts with it; otherwise the issue-id segment is omitted. Trigger when the user says "make a branch", "start a branch for X", "branch off main for…", or when another skill/command (e.g. handle-linear-issue) is about to call `git checkout -b`.
---

# Branch Naming

Pick a branch name that encodes the issue, the kind of change, and a short slug — in that order.

## Format

With an issue ID (preferred when one exists):

```
<issue-id>/<type>/<slug>
```

Without an issue ID:

```
<type>/<slug>
```

Segments are always joined with `/` — never `-`, `_`, or nothing. The issue ID, when one exists, is always the **first segment, on its own**, followed by `/`. Do not invent an issue ID if there isn't one — drop the segment instead.

Concrete:

- Issue ID `P-123`, feature: `P-123/feat/new-feature`
- No issue ID, feature: `feat/new-feature`

## Segments

### `<issue-id>`

Copy the tracker ID **verbatim** as the first segment — same case, same prefix, same internal dash. Do not lowercase it. Do not drop or rename the prefix. Do not collapse, remove, or substitute the dash inside it. Whatever the tracker prints is what goes on the branch.

- Linear `P-688` → branch starts `P-688/…` (not `p-688/…`, not `P688/…`, not `P_688/…`)
- Linear `ENG-1421` → branch starts `ENG-1421/…` (not `eng-1421/…`)
- GitHub `gh-482` → branch starts `gh-482/…` (lowercase here is verbatim — preserve whatever the tracker uses)
- Jira `WED-93` → branch starts `WED-93/…`

The `/` after the issue ID is required — it separates the issue-id segment from the type segment. The issue ID is never glued to the type with `-` or `_`, and never embedded inside the slug.

If the user gives you the issue by URL or title, extract the ID first (e.g. `https://linear.app/foo/issue/P-688/...` → `P-688`). If no issue exists, drop the segment entirely; do not invent one and do not leave an empty `/`.

### `<type>`

Use the same prefixes as Conventional Commits. Pick exactly one — the type of the highest-impact change in the branch:

- `feat` — new feature or capability
- `fix` — bug fix
- `rf` — refactor with no behavior change
- `perf` — performance improvement with no behavior change
- `docs` — documentation only
- `test` — adding or correcting tests
- `build` — build system or dependencies
- `ci` — CI configuration
- `chore` — maintenance that doesn't fit the above
- `revert` — reverting a prior change

If torn between `feat` and `rf`: ask whether a user-visible capability changes. Yes → `feat`. No → `rf`.

See the `conventional-commits` skill for the full type catalog and semantics; this skill reuses the same vocabulary.

### `<slug>`

A short, lowercase, hyphenated summary of the work — not the full issue title.

Rules:

1. Lowercase everything.
2. Replace spaces and punctuation with `-`.
3. Collapse repeated `-` and trim leading/trailing `-`.
4. Drop noise words (`the`, `a`, `an`) when it helps brevity.
5. Strip any tracker-added prefixes from the source title (`[Bug] `, `Spike: `, `[P0] `, …).
6. Target ≤ 6 words / ~40 chars. Shorter is better.
7. ASCII only. No slashes inside the slug — `/` is the segment separator.

## Examples

With issue ID:

```
P-688/feat/subscription-calculated-amount
ENG-1421/fix/oauth-refresh-race
WED-93/rf/extract-billing-service
P-1207/chore/bump-laravel-11
```

Without issue ID:

```
feat/dark-mode-toggle
fix/null-deref-on-logout
rf/split-user-repo
```

## Anti-patterns

- ❌ `feat/P-688-subscription` — issue ID belongs in its own segment, first.
- ❌ `P-688/Subscription_Calculated_Amount` — wrong case, wrong separator.
- ❌ `p-688/feat/subscription-calculated-amount` — issue ID case must match the tracker.
- ❌ `P-688/feature/add-subscription-calculated-amount-field-to-the-billing-table` — wrong type token (`feature` vs `feat`), slug too long, redundant verbs.
- ❌ `P-688/feat/` — empty slug.
- ❌ `feat//dark-mode` — empty issue-id segment; drop the segment entirely if you don't have one.

## Workflow

1. Determine whether an issue ID exists. If yes, copy it verbatim from the tracker — same case, same prefix, same dash.
2. Pick the type from the Conventional Commits vocabulary above.
3. Slugify the source title per the rules.
4. Assemble `<issue-id>/<type>/<slug>` or `<type>/<slug>`.
5. Verify against the regex `^([A-Za-z]+-\d+/)?(feat|fix|rf|perf|docs|style|test|build|ci|chore|revert)/[a-z0-9]+(-[a-z0-9]+)*$` before running `git checkout -b`.
