## Metadata

- **ID**: REQ-PIPELINE-050
- **Title**: Keep the deploy, preview-deploy and content-read secrets in a production environment limited to main
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-1 (GitHub storage), SEC-SECRET-2 (only the production build of `main`; paid plan), `REF-GH-PLANS`; T-20, T-52; parent https://github.com/dalini/nina-website/issues/14

## Requirement

- **Statement**: The production deploy key, the preview deploy key and the content-read credential MUST be stored only as secrets of the `production` environment of `dalini/nina-site`, whose deployment branches are limited to `main`, so that only the build of `main` can use them and builds of other branches or external contributions cannot.
- **Rationale**: Unreviewed branches must not be able to read the keys (T-20); environments limited to `main` need the paid plan that `dalini` uses (T-52, step 10 again).
- **Assumptions**: `dalini` is a personal account on GitHub Pro, so environments with deployment branches are available for the private repositories (step 10 again; account type checked in step 10 a third time)
- **Out of Scope**: Which job may read which secret (REQ-PIPELINE-090); secrets on the VPS (REQ-HOST-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (GitHub Actions in `dalini/nina-site`); Deployment model
- **Security Traceability**: SEC-SECRET-1, SEC-SECRET-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline
- **Interfaces / Operations**: GitHub environment `production`, its deployment branch policy and secrets
- **Actors**: Build identity; maintainer (Ives); contributors to other branches (abuse case)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Restricted
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Authorization, Supply Chain
- **Threat References**: STRIDE Information Disclosure and Repudiation: SECURITY.md Threat Model v3 T-20, T-52; CWE-522
- **Abuse / Misuse Case**: A branch or pull request workflow prints the deploy key
- **Trust Boundary**: TB-4 (Site Build pipeline)
- **Untrusted Inputs or Assertions**: Workflow runs from branches other than `main`
- **Authoritative Enforcement Point**: GitHub environment protection (deployment branches)
- **Independent Verification**: A test run from another branch
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`); GitHub Docs on environments (`REF-GH-PLANS`)
- **Mapping Basis**: Other: `REF-CICD` and `REF-GH-PLANS` as cited in SECURITY.md SEC-SECRET-2.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the `production` environment of `dalini/nina-site` holds the production deploy key, the preview deploy key and the content-read credential, when the build workflow runs on `main`, then its jobs can use them.
2. **AC-02 — Boundary or failure behavior**: Given a workflow run on another branch or from a pull request, when it references the environment or its secrets, then GitHub refuses the deployment and no secret value is available to it.
3. **AC-03 — Prohibited behavior**: Given the repository and its environments, when their secrets are listed, then none of these secrets exists outside the `production` environment, and `dalini/nina-content` holds no Actions secrets (SEC-CICD-7).

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A
- **On Authorization Failure**: GitHub refuses the environment for the run; the job fails without secrets
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: GitHub records deployments to the environment
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: N/A: settings
- **Integration Tests**: Workflow runs on `main` and on a test branch
- **Security Tests**: Attempt to read the secrets from a test branch and from a fork pull request
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-050/AC-01`; AC-02 → `test:PIPELINE-050/AC-02`; AC-03 → `test:PIPELINE-050/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: `dalini/nina-site` (or a test repository with the same plan) with test secret values

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 (https://github.com/dalini/nina-website/issues/85)
- **Downstream Requirements**: REQ-PIPELINE-060 (https://github.com/dalini/nina-website/issues/90), REQ-PIPELINE-090 (https://github.com/dalini/nina-website/issues/95)
- **External Dependencies**: GitHub Actions environments
- **Dependency Assumptions**: GitHub enforces deployment branch policies for private repositories on the paid plan
- **Failure Impact**: Without the limit, any branch run can read the keys

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site` (ARCHITECTURE.md Deployment model); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Repository-level secrets for these keys; secrets in workflow files
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: GitHub environment settings with a negative test from another branch; fully specified by SEC-SECRET-1 and SEC-SECRET-2.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
