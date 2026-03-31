---
name: workflow-controller
description: Recognize workflow-shaped tasks that are too large for prompt-only orchestration and implement them as ordinary code with atomic coding-agent sub-tasks, explicit accounting, checkpoint and resume, and machine-verifiable contracts. Use when work involves repeated steps, branching, reconciliation, staged analysis, long-running state, or prompts that would otherwise accumulate many `and` clauses or hidden `for each` logic.
---

# Workflow Controller

Implement workflow-shaped tasks as bespoke controller programs. Put sequencing, branching, retries, checkpoints, and accounting in ordinary code. Use coding-agent calls only for narrow, judgment-heavy subproblems.

## Core Claim

Prompt accretion is not a controller. Once a task needs durable state, repeated steps, multiple passes, or explicit branching, move the control flow into code.

## Detect Workflow-Shaped Tasks

Treat these signals as evidence that controller code is the right shape:

- repeated work across many items or entities
- branching or conditional retries
- reconciliation between sources of truth
- long-running progress that may need interruption and resume
- staged analysis followed by targeted edits or actions
- high cost for skipped work or partial completion

A long prompt that still reads like a procedure is usually a trigger signal, not a reason to keep expanding the prompt.

Do not use this skill to justify controller code for one-shot tasks that ordinary programming or a single focused coding-agent call can already handle.

## Reject Prompt Accretion

Do not solve workflow complexity by stacking more rules into one prompt. If the task depends on the model remembering a long sequence of instructions while also acting as the driver, assume the structure is wrong.

## Hard Decomposition Rule

Decompose coding-agent work by default.

Use these smell tests:

- many `and` clauses
- explicit or implicit `for each` logic
- multiple independent checks in one task
- requests to discover, decide, and execute in one pass
- hidden second passes over the same material

Translate each meaningful `and` into a candidate controller step. Translate each `for each` into ordinary code unless there is a strong reason not to.

Keep each coding-agent task centered on one primary judgment.

## Prompt-Atomization Reviews

Run two review passes:

1. Design-time review
   Inspect the planned workflow and split oversized coding-agent tasks before implementation.
2. Implementation-time review
   Inspect the actual task contracts or prompts in code and split anything that still hides multiple judgments, hidden loops, or chained goals.

If a prompt grows while you are debugging, review it again instead of normalizing the growth.

## Choose Control Topology

Derive the control shape from the task instead of forcing a template. Pick the simplest topology that fits the real workflow:

- state machine
- work queue
- reconciliation pass
- planner and executor split
- iterative refinement loop
- DAG or staged pipeline

Avoid linear pipelines when the workflow clearly revisits prior steps, branches deeply, or depends on long-lived state.

See [workflow-topologies.md](references/workflow-topologies.md).

## Define Atomic Coding-Agent Task Contracts

Before invoking the coding agent, define:

- the exact input
- the allowed context
- the expected output shape
- the success condition
- the failure condition

Prefer structured outputs that ordinary code can parse and verify. Do not let success depend on vague prose if a stronger contract is possible.

See [verification.md](references/verification.md).

## Implement Deterministic Control In Code

Own these responsibilities in ordinary code:

- iteration
- branching
- retries
- checkpointing
- work ledgers
- progress summaries
- escalation paths

Treat the coding-agent CLI as a worker boundary, not as the controller itself.

## Preserve Resume, Accounting, And Verification

Persist enough state to stop and resume safely. Keep explicit accounting so unfinished work cannot disappear. Validate each coding-agent result before advancing the workflow.

Required defaults:

- checkpoint and resume
- strict per-item or per-step accounting
- machine-verifiable outputs or contracts

Recommended default:

- audit logging proportional to workflow cost and risk

See [state-and-checkpoints.md](references/state-and-checkpoints.md) and [verification.md](references/verification.md).

## Keep Workflows Bespoke

Do not build a generic framework unless repetition clearly earns it. Start with workflow-specific code and extract shared pieces only after they recur in real implementations.

Do not assume:

- a linear pipeline
- item-by-item processing
- a reusable runtime
- a raw model API abstraction

## Default Assumptions

Use these defaults unless the task or user clearly requires otherwise:

- reuse the same local coding-agent harness the user is already running
- prefer a CLI boundary over raw model APIs
- prefer Python for accessibility and readability
- consider Go when deployable binary robustness clearly matters

See [agent-cli-invocation.md](references/agent-cli-invocation.md).

## Reference Map

- [atomic-agent-tasks.md](references/atomic-agent-tasks.md): decomposition rules, smell tests, and prompt-atomization reviews
- [workflow-topologies.md](references/workflow-topologies.md): choosing the right control shape
- [state-and-checkpoints.md](references/state-and-checkpoints.md): durable state, ledgers, and resumability
- [agent-cli-invocation.md](references/agent-cli-invocation.md): recursive use of the current coding-agent harness
- [verification.md](references/verification.md): contracts, parsing, and bounded retry behavior
