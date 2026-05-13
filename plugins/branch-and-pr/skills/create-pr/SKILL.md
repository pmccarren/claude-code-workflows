---
name: create-pr
description: Use this skill when opening a GitHub pull request (e.g. via `gh pr create`). Produces Conventional-Commits-esque PR titles and a consistent body. If a Linear (or other tracker) issue ID is known, append it in parens; otherwise omit. Trigger when the user says "open a PR", "create a pull request", "ship this branch", or when another skill/command (e.g. handle-linear-issue) is about to invoke `gh pr create`.
---

# Create PR

Open a pull request with a title that follows Conventional Commits and a body that explains the change.

## Title format

With an issue ID (preferred when one exists):

```
<type>: <title> (<issue-id>)
```

Without an issue ID:

```
<type>: <title>
```

Rules:

1. `<type>` is exactly one Conventional Commits type: `feat`, `fix`, `rf`, `perf`, `docs`, `style`, `test`, `build`, `ci`, `chore`, `revert`. Pick the highest-impact type if the change spans several (`feat` > `fix` > `rf` > others).
2. `<title>` is the human-readable summary in **sentence case** — capital first word, the rest lowercase unless they're proper nouns. NOT the branch slug. NOT all-lowercase like a commit subject.
3. No trailing period.
4. Keep the whole line ≤ 70 characters when practical. Put detail in the body, not the title.
5. Strip tracker-added prefixes from the source title (`[Bug] `, `Spike: `, `[P0] `, …).
6. `<issue-id>` is the tracker ID exactly as the tracker returns it (e.g. `P-688`, `ENG-1421`). Preserve case. Wrap in `( )`.
7. Breaking change → append `!` before the colon (`feat!:`) and document it in the body.

See the `conventional-commits` skill for the full type catalog and breaking-change semantics; PR titles reuse the same vocabulary.

### Title examples

With issue ID:

```
feat: subscription calculated amount (P-688)
fix: oauth refresh race on slow networks (ENG-1421)
rf: extract billing service (WED-93)
chore: bump laravel to 11 (P-1207)
feat!: drop support for legacy webhook payload (P-901)
```

Without issue ID:

```
feat: dark mode toggle
fix: null deref on logout
rf: split user repo
```

### Title anti-patterns

- ❌ `[P-688] feat: subscription calculated amount` — bracketed prefix; the ID goes in trailing parens.
- ❌ `feat: subscription calculated amount [P-688]` — wrong brackets; use `( )`.
- ❌ `feat(P-688): subscription calculated amount` — scope position is for code areas, not issue IDs.
- ❌ `feat: Add subscription calculated amount.` — leading verb `Add` is redundant after `feat`; trailing period.
- ❌ `Feat: Subscription Calculated Amount` — wrong type casing and title-case headline.
- ❌ `feat: subscription-calculated-amount (P-688)` — that's the branch slug, not a title.

## Body format

Required sections, in this order:

```markdown
## Summary

<1–3 short paragraphs or bullets describing what changed and why.>

<Link to the tracker issue, if one exists. e.g. `Linear: P-688` linked to the issue URL.>

## Test plan

- [ ] <how to verify behavior 1>
- [ ] <how to verify behavior 2>
- [ ] <regression check, if relevant>
```

Guidelines:

- **Summary**: lead with *why*, not just *what*. The diff already shows what changed.
- **Tracker link**: include the full URL when available so reviewers can jump straight to context.
- **Test plan**: concrete, checkable steps. "Run X and confirm Y", not "test it".
- Add additional sections (`## Screenshots`, `## Rollout`, `## Risks`, `## Follow-ups`) only when they carry real information.
- Do not paste the entire issue description into the body — link to it.

## Workflow

1. Confirm the branch is pushed (`git push -u origin <branch>`) — `gh pr create` needs a remote branch.
2. Pick the type (same rules as Conventional Commits and the `branch-naming` skill).
3. Draft the title per the format above. Verify it matches the regex `^(feat|fix|rf|perf|docs|style|test|build|ci|chore|revert)!?: .+( \([A-Za-z]+-\d+\))?$`.
4. Draft the body with `## Summary` (+ tracker link) and `## Test plan`.
5. Invoke `gh pr create` with `--title` and `--body`. Pass the body via a heredoc to preserve formatting:

   ```bash
   gh pr create --title "feat: subscription calculated amount (P-688)" --body "$(cat <<'EOF'
   ## Summary

   …

   Linear: [P-688](https://linear.app/…/P-688)

   ## Test plan

   - [ ] …
   EOF
   )"
   ```

6. Return the PR URL to the user.

## Notes

- Do not push or open the PR if the working tree is dirty in unrelated ways — surface that first.
- Do not mark the tracker issue done. That's the user's call.
- For draft PRs, add `--draft`. Default to non-draft unless the user asked otherwise or the change is clearly incomplete.
