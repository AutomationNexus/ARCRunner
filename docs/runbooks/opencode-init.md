# Opencode Init Prompt

Use this prompt when handing the ARCRunner repo to OpenCode or another coding agent.

## Local OpenCode setup

- `opencode.json` and `.opencode/` are local-only. Never commit them.
- Bootstrap on a new machine: run `tools\bootstrap-opencode.cmd` (or `tools\bootstrap-opencode.ps1`). This copies `opencode.json.example` to `opencode.json` when missing, and mirrors committed seeds from `tooling/opencode/` into `.opencode/`.
- Committed templates: `opencode.json.example` and `tooling/opencode/` (project rules, plan/build instructions, agents, commands).
- New sessions default to **plan** mode (`cursor-acp/composer-2.5`, read-only). Switch to **build** with Tab or run `/arc-execute` after plan approval. Build uses the same model with `.opencode/project-rules.md` and `.opencode/build-instructions.md`.
- Built-in `general`, `explore`, and `scout` agents are disabled in `opencode.json`.

```text
You are working in the ARCRunner repo (AutomationNexus custom Actions runner image).

This is a public repo with only Dockerfile.runner and the build workflow — no secrets. It builds ghcr.io/automationnexus/arcrunner for the self-hosted ARC pool.

Start by reading:
- README.md
- docs/runbooks/branch-policy.md
- Dockerfile.runner
- .github/workflows/build-runner-image.yml

Branch model:
- main-only (no dev). Never push directly to main.
- Use feature branches, open PRs to main, merge after CI is green, then delete the feature branch.
- CI and .githooks/pre-push enforce policy for main.
- Read docs/runbooks/branch-policy.md for the exact agent workflow.

Normal workflow:
1. Run git status --short --branch and confirm the working tree before changing files.
2. Work on a feature branch from main (never commit directly on main).
3. Only edit Dockerfile.runner and .github/workflows/build-runner-image.yml unless the user says otherwise.
4. When bumping the runner version, keep Dockerfile.runner FROM tag and workflow TAG in lockstep.
5. Never track AGENT-HANDOFF.md, AGENTS.md, CLAUDE.md, opencode.json, or .opencode/.
6. For OpenCode on a new machine, run tools\bootstrap-opencode.ps1 (creates opencode.json if missing and syncs tooling/opencode to .opencode/). New sessions start in plan mode; say go to switch to build or run /arc-execute after plan approval.
7. Before committing, run /arc-qa or invoke @arc-qa-gatekeeper (branch not main, FROM/TAG lockstep, git diff --check).
8. Push the feature branch and open a PR to main; merge after CI is green.
```
