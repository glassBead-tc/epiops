# Ulysses Protocol — Agent Instructions

Investigate the reported failure, identify its causal mechanism, and verify the smallest justified fix. Keep every probe tied to the original failure.

Assume no other actor changes the codebase during the investigation. Preserve the starting state and track your experimental changes.

**Maintain an explicit state tracker**

Keep this compact record in your working notes:

```text
stateStep: 0 | 1 | 2 | -1
problem: triggering input, expected behavior, actual failure
checkpoint: known code state and accumulated evidence
hypotheses: current explanation and plausible alternative
primary: action, evaluation, prediction, disconfirmation
backup: action, evaluation, prediction, disconfirmation
lastObservation: actual result and what it changed
```

Initialize `stateStep = 0`.

The tracker represents the investigation phase, not the number of code edits. Read-only probes count. A checkpoint is a known state; it need not be bug-free.

**State `0`: PLAN**

Identify the unresolved question about the original failure.

Choose the smallest primary probe that could distinguish your current explanation from a plausible alternative. Before acting, specify:

- The action or small bundle of actions answering one diagnostic question.
- The executable check or exact observation used to evaluate it.
- Your prediction and what would count against your explanation.

Precommit a backup probe for the case where the primary cannot run or leaves the question unresolved. The backup seeks information.

Set `stateStep = 1` and execute the primary.

**After every probe: OBSERVE AND UPDATE**

Record the actual result. Answer three questions:

1. **Validity:** Did the probe produce trustworthy evidence?
2. **Meaning:** What does the evidence support, weaken, or rule out?
3. **Progress:** What changed in the diagnosis of the original failure?

Useful progress means new evidence materially revises a causal explanation, narrows the failing boundary, or establishes part of the causal chain. Successful execution or additional logging alone does not qualify.

Update your hypotheses immediately. **A valid result that disproves your prediction can be progress.** An invalid experiment gives no verdict on the application hypothesis.

Apply these transitions:

| Current state | Result | Transition |
|---|---|---|
| `1` or `2` | Useful diagnostic progress | Preserve evidence and useful code state; return to `0`. |
| `1` | No useful diagnostic progress | Record one stall; move to `2`. |
| `2` | No useful diagnostic progress | Record the second stall; move to `-1`. |

**State `2`: BACKUP**

Check whether the precommitted backup remains justified.

If it does, execute it. Otherwise, replace it with a better probe and record its evaluation and prediction before execution.

**Remain at `2`. Replanning, revising a prediction, or changing tools does not erase the first stall.**

Evaluate the result using the same rules above. Do not bundle unrelated probes to conceal stalls.

**State `-1`: CONSIDERATION**

Return to the original failure and accumulated evidence. State why the two probes failed to advance the diagnosis.

Make a substantive change: challenge an assumption, examine a different causal mechanism or system boundary, or correct an invalid observation method. Cosmetic variations of the same unsuccessful approach are insufficient.

Restore experimental code to a known state when needed. **Keep the evidence and rejected explanations.** Repeat an earlier probe only when changed conditions or a specific uncertainty justify it.

Record the revised approach, then set `stateStep = 0` and plan a new pair.

**Completion**

Before declaring resolution:

1. Explain the chain: **triggering input → defective behavior → observed failure**.
2. Make the smallest justified fix.
3. Demonstrate that the original reproducer fails before the fix and passes afterward.
4. Check relevant nearby cases for regressions.

If time expires or no informative next probe is available, state what is established, what remains uncertain, and the next discriminating test. Report why work stopped without claiming resolution.

Keep state updates brief:

```text
Ulysses 1 → 0
Prediction: ...
Observation: ...
Learned: ...
Next question: ...
```

**Predict → Probe → Update. Two stalls → Reconsider. Explain → Verify.**