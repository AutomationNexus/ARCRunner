# CLAUDE.md — ARCRunner

This repo builds the custom GitHub Actions runner image for AutomationNexus ARC. It is
**public** and contains only `Dockerfile.runner` and the build workflow — no secrets.

> **Note on this file's location:** the org convention commits `CLAUDE.md`/`.claude/` on
> `dev` and strips them from `main` (see `automationnexus/.github`'s `docs/ai-migration.md`).
> This repo is **main-only** (no `dev` branch), so that mechanism doesn't apply — this file
> is committed directly on `main` as an explicit, documented exception, since the
> alternative (local-only/gitignored) would mean Claude Code has no committed instructions
> for this repo at all. If a `dev` branch is ever added to this repo, revisit this and move
> to the standard dev-only convention.

## Branch rules

- This repo is **main-only** (no `dev` branch). If a `dev` branch is added later, use
  feature-branch PRs into it first; never push directly to `dev` or `main`.
- Never push directly to `main` except via the `github-actions[bot]` actor (workflow
  promotion). Use a feature branch and open a PR to `main` for all human changes.
- Start every task with `git status --short --branch` before edits.
- Enable local hook once per clone: `tools\install-githooks.cmd` (blocks direct pushes to
  protected branches).

## Editable files

- Only edit `Dockerfile.runner` and `.github/workflows/build-runner-image.yml` unless the
  user explicitly requests other paths.
- Keep the `FROM` image tag in `Dockerfile.runner` and any `TAG` (or equivalent base-tag
  reference) in the build workflow **in lockstep** when bumping the Actions runner version.

## QA gates

Before opening a PR, run local QA in the same task:

```
git status --short --branch   # confirm not on main
git diff --check
```
Plus: verify `FROM` tag in `Dockerfile.runner` matches the workflow `TAG` / base reference
(see README "Bumping the runner version"). Dispatch `qa-gatekeeper` or run `/qa` before
push. Run `/prepush` before opening or updating a PR.

## Shell (Windows local)

- Agent terminal commands use PowerShell (`powershell`; use `pwsh` if installed).
- Chain with `;`, not `&&`. Use Windows paths (`tools\install-githooks.cmd`).
- One shell per tool call when debugging; do not mix cmd/bash/PowerShell in one pipeline.
- Outside the clone: `gh --repo AutomationNexus/ARCRunner <subcommand>`.
- Tail logs: `... | Select-Object -Last N`. CI workflows stay bash/ubuntu — do not change.

## Subagents

| Agent | Use for | Model |
|-------|---------|-------|
| `qa-gatekeeper` | Branch policy + FROM/TAG lockstep check before PR | haiku |

This repo's scope is small enough (one Dockerfile, one workflow) that no architect/reviewer/
security-auditor agents are warranted — adding them would be over-engineering for the
surface area here.

## Slash commands

`/execute` (apply plan + QA gate), `/qa` (QA gate), `/prepush` (PR readiness check).

## Shared CI — do not inline

- This repo's `build-runner-image.yml` is currently self-contained (does not call
  `automationnexus/.github` reusable workflows). If that ever changes, **never inline or
  fork `automationnexus/.github` reusable-workflow logic** into this repo's own workflow
  files — always call it via `uses: automationnexus/.github/.github/workflows/<name>.yml@v1`.
- If a future need requires CI behavior a shared workflow doesn't support, the fix is a new
  **generic** input on the shared workflow (contributed to `automationnexus/.github`), never
  a local copy/paste workaround.
- Never use `GITHUB_TOKEN` for cross-repo automation — only the CI-Bot GitHub App, if this
  repo ever needs cross-repo writes.

## Do not

- Do not add model/provider/router config anywhere in this repo. Claude Code talks directly
  to Anthropic with the operator's own account.
