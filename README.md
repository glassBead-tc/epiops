# epiops

A Claude Code plugin marketplace for **operational epistemics**: skills that make Claude's reasoning-in-action more disciplined.

## Plugins

| Plugin | What it does |
|---|---|
| [`ulysses-protocol`](plugins/ulysses-protocol) | Disciplined debugging: predict-probe-update loop, explicit state tracker, precommitted backup probes, two-stall reconsideration, and a minimal fix proven against the original reproducer. |

## Install

From any Claude Code session:

```
/plugin marketplace add glassBead-tc/epiops
/plugin install ulysses-protocol@epiops
```

Or from a shell:

```
claude plugin marketplace add glassBead-tc/epiops
claude plugin install ulysses-protocol@epiops
```

Then restart Claude Code or run `/reload-plugins`. Later, `claude plugin update ulysses-protocol@epiops` pulls new versions.

## Local development

Load a plugin for one session without installing it:

```
claude --plugin-dir ./plugins/ulysses-protocol
```

Validate manifests and skills:

```
claude plugin validate .                          # marketplace manifest
claude plugin validate plugins/ulysses-protocol   # plugin manifest and skills
```

## Layout

```
.claude-plugin/marketplace.json      # the marketplace catalog
plugins/
  ulysses-protocol/
    .claude-plugin/plugin.json       # plugin manifest
    skills/ulysses-protocol/SKILL.md # the skill Claude loads
    README.md
```

## Adding a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` and `plugins/<name>/skills/<skill>/SKILL.md`.
2. Add an entry to `.claude-plugin/marketplace.json` with `"source": "./plugins/<name>"`.
3. Run `claude plugin validate .` and `claude plugin validate plugins/<name>`.
