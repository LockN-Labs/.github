# LockN Labs — Shared CI/CD Workflows

Centralized reusable workflows for all LockN repositories.

## Available Workflows

| Workflow | Purpose | Runner |
|----------|---------|--------|
| `dotnet-ci.yml` | Build + test .NET projects | GitHub-hosted |
| `integration-test.yml` | Docker compose up → smoke tests → teardown | Self-hosted |
| `contract-check.yml` | Verify frontend API calls match backend handlers | GitHub-hosted |
| `docker-release.yml` | Build + push container to GHCR | Self-hosted |
| `post-deploy-smoke.yml` | Hit prod endpoints after deploy | GitHub-hosted |

## Usage

In your repo's `.github/workflows/ci.yml`:

```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    uses: LockN-Labs/.github/.github/workflows/dotnet-ci.yml@main
  
  integration:
    needs: build
    if: github.ref == 'refs/heads/main'
    uses: LockN-Labs/.github/.github/workflows/integration-test.yml@main
    with:
      health-endpoint: http://localhost:5100/health
```

## Repo Requirements

For integration tests, your repo needs:
- `docker-compose.test.yml` — test environment definition
- `.github/smoke-tests.sh` — endpoint-specific test cases

For contract checks (frontend+backend repos):
- Frontend code with `fetch()` calls
- Backend code with `MapPost()`/`MapGet()` handlers

## Self-Hosted Runner

`threadripper-pro` — registered at org level. Labels: `self-hosted,linux,x64,docker`.

All repos can use `runs-on: [self-hosted, linux, x64, docker]` for jobs that need Docker or local network access.

## Origin

Created after the Apply intake prod failure (2026-02-26). See `docs/rca/2026-02-26-apply-intake-prod-failure.md` in the workspace repo.
