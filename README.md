# workflow-controller

Most coding-agent failures do not come from weak models. They come from asking a model to be the workflow engine.

`workflow-controller` is a skill for Codex-style coding agents that teaches a more reliable pattern:

- put control flow in ordinary code
- keep coding-agent calls small and atomic
- persist state so long jobs can resume
- verify every agent result before moving on

If a task keeps accumulating `and` clauses, hidden `for each` loops, retries, exceptions, or multi-step judgment, this skill helps the agent stop prompt-stacking and build the right thing instead.

## A Case Study: Supermarket Shopping

Imagine asking a coding agent:

> Read my shopping list, figure out what each item really means, ask follow-up questions where needed, search my preferred supermarket, compare options, remember my past preferences, build the cart, and prepare checkout.

That sounds reasonable until you look at the structure.

The task is not one task. It is a workflow:

- iterate over a list of items
- branch when an item is ambiguous
- look things up from external systems
- remember previous decisions
- compare alternatives
- ask for approval before expensive actions
- keep track of what has been processed and what has not

This is exactly where stacking prompts starts to break down. The agent may do a good first pass, then skip an item, merge two decisions into one, forget a branch, lose track of state, or fail to recover cleanly after an interruption.

The problem is not intelligence. The problem is architecture.

`workflow-controller` teaches the agent to redesign that job as a program:

- code owns the shopping list iteration
- code owns branching, retries, and checkpoints
- code tracks progress item by item
- the coding agent is invoked only for narrow judgments
- each agent result is returned in a machine-checkable format

The result is not "a better prompt." It is a workflow that can actually survive complexity.

## Prompt Stacking Eventually Fails

Prompt accretion feels productive for a while:

"Also make sure you do not skip any items."

"Also ask me before buying anything expensive."

"Also remember my preferences from last time."

"Also compare brands and package sizes."

"Also recover if the website changes."

At some point, the prompt has quietly become a controller. That is the mistake.

Large language models are useful for judgment-heavy steps. They are weak substitutes for:

- deterministic iteration
- exact accounting
- durable state
- branch handling
- resumability
- strict verification

`workflow-controller` exists to catch that transition and push the agent toward code.

## What This Skill Teaches

The skill trains the agent to:

- detect workflow-shaped tasks before they turn into brittle prompt piles
- spot telltale oversized prompts with too many `and` clauses or implicit `for each` logic
- decompose big tasks into atomic coding-agent calls
- choose a control topology that matches the real job: state machine, queue, reconciliation pass, planner/executor split, iterative refinement loop, or staged pipeline
- implement sequencing, retries, checkpoints, and work accounting in ordinary code
- require structured, machine-verifiable outputs from each coding-agent step
- reuse the same local coding-agent CLI the user is already running, instead of dropping down to raw model APIs by default

## Good Fit

This skill is a strong fit when the task is too complex to one-shot, but still benefits from coding-agent intelligence inside a larger program.

Examples:

- integrating a SaaS product into a customer's codebase through staged analysis, skeleton generation, validation, and iterative completion
- reconciling a media library against online episode guides without skipping files or losing track of mismatches
- shopping or research workflows with clarification loops, external lookups, preference memory, and approval gates
- any long-running task where interruption, resume, and exact accounting matter

## Bad Fit

Do not reach for `workflow-controller` when:

- a normal script can solve the problem with no meaningful judgment calls
- a single focused coding-agent task is already enough
- the task is mostly implementation work inside one repository with no real workflow orchestration problem

This skill is not a framework. It does not force every workflow into the same scaffold. It teaches the agent how to derive the right controller shape for the task at hand.

## What Is In This Repo

Today this repo contains the first version of the `workflow-controller` skill:

- [`skills/workflow-controller/SKILL.md`](skills/workflow-controller/SKILL.md): the core method
- [`skills/workflow-controller/references/atomic-agent-tasks.md`](skills/workflow-controller/references/atomic-agent-tasks.md): how to detect and split oversized coding-agent prompts
- [`skills/workflow-controller/references/workflow-topologies.md`](skills/workflow-controller/references/workflow-topologies.md): how to choose the right controller shape
- [`skills/workflow-controller/references/state-and-checkpoints.md`](skills/workflow-controller/references/state-and-checkpoints.md): resumability and strict accounting patterns
- [`skills/workflow-controller/references/agent-cli-invocation.md`](skills/workflow-controller/references/agent-cli-invocation.md): recursive use of the current coding-agent harness
- [`skills/workflow-controller/references/verification.md`](skills/workflow-controller/references/verification.md): contracts, parsing, and validation

## How To Use This

Tell your coding agent to install the skill from this repo, then use it whenever a task starts turning into a workflow instead of a prompt.

One-line version:

> Install the `workflow-controller` skill from https://github.com/lavie/skills/tree/main/skills/workflow-controller and use it whenever a task has too many `and` clauses, hidden `for each` loops, or needs checkpoints, accounting, and machine-verifiable agent outputs.

## The Core Idea

Prompt accretion is not a controller.

When the workflow gets real, write the workflow as code. Let the coding agent do the parts that require judgment. Verify everything else like a program.
