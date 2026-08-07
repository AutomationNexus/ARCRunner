# CLAUDE.md — ARCRunner

This repo builds the custom GitHub Actions runner image used by the AutomationNexus and
ApplyForge ARC (actions-runner-controller) pools. It is **public** and contains only
`Dockerfile.runner` and the build workflow — no secrets.

> **Personal-account repo, not an AutomationNexus org repo.** This repo was transferred out
> of the `AutomationNexus` org to the personal account `ghostkernel-core` — it no longer has
> access to that org's private ARC pool, CI-Bot App secrets, or shared reusable workflows.
> Every workflow here is self-contained (GitHub-hosted `ubuntu-latest`, `GITHUB_TOKEN` only)
> as a result — do not reintroduce a `uses: automationnexus/.github/...@v1` call or an
> `automationnexus/...` `gh --repo` target; they will not resolve from here.

> **Note on this file's location:** the AutomationNexus org convention (which this repo no
> longer belongs to) commits `CLAUDE.md`/`.claude/` on `dev` and strips them from `main`. This
> repo is **main-only** (no `dev` branch), so that mechanism doesn't apply — this file is
> committed directly on `main` as an explicit, documented exception, since the alternative
> (local-only/gitignored) would mean Claude Code has no committed instructions for this repo
> at all. If a `dev` branch is ever added to this repo, revisit this.

## Branch rules

- This repo is **main-only** (no `dev` branch). If a `dev` branch is added later, use
  feature-branch PRs into it first; never push directly to `dev` or `main`.
- Never push directly to `main` except via the `github-actions[bot]` actor (workflow
  promotion). Use a feature branch and open a PR to `main` for all human changes.
- Start every task with `git status --short --branch` before edits.
- Enable local hook once per clone: `tools\install-githooks.cmd` (blocks direct pushes to
  protected branches).
- This repo is no longer part of the AutomationNexus org workspace — the workspace-root
  `CLAUDE.md`'s org-wide CI/PR flow, auto-versioning, and shared-workflow rules do **not**
  apply here. This file plus the workflow files under `.github/workflows/` are the full
  source of truth for this repo's CI.

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
- Outside the clone: `gh --repo ghostkernel-core/ARCRunner <subcommand>`.
- Tail logs: `... | Select-Object -Last N`. CI workflows stay bash/ubuntu — do not change.

## Subagents

| Agent | Use for | Model |
|-------|---------|-------|
| `qa-gatekeeper` | Branch policy + FROM/TAG lockstep check before PR | haiku |

This repo's scope is small enough (one Dockerfile, one workflow) that no architect/reviewer/
security-auditor agents are warranted — adding them would be over-engineering for the
surface area here. This deliberately minimal team is a documented exception within the
broader cross-repo org model; see the workspace-root `CLAUDE.md`'s "Agent organization"
section for the full rationale.

## Slash commands

`/execute` (apply plan + QA gate), `/qa` (QA gate), `/prepush` (PR readiness check).

## CI is self-contained

All four workflows (`ci.yml`, `build-runner-image.yml`, `semgrep.yml`) run entirely on
GitHub-hosted `ubuntu-latest` with `GITHUB_TOKEN` only — no reusable-workflow calls, no
CI-Bot App, no org secrets. This repo no longer has access to any of those (see the
personal-account note above), so there is nothing to route through a shared workflow. If a
future need genuinely requires cross-repo automation, that's a fresh design decision, not a
reconnection to the old AutomationNexus machinery.

## Do not

- Do not add model/provider/router config anywhere in this repo. Claude Code talks directly
  to Anthropic with the operator's own account.
