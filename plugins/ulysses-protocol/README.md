# Ulysses Protocol

A Claude Code plugin that gives Claude a disciplined debugging methodology: take a reported failure to its causal mechanism, then make and prove the smallest justified fix.

Named for the mast-binding: the protocol precommits Claude to predictions and backup probes *before* it acts, so it cannot drift into unfocused poking.

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

Then restart Claude Code or run `/reload-plugins`.

## Use

The skill is **model-invoked**: once installed, Claude applies it on its own when you report a bug, failing test, crash, or regression and ask for it to be diagnosed.

You can also invoke it explicitly, optionally with a problem statement:

```
/ulysses-protocol:ulysses-protocol the login test fails on CI but passes locally
```

## What it does

Claude keeps an explicit state tracker and moves through a small state machine:

| State | Meaning |
|---|---|
| `0` PLAN | Pick the smallest probe that separates the current explanation from a plausible alternative. Write down the prediction, the check, and a backup probe. |
| `1` | Primary probe executed. Observe, judge validity and meaning, record progress. |
| `2` BACKUP | The primary stalled. Run the precommitted backup. Replanning does not erase the stall. |
| `-1` CONSIDERATION | Two stalls. Return to the evidence, challenge an assumption or system boundary, then plan a new pair. |

Before declaring resolution, Claude must explain the chain **triggering input → defective behavior → observed failure**, make the smallest justified fix, show the original reproducer failing before and passing after, and check nearby cases for regressions.

**Predict → Probe → Update. Two stalls → Reconsider. Explain → Verify.**

The full instructions live in [`skills/ulysses-protocol/SKILL.md`](skills/ulysses-protocol/SKILL.md).

## Develop

```
claude plugin validate .                 # from this directory
claude --plugin-dir ./plugins/ulysses-protocol   # from the repo root: load without installing
```
