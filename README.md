# arc-infra

Builds the custom GitHub Actions runner image for the AutomationNexus ARC setup.

## Why the GHCR build workflow isn't on `ubuntu-latest`

This is a **private repo on a Free plan with no GitHub-hosted runner minutes available**, so a job on
`ubuntu-latest` fails at startup. Two consequences:

1. **The first image is built LOCALLY** with Docker (bootstrap — see below).
2. **`.github/workflows/build-runner-image.yml` targets the self-hosted ARC pool** (`automationnexus-k3s`),
   whose runner pods have Docker-in-Docker. It's **manual-only** and only works **after ARC is live**.

## Bootstrap: build the first image locally

Needs Docker (Docker Desktop is fine). Pushes to GHCR with your `gh` token:

```bash
gh auth token | docker login ghcr.io -u <your-gh-login> --password-stdin
docker buildx build --platform linux/amd64 \
  -t ghcr.io/automationnexus/arc-runner:2.335.1-ci1 \
  --push -f Dockerfile.runner .
```

Then make the image pullable by the k3s nodes — **Org → Packages → `arc-runner`**: either set it
**Public**, or keep it private and create a `ghcr-pull` secret on each node
(`ARC-K3S-MIGRATION-GUIDE-AUDITED.md` §6.2) + uncomment `imagePullSecrets` in the common Helm values.

## Rebuilds (after ARC is live)

**Actions → "Build ARC runner image" → Run workflow.** It runs on your ARC runners (DinD) and pushes the
new tag. No hosted runners or local Docker needed.

## Bumping the runner version

Edit the `FROM` tag in `Dockerfile.runner` **and** the `TAG` in the workflow together (keep them in
lock-step with the GitHub Actions runner release), then rebuild (locally or via the workflow).
