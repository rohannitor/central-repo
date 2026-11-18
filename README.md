# Central Golden Pipeline Repository (`central-repo`)

Central Hub for reusable DevSecOps workflows adhering to the Organization's Golden Pipeline Architecture.

## Architecture Principles
1. **Consume, Don't Copy**: Spoke repositories consume these workflows via thin caller workflows (`.github/workflows/ci.yml`).
2. **Strict Pinning**: All GitHub Actions are pinned to full 40-character commit SHAs.
3. **Zero-Credential Deployments**: GitHub OIDC role assumption (`citizen-deploy-<env>`).
4. **Build Once, Promote by Digest via S3**: ARM64 images stored as `.tar.gz` in S3 and pulled via AWS SSM Run Command.
5. **Fail-Closed Security Gates**: PR validation, Ruff, Pyright, Pytest with coverage, Semgrep, Gitleaks, Pip-audit, Trivy, and Syft.

## Reusable Workflows
| Workflow | Purpose | Trigger |
| :--- | :--- | :--- |
| `gp-pr-checks.yml` | Validates PR title (Conventional Commits), branch targeting, and commit messages. | `workflow_call` |
| `gp-python-ci.yml` | Linting (Ruff), format check, type check (Pyright), unit tests & coverage (Pytest). | `workflow_call` |
| `gp-security.yml` | Secret detection (Gitleaks), SAST (Semgrep), dependency audit (Pip-audit). | `workflow_call` |
| `gp-repo-checks.yml` | Action pinning enforcement, CODEOWNERS validation, confidential files detection. | `workflow_call` |
| `gp-deploy-s3-ssm.yml` | ARM64 build, Trivy scan, Syft SBOM, S3 export, and EC2 SSM deployment. | `workflow_call` |

## Pipeline Trigger Note
This central repository runs automated CI (`central-ci.yml`) whenever changes are committed directly to `central-repo`. Consumer repositories invoke these workflows remotely via `workflow_call`.
