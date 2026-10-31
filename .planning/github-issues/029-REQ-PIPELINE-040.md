## Metadata

- **ID**: REQ-PIPELINE-040
- **Title**: Keep deploy and editing-system credentials out of the repository, output, client code and logs
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-1; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Deploy, preview-deploy and content-read credentials MUST be stored only in GitHub Actions secrets of the `production` environment of `dalini/nina-site`, and the Instagram token only in a root-only file on the VPS; none MAY appear in a repository, the generated output, client code or logs; every change MUST be scanned for secrets.
- **Rationale**: Leaked credentials allow defacement or account takeover (SECURITY.md T-20, T-27, T-37).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: The Instagram token refresh (SEC-SECRET-3) and the CMS OAuth helper credentials (SEC-AUTHN-1): decomposed in the step 9 re-run. Rotation: Ives, at least yearly and after any suspected leak (SEC-SECRET-1).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build, Static Hosting, Instagram Feed Integration
- **Security Traceability**: SEC-SECRET-1

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: Repository, CI configuration, build output, CI logs
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Restricted (deploy credentials); Public (output)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Data Protection, Supply Chain
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-20, T-27; CWE-798, CWE-532
- **Abuse / Misuse Case**: A credential committed to the repository, printed in a log or copied into the output is read by anyone with access.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Every committed change; build output; log output
- **Authoritative Enforcement Point**: CI/CD platform configuration and Static Hosting
- **Independent Verification**: Branch protection and CI permissions are enforced by the platform, not by the build script
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Secrets Management Cheat Sheet (`REF-SECRETS`)
- **Mapping Basis**: Other: `REF-SECRETS` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the pipeline needs the deploy credential, when it runs, then it reads the credential from the platform's secret store and the run succeeds.
2. **AC-02 — Boundary or failure behavior**: Given a change that adds a string matching a credential pattern, when it is pushed, then secret scanning flags it and the change cannot be merged until resolved.
3. **AC-03 — Prohibited behavior**: Given a completed pipeline run, when the build output and the full log are searched for the configured secret values, then none is found (the log shows masked values only).

## Failure Behavior

- **On Invalid Input**: Detected secret blocks the merge
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: If scanning cannot run, the merge is blocked
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Pipeline log: run identifier, trigger, branch, Content Store version, result; no secrets (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Pipeline run with a test secret; search of output and log
- **Security Tests**: Seeded fake secret in a test branch is detected; output scan for known secret values
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-040/AC-01`; AC-02 → `test:PIPELINE-040/AC-02`; AC-03 → `test:PIPELINE-040/AC-03`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: GitHub Actions in `dalini/nina-site` and the VPS with nginx (ARCHITECTURE.md) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions in `dalini/nina-site`; VPS with nginx (ARCHITECTURE.md)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: Secret store: GitHub Actions secrets of the `production` environment in `dalini/nina-site`; Instagram token on the VPS (SECURITY.md SEC-SECRET-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Secrets in environment files committed to the repository; echoing secrets in build steps
- **Implementation Guidance**: Use the platform's built-in secret masking and scanning where available.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Platform secret store, masking and scanning configuration.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: None (step 10: secret store and rotation in SECURITY.md SEC-SECRET-1).
