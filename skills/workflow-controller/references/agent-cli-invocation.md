# Agent CLI Invocation

## Reuse the current agent harness

Default to recursively invoking the same coding-agent CLI the user is already using.

Reasons:

- authentication is likely already configured
- skills and plugins already exist there
- tool access and sandbox behavior are already understood
- the harness often adds more value than the raw model alone

Only switch away from the current harness when the user explicitly asks or the harness is clearly unsuitable.

## Keep context minimal

Each coding-agent invocation should receive only the local context required for the current judgment task.

Prefer passing:

- the exact files or paths relevant to the subproblem
- the narrow question to answer
- the required output contract
- the stop condition

Avoid passing the entire workflow history unless it is genuinely necessary.

## Capture structured outputs

Design each invocation around a result contract that the controller can parse and verify.

Prefer outputs that are:

- JSON or another structured format
- small enough to inspect programmatically
- explicit about status, findings, and next action

Free-form prose is acceptable only when the controller can still decide mechanically whether the task succeeded.

## Respect the CLI boundary

The controller should treat the coding-agent CLI as a worker boundary, not as a hidden part of the controller.

That means:

- ordinary code decides when to invoke the agent
- ordinary code decides what input the agent receives
- ordinary code validates the output before continuing
- retries and escalation stay outside the prompt

## Avoid premature raw-API abstractions

Do not introduce a provider-agnostic model layer by default.

Start with the real CLI boundary that exists today. If replacement later becomes necessary, extract the boundary from actual usage rather than guessing it upfront.
