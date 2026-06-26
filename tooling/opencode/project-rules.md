# ARCRunner OpenCode Rules

This repo builds the custom GitHub Actions runner image for AutomationNexus ARC. It is **public** and contains only `Dockerfile.runner` and the build workflow — no secrets.

## Branch Rules

- This repo is **main-only** (no `dev` branch). If a `dev` branch is added later, use feature-branch PRs into it first; never push directly to `dev` or `main`.
- Never push directly to `main` except via the `github-actions[bot]` actor (workflow promotion). Use a feature branch and open a PR to `main` for all human changes.
- Start every task with `git status --short --branch` before edits.
- Work on a feature branch, never on `main` directly.
- Enable local hook once per clone: `tools\install-githooks.cmd` (blocks direct pushes to protected branches).

## Editable Files

- Only edit `Dockerfile.runner` and `.github/workflows/build-runner-image.yml` unless the user explicitly requests other paths.
- Keep the `FROM` image tag in `Dockerfile.runner` and any `TAG` (or equivalent base-tag reference) in the build workflow **in lockstep** when bumping the Actions runner version.

## QA Gates

Before opening a PR, run local QA in the same task:

- `git status --short --branch` — confirm not on `main`
- Verify `FROM` tag in `Dockerfile.runner` matches the workflow `TAG` / base reference (see README “Bumping the runner version”)
- `git diff --check`

Invoke `@arc-qa-gatekeeper` or run `/arc-qa` before push. Run `/arc-prepush` before opening or updating a PR.

## Agent Workflow

- New sessions start in built-in `plan` mode (read-only). Switch to `build` with Tab or after plan approval.
- When the user approves a plan and says go, build, or execute, run `/arc-execute` (built-in `build` orchestrator).
- `build` invokes `@arc-qa-gatekeeper` for the full `/arc-qa` gate before finishing.
- Land git changes with a feature branch and PR to `main`; never push directly to `main`.

## Local OpenCode Setup

- `opencode.json` and `.opencode/` are local-only and must not be committed.
- Bootstrap: `tools\bootstrap-opencode.cmd` copies `opencode.json.example` when missing and mirrors `tooling/opencode/` into `.opencode/`.
- Committed seeds live in `tooling/opencode/` and `opencode.json.example`.

## Token-Efficient Handoff

Before switching agents, write a compact handoff:

- Goal: one sentence.
- Files read/touched: paths only.
- Current branch/status: short.
- FROM/TAG lockstep: pass/fail.
- Validation run: commands and pass/fail only.
- Risks/blockers: actionable items only.

Do not paste large diffs or full logs.
