# Workflow Controller Skill Design

Date: 2026-03-31
Working name: `workflow-controller`
Status: Approved in conversation, written for planning review

## Summary

Create a generic skill that teaches a coding agent to recognize when a task is too workflow-shaped to be solved reliably with prompt accretion, and to instead implement the workflow as ordinary code that invokes coding agents only for narrow, judgment-heavy subproblems.

The skill is not a workflow framework. It is a methodology and implementation pattern for building bespoke controllers that own sequencing, branching, retries, persistence, accounting, and verification, while recursively invoking the current local coding-agent harness for atomic tasks that actually benefit from LLM judgment.

## Problem

Prompt-only orchestration degrades as tasks become longer, more branched, more repetitive, and more stateful. Repeated clarifications and extra rules do not make an LLM behave like a reliable controller. Adherence drops as the process grows. Common failure modes include:

- skipped items in a list or queue
- partial completion with no explicit failure state
- silent rule drift over long horizons
- inability to resume safely after interruption
- oversized prompts that mix multiple judgments, hidden loops, and chained goals

This is especially problematic in tasks that contain repeated steps, branch logic, nested iteration, or long-running stateful progress, but still require localized LLM judgment at some points.

## Goal

Teach the agent to replace brittle prompt orchestration with a bespoke controller program in an ordinary programming language, typically Python, that:

- owns deterministic workflow logic in code
- persists progress and supports resume
- accounts explicitly for work so nothing is silently skipped
- invokes the same locally installed coding-agent CLI the user is already using
- keeps each coding-agent task narrow and atomic
- requires machine-checkable outputs from agent steps
- verifies results before advancing the workflow

## Representative Use Cases

The skill should stay generic, but it is meant to work well on tasks such as:

1. Media-library reconciliation
   A controller iterates through local media, identifies items needing review, invokes the agent for bounded identification or lookup work, validates returned structure, records progress, and reconciles mismatches or missing episodes.

2. Repository integration generation
   A controller analyzes a customer repository in stages, creates an integration skeleton, incrementally enriches it by targeted agent calls, validates code and assumptions at each stage, and resumes safely across many passes.

3. Shopping orchestration
   A controller manages clarification, preference recall, provider lookup, option presentation, selection, and checkout while ensuring each shopping item is handled explicitly and no step is skipped.

These examples illustrate the pattern but do not define the skill narrowly.

## Non-Goals

Version 1 must not become any of the following:

- a generic workflow framework
- a reusable controller runtime
- a linear pipeline template
- an item-by-item processing abstraction
- a raw model API adapter library
- a replacement for ordinary software design

The skill exists to teach decomposition and controller-based implementation, not to impose a universal architecture.

## Core Principles

### 1. Deterministic control belongs in code

Loops, branching, retries, sequencing, checkpointing, accounting, and state transitions belong in ordinary code, not in prompts.

### 2. Coding agents are for atomic judgment

Use coding agents only where bounded intelligence is needed, such as interpretation, classification, synthesis, targeted code edits, or scoped repository reasoning.

### 3. Reuse the current agent harness by default

The controller should recursively invoke the same coding-agent CLI the user is already running, because that harness likely provides authentication, tools, skills, plugins, and environment integration that raw model APIs do not.

### 4. Keep workflows bespoke by default

Each workflow should be implemented concretely for the task at hand. Shared abstractions should be extracted only when repetition is obvious and justified.

### 5. Verify everything that can be verified

Every coding-agent step should have an explicit output contract and machine-checkable success criteria whenever possible.

### 6. Resume is a default requirement

Long-running workflows should persist sufficient state to stop and resume safely without skipped work or ambiguous progress.

## When the Skill Should Trigger

The skill should activate when the requested work has one or more of these characteristics:

- long-running or multi-stage execution
- repeated steps across many items or entities
- explicit or implicit branching logic
- interruption risk requiring checkpoint and resume
- correctness risk from skipped items or partial progress
- dependence on both deterministic orchestration and LLM judgment
- evidence that prompt-only orchestration would be brittle

The skill should not activate for:

- one-shot coding tasks
- ordinary scripts where normal programming is sufficient
- tasks that do not meaningfully benefit from coding-agent judgment

## Hard Decomposition Rule

The skill should enforce a strong default: if a coding-agent task looks too large, decompose it.

Important smell tests:

- prompts containing many `and` clauses
- prompts containing explicit or implicit `for each` logic
- prompts that ask the agent to discover, decide, and execute across many items
- prompts combining multiple independent checks or judgments
- prompts that perform one pass and then immediately require a different pass over the same material

Interpretation rule:

- Translate each meaningful `and` into a candidate controller step.
- Translate each `for each` into ordinary code unless there is a clear reason not to.
- Keep each agent call centered on one primary judgment task.
- Only keep a prompt fused if splitting it would clearly lose essential context or create worse failure modes.

Example oversized task:

- `Review the code and make sure each function is both well named and has a doc comment.`

This is a telltale candidate for decomposition because it hides iteration plus multiple independent judgments.

## Required Prompt-Atomization Reviews

The skill should require two prompt-atomization passes:

1. Design-time review
   Inspect the planned workflow and split oversized coding-agent tasks before implementation.

2. Implementation-time review
   Inspect the actual agent-call contracts or prompts in code and split any remaining tasks that still hide multiple judgments, hidden loops, or chained goals.

These reviews should be defaults, not optional advice.

## Required Workflow Method

When the skill activates, the agent should follow this method:

1. Detect that the task is workflow-shaped.
2. Reject prompt accretion as the primary solution once the task crosses that threshold.
3. Decompose the task into:
   - deterministic control logic
   - judgment-heavy subproblems
   - persistence and checkpoint boundaries
   - verification points
   - optional human approval gates
4. Choose the control topology in ordinary code.
5. Define coding-agent task contracts.
6. Implement the workflow in ordinary code.
7. Keep coding-agent invocations narrow and context-minimal.
8. Verify each result and persist progress.
9. Extract abstractions only if clearly earned by repetition.

## Control Topology Guidance

The skill must not assume workflows are linear. It should explicitly allow the agent to choose the topology that fits the task, such as:

- state machine
- work queue
- reconciliation pass
- planner and executor split
- DAG or dependency graph
- iterative refinement loop
- staged pipeline

The skill should tell the agent to derive the structure from the task instead of forcing a template.

## Default Implementation Stack

Version 1 should recommend:

- Python as the default implementation language
- CLI-first recursive invocation of the current coding-agent harness

Rationale:

- Python is usually accessible and readable for most users.
- The coding-agent harness provides tools, skills, plugins, and authentication that should be reused.

Fallback guidance:

- Prefer Go when the workflow needs a more robust deployable binary or stricter operational simplicity, and that tradeoff clearly matters.

The skill should not force Python when the surrounding context strongly favors another suitable language.

## Coding-Agent Invocation Assumption

Default assumption:

- Whatever coding agent the user is currently using is the one the controller should invoke recursively.

The skill should steer the implementation toward an invocation boundary shaped around the installed agent CLI rather than raw provider APIs.

The invocation layer should be replaceable in principle, but the skill should not encourage premature abstraction.

## Required Guarantees in Generated Workflows

The skill should treat these as non-negotiable defaults:

1. Checkpoint and resume from persisted state
2. Strict per-item or per-step accounting so nothing is silently skipped
3. Machine-verifiable outputs or contracts at each coding-agent step

Recommended but not mandatory in every workflow:

- audit trail of prompts, outputs, and decisions
- bounded retry and recovery behavior
- human approval gates before destructive or costly actions

## Expected Artifacts From a Workflow Built With This Skill

When the skill is used on a real task, the default outcome should be a fuller implementation, not just a design memo. The resulting workflow will usually include:

- a bespoke controller program in ordinary code
- a durable state model for checkpoint and resume
- explicit work accounting
- a narrow coding-agent CLI invocation layer
- structured contracts for every coding-agent task
- verification logic for coding-agent outputs
- task-appropriate topology chosen from the workflow itself

The skill should teach this outcome without shipping a framework or scaffold that assumes a particular control shape.

## Skill Packaging Direction for Version 1

Version 1 should be instruction-heavy and reference-heavy, not scaffold-heavy.

Planned contents:

- `skills/workflow-controller/SKILL.md`
- `skills/workflow-controller/references/atomic-agent-tasks.md`
- `skills/workflow-controller/references/workflow-topologies.md`
- `skills/workflow-controller/references/state-and-checkpoints.md`
- `skills/workflow-controller/references/agent-cli-invocation.md`
- `skills/workflow-controller/references/verification.md`
- optionally `skills/workflow-controller/agents/openai.yaml`

Version 1 should not include reusable controller code or framework scripts unless a later pass proves a truly generic component exists without over-assuming workflow shape.

## Scope Boundary for the First Implementation

The first implementation should focus on authoring the skill itself inside the `lavie/skills` monorepo.

Out of scope for this first implementation:

- building a generic workflow runtime
- shipping a starter controller template
- cross-vendor marketplace packaging
- solving a concrete end-user workflow with this skill in the same change

Those can follow after the skill exists and is tested.

## Success Criteria

The initial skill is successful if it causes a coding agent to:

- notice when a task is too large for prompt-only orchestration
- redesign that task as code plus bounded coding-agent calls
- choose a topology appropriate to the task rather than a forced linear pipeline
- perform prompt-atomization reviews during design and implementation
- preserve resume, accounting, and verification as defaults
- avoid drifting into framework-building or template overreach

## Planning Notes

The next step after spec approval should be an implementation plan for creating the skill contents in the monorepo, with emphasis on:

- writing a strong `SKILL.md` trigger description
- organizing the reference files so they are specific and one level deep
- keeping the core skill concise
- validating that the skill pushes toward decomposition without becoming dogmatic or unusably rigid
