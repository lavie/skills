# Workflow Topologies

## Choose topology from the task

Do not force every workflow into a linear pipeline. Choose the control shape that matches the work.

Start by asking:

- Is the task mostly state transitions?
- Is there a queue of independent work items?
- Is the goal to reconcile two sources of truth?
- Does planning produce tasks that must later be executed?
- Will the workflow revisit items repeatedly as new information arrives?

## State machine

Use when the workflow has clear lifecycle phases and explicit transitions.

Good fit:

- onboarding flows
- staged integration work
- approval-heavy processes

Avoid when the work is mostly a large queue with uniform handling.

## Work queue

Use when the controller must process many discrete units of work with explicit accounting.

Good fit:

- file or record processing
- media reconciliation
- bulk review with retries

The queue should track item status explicitly so skipped work is impossible to hide.

## Reconciliation pass

Use when the workflow compares two or more sources of truth and resolves mismatches.

Good fit:

- local library versus remote catalog
- local data versus provider inventory
- generated artifact versus reference system

Treat reconciliation as repeated comparison plus explicit resolution steps, not as one large prompt.

## Planner/executor split

Use when one phase decides what should happen and another phase performs bounded tasks.

Good fit:

- staged repository integration
- shopping flows with clarification first and fulfillment later
- research followed by action

The plan itself should still be validated and broken into atomic executor tasks.

## Iterative refinement loop

Use when the workflow must improve an artifact over several passes with verification between passes.

Good fit:

- skeleton then enrich then validate
- draft then inspect then tighten

Each loop needs explicit stop conditions so the process does not drift forever.

## DAG or staged pipeline

Use only when dependency structure is real and stable.

Good fit:

- tasks with clear prerequisites
- branches that can run independently after a setup phase

Do not pick a DAG just because it sounds sophisticated.

## Avoid forcing linear pipelines

Linear pipelines are attractive because they are easy to describe in prose. They are a bad default when the task actually contains:

- retries
- branching
- revisits
- human gates
- reconciliation
- long-lived state

If the workflow description contains many conditional returns to earlier steps, it is probably not a linear pipeline.
