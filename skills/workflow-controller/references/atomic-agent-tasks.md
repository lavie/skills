# Atomic Agent Tasks

## Why prompts get too big

Coding-agent prompts become unreliable when they try to act as both controller and worker. The failure mode is usually not lack of intelligence. It is lack of structure. A single prompt silently accumulates:

- multiple judgments
- hidden iteration
- chained goals
- mixed discovery and execution
- implicit state transitions

When that happens, rule adherence drops and skipped work becomes hard to detect.

## Smell tests

Treat a coding-agent task as oversized by default when it contains any of these smells:

- many `and` clauses
- explicit or implicit `for each` logic
- requests to discover, decide, and execute in one pass
- multiple independent checks in a single prompt
- a second pass hidden inside the first pass

Example smell:

- `Review the code and make sure each function is both well named and has a doc comment.`

This combines iteration with two distinct judgments.

## Hard default: decompose

Decompose unless there is a clear reason not to.

Working rules:

- Translate each meaningful `and` into a candidate controller step.
- Translate each `for each` into ordinary code.
- Keep each coding-agent call centered on one primary judgment task.
- Prefer one narrow call with a machine-checkable output over one broad call with prose.

## Design-time review

Before implementing the workflow:

1. List every planned coding-agent task.
2. Inspect each task for hidden loops or multiple judgments.
3. Split oversized tasks before writing code.
4. Decide what ordinary code will own instead:
   - iteration
   - branching
   - retries
   - checkpointing
   - accounting

If the workflow still looks like a large natural-language procedure, it is not decomposed enough.

## Implementation-time review

After the controller exists, review the actual task contracts or prompts in code.

Check for:

- newly introduced `and` chains
- prompts that grew while debugging
- hidden multi-pass tasks
- fused prompts that now have schema plus interpretation plus action

The implementation-time review exists because prompts tend to expand during iteration.

## When to keep a prompt fused

Do not split blindly.

Keep a prompt fused only when:

- the judgments are tightly coupled and need the same context window
- splitting would force brittle intermediate representations
- the result is still narrow enough to validate mechanically

The burden of proof is on keeping the prompt fused, not on decomposing it.
