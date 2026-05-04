# Extended examples

Worked examples beyond the spec, covering common real-world cases. Use the [spec examples](spec-examples.md) as the source of truth when these conflict.

## Bug fix with issue link

```
fix(auth): reject expired refresh tokens

The refresh handler was treating tokens past their `exp` claim as
valid because we compared against `iat` instead of `exp`.

Fixes: #841
```

## Feature with co-author

```
feat(billing): add proration for mid-cycle plan changes

Co-authored-by: Alex Rivera <alex@example.com>
```

## Performance improvement

```
perf(search): cache tokenizer output across queries

Reduces p95 search latency from 180ms to 60ms on the staging dataset.
```

## Pure refactor (no behavior change)

```
rf(parser): extract token-stream helper

No behavior change; preparing for the streaming-parse work in #902.
```

## Documentation only

```
docs: clarify retry semantics in client README
```

## Build / dependency bump

```
build(deps): bump axios from 1.6.7 to 1.7.2
```

## CI configuration

```
ci: run integration suite on macOS runners
```

## Test additions

```
test(api): cover 429 backoff path in rate-limit suite
```

## Chore with breaking change

A `chore!:` still triggers a MAJOR SemVer bump because of the `!`.

```
chore!: drop support for Node 16

BREAKING CHANGE: minimum supported Node version is now 18.
```

## Revert

```
revert: feat(api): allow provided config object to extend other configs

This reverts commit 676104ed.
Refs: #1024
```

## Scope spanning multiple areas

When a change crosses a couple of related areas, pick the dominant one and mention the rest in the body — don't invent multi-scope syntax.

```
feat(api): paginate /events responses

Also updates the TypeScript client to surface the new `nextCursor`
field. Web dashboard work tracked separately in #1130.
```

## Security fix

```
fix(auth)!: rotate signing key on every login

BREAKING CHANGE: existing sessions are invalidated on deploy. Clients
must re-authenticate. CVE-2026-12345.
```
