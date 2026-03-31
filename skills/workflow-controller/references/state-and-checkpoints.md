# State and Checkpoints

## Durable state model

Long-running workflows need persisted state outside the coding-agent call itself.

At minimum, persist:

- workflow identity
- workflow topology state
- per-item or per-step progress
- references to produced artifacts
- failure state
- last successful checkpoint

If progress only exists in the agent transcript, the workflow is not resumable.

## Progress ledger

Use an explicit ledger so the controller can answer:

- what work exists
- what has completed
- what failed
- what is pending retry
- what is blocked

Typical ledger fields:

- stable work id
- status
- attempt count
- last update time
- checkpoint pointer
- verification result

## Resume boundaries

Choose resume boundaries where a completed unit can be safely reloaded without rerunning ambiguous work.

Good resume boundaries are:

- after a validated coding-agent result
- after a persisted state transition
- after a queue item is marked complete

Bad resume boundaries are:

- in the middle of an unvalidated multi-step prompt
- after partially applying changes with no ledger update

## Idempotent step design

Prefer steps that can be retried safely.

Practical rules:

- separate compute from commit when possible
- write outputs to explicit locations
- validate before mutating external state
- record whether the mutation already happened

The goal is to avoid duplicate effects after interruption or retry.

## Preventing skipped work

Skipped work is often a bookkeeping problem, not an intelligence problem.

To prevent it:

- enumerate work explicitly in code
- keep a stable id for each work unit
- update ledger state only after verification
- report counts for pending, complete, failed, and blocked work

If the controller cannot count unfinished work, it cannot prove completeness.

## Failure and retry boundaries

Retries should be bounded and explicit.

Persist:

- why the step failed
- whether the failure is retryable
- how many attempts have happened
- what escalation path remains

Do not hide retries inside large prompts. The controller should own retry policy.
