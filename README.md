# ARCRunner

Builds the custom GitHub Actions runner image for the AutomationNexus ARC setup.

This repo is **public** on purpose — it holds only a Dockerfile + build workflow (no secrets) — so the
image build runs on **free GitHub-hosted `ubuntu-latest`**. Only this tooling repo uses hosted runners;
all 5 app repos run their CI on the self-hosted **ARC** pool (`automationnexus-k3s`).

## Build / rebuild the image

**Actions → "Build ARC runner image" → Run workflow** (or push a change to `Dockerfile.runner`).
It builds `linux/amd64` and pushes `ghcr.io/automationnexus/arcrunner:2.335.1-ci1`.

The GHCR package should be **public** so the k3s nodes pull it with no credential. (If you keep it
private, add a `ghcr-pull` secret per node — see `ARC-K3S-MIGRATION-GUIDE-AUDITED.md` §6.2.)

## Local build (fallback, no Actions)

```bash
gh auth token | docker login ghcr.io -u <your-gh-login> --password-stdin
docker buildx build --platform linux/amd64 \
  -t ghcr.io/automationnexus/arcrunner:2.335.1-ci1 \
  --push -f Dockerfile.runner .
```

## Bumping the runner version

Edit the `FROM` tag in `Dockerfile.runner` **and** the `TAG` in the workflow together (keep them in
lock-step with the GitHub Actions runner release), then rebuild.
