# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Core logic

The entire implementation is four lines in `chart/templates/templates.yaml`:

```yaml
{{ range .Values.templates }}
---
{{ tpl (toYaml . | replace "<<" "{{" | replace ">>" "}}") $ }}
{{ end }}
```

Each entry in `templates` is serialized back to YAML, then `<<`/`>>` are substituted for `{{`/`}}` before being evaluated by `tpl`. This indirection exists because raw `{{ }}` inside YAML string values requires awkward quoting; `<<`/`>>` is the user-facing template delimiter.

`tpl` receives `$` (the root context), so expressions inside `<<`/`>>` have full access to `.Values`, `include`, and all standard Helm template functions.

`templates` accepts either an array of objects or a map (see `chart/values.schema.json`).

## Development commands

Render output:
```bash
helm template <release-name> ./chart -f <values-file>
```

Lint and validate schema:
```bash
helm lint ./chart -f <values-file>
```
