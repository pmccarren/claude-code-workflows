---
name: conventional-commits
version: 1.0.0
description: This skill should be used when creating Git commits to ensure they follow the Conventional Commits specification. It provides guidance on commit message structure, types, scopes, and best practices for writing clear, consistent, and automated-friendly commit messages. Use when committing code changes or reviewing commit history.
---

# Conventional Commits

Produce commit messages that conform to the Conventional Commits spec. The format makes commit history machine-readable so it can drive SemVer bumps, changelogs, and release tooling.

## Format

```
<type>[optional scope][!]: <description>

[optional body]

[optional footer(s)]
```

- **type** — required noun describing the kind of change (see below).
- **scope** — optional noun in parentheses naming the affected area, e.g. `feat(parser):`.
- **!** — optional marker before the colon to flag a breaking change, e.g. `feat!:` or `feat(api)!:`.
- **description** — required short summary on the same line as the type, immediately after `: `.
- **body** — optional free-form paragraphs explaining *why* and *what*. Separated from the description by one blank line.
- **footers** — optional `Token: value` or `Token #value` lines, one blank line after the body. Tokens use `-` instead of spaces (e.g. `Reviewed-by`). The single exception is `BREAKING CHANGE`.

## Types

The spec mandates two; the rest come from the Angular convention referenced by the spec:

- `feat` — a new feature (MINOR in SemVer).
- `fix` — a bug fix (PATCH in SemVer).
- `docs` — documentation only.
- `style` — formatting, whitespace, semicolons; no code-behavior change.
- `rf` — code change that neither fixes a bug nor adds a feature.
- `perf` — performance improvement.
- `test` — adding or correcting tests.
- `build` — build system or external dependencies (npm, cargo, gradle, …).
- `ci` — CI configuration and scripts.
- `chore` — other maintenance that doesn't fit above.
- `revert` — reverts a previous commit; body should reference the reverted SHA.

If unsure between `feat` and `refactor`: ask whether a user-visible capability changed. If yes, `feat`. If no, `refactor`.

## Breaking changes

Indicate a breaking change in **either** of these ways (you may use both):

1. Append `!` before the colon: `feat(api)!: drop support for Node 16`.
2. Add a `BREAKING CHANGE:` footer:
   ```
   BREAKING CHANGE: `--legacy-flag` has been removed; use `--mode=legacy` instead.
   ```

When `!` is used, the description itself should describe the break; the footer becomes optional. A breaking change triggers a MAJOR SemVer bump regardless of type — even `chore!:` or `refactor!:`.

## Rules (from the spec)

1. The prefix is `type`, optional `(scope)`, optional `!`, then `: ` (colon + space) — exactly.
2. `feat` MUST be used for new features; `fix` MUST be used for bug fixes.
3. Scope is a noun in parentheses describing a section of the codebase.
4. The description follows the `: ` on the same line.
5. The body, if present, starts one blank line after the description.
6. Footers, if present, start one blank line after the body. Each is `Token: value` or `Token #value`.
7. Footer tokens use `-` for spaces (`Co-authored-by`, `Reviewed-by`). `BREAKING CHANGE` is the one allowed exception with a space.
8. Breaking changes are signaled by `!` in the prefix and/or a `BREAKING CHANGE: …` footer.
9. Types other than `feat` and `fix` are allowed and MUST NOT influence SemVer unless they include a `!` or `BREAKING CHANGE` footer.
10. The units of the type and the `BREAKING CHANGE` token are case-insensitive in tooling, but convention is lowercase types and uppercase `BREAKING CHANGE`.

## Description style

- Imperative mood: "add", "fix", "remove" — not "added" or "adds".
- Lowercase first letter; no trailing period.
- Keep the subject line ≤ ~72 characters when practical.
- Describe the change, not the file. `fix: prevent race in token refresh` beats `fix: update auth.ts`.

## Common footers

- `BREAKING CHANGE: <description>` — the breaking-change footer.
- `Refs: <issue-id>`, `Closes: <issue-id>`, `Fixes: <issue-id>` — issue links.
- `Co-authored-by: Name <email>` — additional authors.
- `Reviewed-by: Name <email>` — reviewers.
- `Signed-off-by: Name <email>` — DCO sign-off.

## Examples

Simple fix:
```
fix: prevent racing of requests
```

Feature with scope:
```
feat(lang): add Polish language
```

Breaking change via `!`:
```
feat!: send an email to the customer when a product is shipped
```

Breaking change via footer, with body and issue link:
```
feat(api): allow provided config object to extend other configs

Configs are now merged shallowly with user values taking precedence.
This unblocks the multi-tenant deployment story.

BREAKING CHANGE: `extends` is no longer a reserved key in config files.
Refs: #482
```

Revert:
```
revert: let us never again speak of the noodle incident

Refs: 676104e, a215868
```

## References

The `references/` directory next to this file contains additional examples to consult when drafting messages:

- [`references/spec-examples.md`](references/spec-examples.md) — The canonical reference of Conventional Commits.
- [`references/extended-examples.md`](references/extended-examples.md) — worked examples for common real-world cases (fixes with issue links, perf, refactor, build, ci, revert, security, etc.).

When unsure how to format a particular case, read these before guessing.

## Workflow

When writing a commit message:

1. Read the staged diff (`git diff --cached`) to see what actually changed. If nothing is staged, check `git status` and `git diff`.
2. Pick the single best type. If a change spans types, prefer the type of the highest-impact change (`feat` > `fix` > `refactor` > others) and mention the rest in the body.
3. Pick a scope only if there's an obvious one — a package, module, or subsystem. Don't invent scopes.
4. Write the description in imperative mood, focused on the *effect* of the change.
5. Add a body only when the *why* isn't obvious from the diff or when the change is large enough to need context.
6. Add `BREAKING CHANGE:` (and/or `!`) when the public API, CLI, config, or on-disk format changes incompatibly.
7. Verify the prefix matches the regex `^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert)(\([^)]+\))?!?: .+` before finalizing.
