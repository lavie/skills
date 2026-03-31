# AGENTS

This repo is a monorepo of reusable coding-agent skills. Treat it as a distribution repo for durable skill artifacts, not as a scratch space for workflow notes or project-specific experiments.

## Purpose

- Keep reusable skills under `skills/`.
- Make each skill independently understandable and installable.
- Prefer generic, portable guidance over vendor-specific lock-in where possible.

## Repo Layout

- Put each skill in `skills/<skill-name>/`.
- Each skill must have a `SKILL.md`.
- Optional skill subdirectories are `references/`, `agents/`, `scripts/`, and `assets/`.
- Keep skill-specific supporting material inside the skill directory rather than at repo root.

## Contribution Rules

- Add a new skill only when it represents a distinct reusable capability.
- If the work is an extension of an existing skill, update that skill instead of creating a near-duplicate.
- Keep the core skill content vendor-neutral when possible.
- Put vendor-specific metadata in vendor-specific files such as `agents/openai.yaml`, not in the core skill body unless necessary.
- Prefer small, focused skills over large umbrella skills with vague scope.

## Repo Hygiene

- Do not commit planning docs, process docs, or brainstorming artifacts.
- Do not commit anything under `docs/superpowers/`.
- Do not commit scratch files, temporary outputs, local validation junk, or worktree artifacts.
- Keep the repo centered on distributable skill content and top-level repo docs such as `README.md` and this file.

## Validation Expectations

- When changing a skill, validate that its structure is still correct.
- Check that links inside `SKILL.md` and any reference docs still resolve.
- Check that any vendor metadata files still match the skill they describe.
- Do not claim completion if the skill docs and repo docs disagree.

## Default Editing Stance

- Preserve existing repo conventions unless there is a clear reason to improve them.
- Keep changes minimal and intentional.
- If a process artifact is useful during development but not part of the distributable repo, keep it out of git.
