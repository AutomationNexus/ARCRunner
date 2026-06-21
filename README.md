# arc-infra

Builds the custom GitHub Actions runner image for the AutomationNexus ARC setup, **without needing a
local Docker host** — the build runs in GitHub Actions on a hosted runner and pushes to GHCR.

## One-time setup

1. **Create this repo in the `automationnexus` org** (private is fine). The org ownership is what lets
   `GITHUB_TOKEN` push to `ghcr.io/automationnexus/...`.
2. Push these two files:
   - `Dockerfile.runner`
   - `.github/workflows/build-runner-image.yml`

## Build / rebuild the image

- **Actions → "Build ARC runner image" → Run workflow** (or just push a change to `Dockerfile.runner`).
- It builds `linux/amd64` and pushes `ghcr.io/automationnexus/arc-runner:2.335.1-ci1`.

## After the first push

- Check **Org → Packages → `arc-runner`** exists.
- Make sure the package is readable by the runner nodes: either make it **public**, or keep it private
  and create the `ghcr-pull` secret on each k3s node (see `ARC-K3S-MIGRATION-GUIDE-AUDITED.md` §6.2) and
  uncomment `imagePullSecrets` in the common Helm values.

## Bumping the runner version

Edit the `FROM` tag in `Dockerfile.runner` **and** the `TAG` in the workflow together (keep them in
lock-step with the GitHub Actions runner release), then re-run the workflow.
