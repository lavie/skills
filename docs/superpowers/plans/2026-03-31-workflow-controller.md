# Workflow Controller Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the first version of the `workflow-controller` skill in this monorepo so coding agents can recognize workflow-shaped tasks and implement them as bespoke controller programs with atomic agent invocations.

**Architecture:** Build a single skill at `skills/workflow-controller/` using the standard `SKILL.md` format plus concise reference documents. Keep the skill instruction-heavy and reference-heavy, with no reusable runtime or controller scaffold in v1. Use upstream skill-authoring helper scripts for bootstrap, UI metadata generation, and validation.

**Tech Stack:** Agent Skills markdown format, repository-local skill files, Python helper scripts from `/Users/assaf/.codex/skills/.system/skill-creator/scripts/`

---

## Planned File Structure

Create or modify these files:

- Create: `skills/workflow-controller/SKILL.md`
- Create: `skills/workflow-controller/agents/openai.yaml`
- Create: `skills/workflow-controller/references/atomic-agent-tasks.md`
- Create: `skills/workflow-controller/references/workflow-topologies.md`
- Create: `skills/workflow-controller/references/state-and-checkpoints.md`
- Create: `skills/workflow-controller/references/agent-cli-invocation.md`
- Create: `skills/workflow-controller/references/verification.md`

Reference-only helper scripts:

- Use: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/init_skill.py`
- Use: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/generate_openai_yaml.py`
- Use: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py`

Reference spec:

- Read: `docs/superpowers/specs/2026-03-31-workflow-controller-design.md`

## Validation Strategy

This repo does not yet contain an automated test harness, so validation for v1 is:

- structural validation with `quick_validate.py`
- placeholder scan with `rg`
- manual smoke-check of trigger behavior against the approved examples from the spec

The implementation must not introduce a generic framework, starter controller template, or marketplace packaging in this pass.

### Task 1: Bootstrap the Skill Skeleton

**Files:**
- Create: `skills/workflow-controller/SKILL.md`
- Create: `skills/workflow-controller/agents/openai.yaml`
- Create: `skills/workflow-controller/references/`
- Read: `docs/superpowers/specs/2026-03-31-workflow-controller-design.md`
- Test: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py`

- [ ] **Step 1: Create the skill parent directory**

Run:

```bash
mkdir -p skills
```

Expected: `skills/` exists at repo root.

- [ ] **Step 2: Generate the initial skill scaffold with references enabled**

Run:

```bash
python /Users/assaf/.codex/skills/.system/skill-creator/scripts/init_skill.py workflow-controller \
  --path /Users/assaf/Dropbox/Dev/workflow-skill/skills \
  --resources references \
  --interface display_name="Workflow Controller" \
  --interface short_description="Turn brittle prompts into controller code" \
  --interface default_prompt="Use $workflow-controller to implement this workflow as code plus atomic coding-agent tasks."
```

Expected: a new `skills/workflow-controller/` directory containing `SKILL.md`, `agents/openai.yaml`, and `references/`.

- [ ] **Step 3: Inspect the generated file layout**

Run:

```bash
find skills/workflow-controller -maxdepth 3 -type f | sort
```

Expected: only the scaffold files you intend to keep for this skill.

- [ ] **Step 4: Run structural validation on the fresh scaffold**

Run:

```bash
python /Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  /Users/assaf/Dropbox/Dev/workflow-skill/skills/workflow-controller
```

Expected: exit code `0`, with no blocking structural errors.

- [ ] **Step 5: Commit the scaffold**

Run:

```bash
git add skills/workflow-controller
git commit -m "feat: scaffold workflow-controller skill"
```

### Task 2: Write the Reference Documents

**Files:**
- Create: `skills/workflow-controller/references/atomic-agent-tasks.md`
- Create: `skills/workflow-controller/references/workflow-topologies.md`
- Create: `skills/workflow-controller/references/state-and-checkpoints.md`
- Create: `skills/workflow-controller/references/agent-cli-invocation.md`
- Create: `skills/workflow-controller/references/verification.md`
- Read: `docs/superpowers/specs/2026-03-31-workflow-controller-design.md`
- Test: `skills/workflow-controller/references/*.md`

- [ ] **Step 1: Write `atomic-agent-tasks.md`**

Write these sections:

- `# Atomic Agent Tasks`
- `## Why prompts get too big`
- `## Smell tests` covering `and`, `for each`, chained goals, and hidden loops
- `## Hard default: decompose`
- `## Design-time review`
- `## Implementation-time review`
- `## When to keep a prompt fused`

- [ ] **Step 2: Write `workflow-topologies.md`**

Write these sections:

- `# Workflow Topologies`
- `## Choose topology from the task`
- `## State machine`
- `## Work queue`
- `## Reconciliation pass`
- `## Planner/executor split`
- `## Iterative refinement loop`
- `## DAG or staged pipeline`
- `## Avoid forcing linear pipelines`

- [ ] **Step 3: Write `state-and-checkpoints.md`**

Write these sections:

- `# State and Checkpoints`
- `## Durable state model`
- `## Progress ledger`
- `## Resume boundaries`
- `## Idempotent step design`
- `## Preventing skipped work`
- `## Failure and retry boundaries`

- [ ] **Step 4: Write `agent-cli-invocation.md`**

Write these sections:

- `# Agent CLI Invocation`
- `## Reuse the current agent harness`
- `## Keep context minimal`
- `## Capture structured outputs`
- `## Respect the CLI boundary`
- `## Avoid premature raw-API abstractions`

- [ ] **Step 5: Write `verification.md`**

Write these sections:

- `# Verification`
- `## Contract-first agent tasks`
- `## Parsing and validation`
- `## Success and failure conditions`
- `## Bounded retry guidance`
- `## Audit logging as a recommended default`

- [ ] **Step 6: Scan the new reference files for placeholders**

Run:

```bash
rg -n "TODO|TBD|FIXME|placeholder" skills/workflow-controller/references
```

Expected: no matches.

- [ ] **Step 7: Commit the reference set**

Run:

```bash
git add skills/workflow-controller/references
git commit -m "feat: add workflow-controller reference docs"
```

### Task 3: Author the Core `SKILL.md`

**Files:**
- Modify: `skills/workflow-controller/SKILL.md`
- Read: `skills/workflow-controller/references/atomic-agent-tasks.md`
- Read: `skills/workflow-controller/references/workflow-topologies.md`
- Read: `skills/workflow-controller/references/state-and-checkpoints.md`
- Read: `skills/workflow-controller/references/agent-cli-invocation.md`
- Read: `skills/workflow-controller/references/verification.md`
- Read: `docs/superpowers/specs/2026-03-31-workflow-controller-design.md`
- Test: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py`

- [ ] **Step 1: Replace the scaffold frontmatter with the real trigger metadata**

Set:

```yaml
---
name: workflow-controller
description: Use when a task is too workflow-shaped for prompt-only orchestration and should be implemented as ordinary code with atomic coding-agent sub-tasks, checkpointing, explicit accounting, and machine-verifiable contracts.
---
```

Expected: frontmatter contains only `name` and `description`.

- [ ] **Step 2: Write the core method sections**

Write these sections:

- `# Workflow Controller`
- `## Core claim`
- `## Detect workflow-shaped tasks`
- `## Reject prompt accretion`
- `## Hard decomposition rule`
- `## Prompt-atomization reviews`
- `## Choose control topology`
- `## Define atomic coding-agent task contracts`
- `## Implement deterministic control in code`
- `## Preserve resume, accounting, and verification`
- `## Keep workflows bespoke`
- `## Reference map`

- [ ] **Step 3: Add explicit non-goals and default assumptions**

Include the v1 non-goals from the spec, the CLI-first recursive invocation assumption, the Python default with Go fallback, and the rule against premature framework extraction.

- [ ] **Step 4: Link the reference files directly from `SKILL.md`**

Make each reference discoverable from the top-level skill document. Keep the reference map one level deep.

- [ ] **Step 5: Run structural validation on the authored skill**

Run:

```bash
python /Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  /Users/assaf/Dropbox/Dev/workflow-skill/skills/workflow-controller
```

Expected: exit code `0`, with no blocking validation errors.

- [ ] **Step 6: Commit the authored skill**

Run:

```bash
git add skills/workflow-controller/SKILL.md
git commit -m "feat: author workflow-controller skill"
```

### Task 4: Regenerate and Refine `agents/openai.yaml`

**Files:**
- Modify: `skills/workflow-controller/agents/openai.yaml`
- Read: `/Users/assaf/.codex/skills/.system/skill-creator/references/openai_yaml.md`
- Test: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py`

- [ ] **Step 1: Regenerate `agents/openai.yaml` from the finished skill text**

Run:

```bash
python /Users/assaf/.codex/skills/.system/skill-creator/scripts/generate_openai_yaml.py \
  /Users/assaf/Dropbox/Dev/workflow-skill/skills/workflow-controller \
  --interface display_name="Workflow Controller" \
  --interface short_description="Turn brittle prompts into controller code" \
  --interface default_prompt="Use $workflow-controller to redesign this workflow as code plus atomic coding-agent tasks."
```

Expected: `skills/workflow-controller/agents/openai.yaml` is regenerated without inventing extra dependencies.

- [ ] **Step 2: Inspect the generated metadata for scope drift**

Confirm the short description, display name, and default prompt all reflect the approved spec and do not imply a framework, template, or raw API adapter.

- [ ] **Step 3: Re-run structural validation**

Run:

```bash
python /Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  /Users/assaf/Dropbox/Dev/workflow-skill/skills/workflow-controller
```

Expected: exit code `0`.

- [ ] **Step 4: Commit the metadata update**

Run:

```bash
git add skills/workflow-controller/agents/openai.yaml
git commit -m "feat: add workflow-controller UI metadata"
```

### Task 5: Smoke-Validate Triggering and Guardrails

**Files:**
- Modify if needed: `skills/workflow-controller/SKILL.md`
- Modify if needed: `skills/workflow-controller/references/*.md`
- Read: `docs/superpowers/specs/2026-03-31-workflow-controller-design.md`
- Test: `/Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py`

- [ ] **Step 1: Manually compare the final skill against the three approved examples**

Check that the skill clearly applies to:

- media-library reconciliation
- staged repository integration
- shopping orchestration

And clearly does not over-trigger on one-shot coding tasks or ordinary scripts.

- [ ] **Step 2: Tighten wording where the trigger feels too broad or too narrow**

Prefer edits that improve trigger precision without expanding scope or adding framework assumptions.

- [ ] **Step 3: Re-run placeholder and structure checks**

Run:

```bash
rg -n "TODO|TBD|FIXME|placeholder" skills/workflow-controller
python /Users/assaf/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  /Users/assaf/Dropbox/Dev/workflow-skill/skills/workflow-controller
```

Expected: no placeholder matches and validator exit code `0`.

- [ ] **Step 4: Commit the final v1 tightening pass**

Run:

```bash
git add skills/workflow-controller
git commit -m "feat: finalize workflow-controller v1"
```
