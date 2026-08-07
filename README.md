# ARCRunner

Builds the custom GitHub Actions runner image used by the AutomationNexus and ApplyForge
ARC (actions-runner-controller) pools.

This repo is **public** on purpose — it holds only a Dockerfile + build workflow (no secrets).
It's a personal repo (moved out of the AutomationNexus org) — the image it builds is still
consumed by both orgs' self-hosted k3s runner pools.

The image build runs on GitHub-hosted `ubuntu-latest` — this repo has no access to either
org's private ARC pool, and building the pool's own image on GitHub-hosted infra avoids the
bootstrap risk of a self-hosted build breaking its own runners.

## Build / rebuild the image

**Actions → "Build ARC runner image" → Run workflow** (or push a change to `Dockerfile.runner`).
It builds `linux/amd64` and pushes `ghcr.io/ghostkernel-core/arcrunner:2.335.1-ci1`.

The GHCR package should be **public** so the k3s nodes pull it with no credential.

## Local build (fallback, no Actions)

```bash
gh auth token | docker login ghcr.io -u <your-gh-login> --password-stdin
docker buildx build --platform linux/amd64 \
  -t ghcr.io/ghostkernel-core/arcrunner:2.335.1-ci1 \
  --push -f Dockerfile.runner .
```

## Bumping the runner version

Edit the `FROM` tag in `Dockerfile.runner` **and** the `TAG` in the workflow together (keep them in
lock-step with the GitHub Actions runner release), then rebuild.
