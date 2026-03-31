# Verification

## Contract-first agent tasks

Every coding-agent task should have a contract before it is invoked.

The contract should define:

- required input
- allowed context
- expected output shape
- success condition
- failure condition

If the controller cannot state what counts as success, the task is too vague.

## Parsing and validation

Prefer outputs that the controller can parse mechanically.

Useful validation checks include:

- schema validation
- presence of required fields
- value range checks
- cross-checks against source data
- file existence or diff checks

Do not move forward on “looks good” alone when a stronger check is possible.

## Success and failure conditions

A coding-agent step should end in one of a small number of explicit states:

- success
- retryable failure
- terminal failure
- blocked pending human input

Make the controller decide which state applies based on the returned contract and any downstream checks.

## Bounded retry guidance

Retries should be limited and intentional.

Good retry behavior:

- retry when the failure is transient or contract-shaped
- change something specific between attempts
- escalate after the bounded retry count is reached

Bad retry behavior:

- replay the same large prompt several times
- keep retrying without changing inputs or context

## Audit logging as a recommended default

Audit logs are recommended even when they are not a hard requirement.

Useful audit entries:

- which coding-agent task ran
- what inputs or artifact references it used
- what contract it was expected to satisfy
- what result it returned
- what verification concluded

Keep audit detail proportional to workflow cost and risk.
