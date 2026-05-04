# Spec examples

The official examples from the [Conventional Commits v1.0.0 spec](https://www.conventionalcommits.org/en/v1.0.0/#examples), reproduced verbatim. Use these as the canonical reference when in doubt about formatting.

## Commit message with description and breaking change footer

```
feat: allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

## Commit message with `!` to draw attention to breaking change

```
feat!: send an email to the customer when a product is shipped
```

## Commit message with scope and `!` to draw attention to breaking change

```
feat(api)!: send an email to the customer when a product is shipped
```

## Commit message with both `!` and BREAKING CHANGE footer

```
feat!: drop support for Node 6

BREAKING CHANGE: use JavaScript features not available in Node 6.
```

## Commit message with no body

```
docs: correct spelling of CHANGELOG
```

## Commit message with scope

```
feat(lang): add Polish language
```

## Commit message with multi-paragraph body and multiple footers

```
fix: prevent racing of requests

Introduce a request id and a reference to latest request. Dismiss
incoming responses other than from latest request.

Remove timeouts which were used to mitigate the racing issue but are
obsolete now.

Reviewed-by: Z
Refs: #123
```
