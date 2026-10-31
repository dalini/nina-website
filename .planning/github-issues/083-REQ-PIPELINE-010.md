## Metadata

- **ID**: REQ-PIPELINE-010
- **Title**: Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-1, SEC-INPUT-3, SEC-SECRET-1, `REF-CICD`, `REF-SSDF`; REQUIREMENTS.md FR-10.4; ARCHITECTURE.md Site Build, Static Hosting, Deployment model; parent https://github.com/dalini/nina-website/issues/14

## Requirement

- **Statement**: The production workflow in `dalini/nina-site` MUST check out `main` of `dalini/nina-content` with a read-only credential, build it with Hugo and, only after every check passed, deploy the output to the `nina-laaf.de` web root with rsync over SSH, using a deploy key restricted to that web root and a deploy account on the VPS that has no root rights and can reach only the web root, the deployed feed files, the Instagram token file (read only) and the publish status file; the workflow's `GITHUB_TOKEN` MUST be read-only, and every connection to the VPS MUST verify its SSH host key against a pinned value.
- **Rationale**: The deploy identity must be able to publish the site and nothing else (T-27, T-57, T-62).
- **Assumptions**: None
- **Out of Scope**: Storing the secrets in the `production` environment limited to `main` (REQ-PIPELINE-050); what the deploy keys may do over SSH (REQ-PIPELINE-140); reading the Instagram token (REQ-INSTAGRAM-030); the triggers (REQ-PIPELINE-080, REQ-PIPELINE-130).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (GitHub Actions in `dalini/nina-site`); Static Hosting (rsync over SSH)
- **Security Traceability**: SEC-CICD-1 (except reading the Instagram token), SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Production workflow; deploy account and key on the VPS
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-CONTENT-030, REQ-HOST-020, REQ-STORE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Authorization, Supply Chain
- **Threat References**: STRIDE Spoofing / Tampering / Elevation of Privilege: SECURITY.md Threat Model v3 T-27, T-57, T-62; CWE-250, CWE-322
- **Abuse / Misuse Case**: A leaked deploy key is used to write outside the web root or to gain root on the VPS; a forged host intercepts the deploy
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Content Store checkout; the VPS identity until the host key is verified
- **Authoritative Enforcement Point**: Site Build pipeline configuration in `dalini/nina-site` (maintainer-only, DR-8)
- **Independent Verification**: Configuration review by Ives and a test run that tries the prohibited path
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`); NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-CICD` and `REF-SSDF` as cited in SECURITY.md; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given valid content on `main` of the test content repository, when the workflow runs, then the output is built and appears in the test web root.
2. **AC-02 — Boundary or failure behavior**: Given content failing a check (REQ-CONTENT-030), when the workflow runs, then the deploy step never runs and the test web root is unchanged.
3. **AC-03 — Boundary or failure behavior**: Given the test host presents a different SSH host key, when the deploy step connects, then the connection fails and nothing is written.
4. **AC-04 — Prohibited behavior**: Given the deploy account, when it tries to write outside the web root and the publish status file, read outside the listed paths, or use sudo, then every attempt is denied.
5. **AC-05 — Additional criterion**: Given the workflow, when its permissions are reviewed, then `GITHUB_TOKEN` has read-only permissions, and only `main` of the Content Store is ever checked out for production.

## Failure Behavior

- **On Invalid Input**: The run fails before any deploy step; nothing is published
- **On Authentication Failure**: N/A: the pipeline authenticates with its own credentials only
- **On Authorization Failure**: A step without the needed permission fails; the run stops and publishes nothing
- **On Security-Decision Failure**: Deny by default: a missing or failed check stops the run before deploy
- **On External Dependency Failure**: GitHub Actions unavailable: no build runs and the previous site stays online
- **On System Error**: The run fails and publishes nothing; the previous site stays online
- **Logging / Audit**: The run log records steps and results; secrets are masked and never printed (SEC-SECRET-1)
- **Alerting**: A failed run emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: N/A unless first-party deploy scripts are added
- **Integration Tests**: End-to-end run against a test content repository and a test host with test keys
- **Security Tests**: Deploy-key misuse attempts (write outside root, shell, sudo); host-key mismatch
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-010/AC-01`; AC-02 → `test:PIPELINE-010/AC-02`; AC-03 → `test:PIPELINE-010/AC-03`; AC-04 → `test:PIPELINE-010/AC-04`; AC-05 → `test:PIPELINE-010/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 (https://github.com/dalini/nina-website/issues/22), REQ-HOST-020 (https://github.com/dalini/nina-website/issues/64), REQ-STORE-010 (https://github.com/dalini/nina-website/issues/79)
- **Downstream Requirements**: REQ-PIPELINE-020 (https://github.com/dalini/nina-website/issues/86), REQ-PIPELINE-030 (https://github.com/dalini/nina-website/issues/87), REQ-PIPELINE-040 (https://github.com/dalini/nina-website/issues/88), REQ-PIPELINE-050 (https://github.com/dalini/nina-website/issues/89), REQ-PIPELINE-060 (https://github.com/dalini/nina-website/issues/90), REQ-PIPELINE-070 (https://github.com/dalini/nina-website/issues/92), REQ-PIPELINE-080 (https://github.com/dalini/nina-website/issues/93), REQ-PIPELINE-090 (https://github.com/dalini/nina-website/issues/95), REQ-PIPELINE-140 (https://github.com/dalini/nina-website/issues/96), REQ-INSTAGRAM-040 (https://github.com/dalini/nina-website/issues/99), REQ-PIPELINE-100 (https://github.com/dalini/nina-website/issues/100), REQ-PIPELINE-110 (https://github.com/dalini/nina-website/issues/101), REQ-PIPELINE-120 (https://github.com/dalini/nina-website/issues/102)
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); secrets come from the `production` environment of `dalini/nina-site`, limited to `main` (REQ-PIPELINE-050); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: A forced-command restriction such as `rrsync` limits the key to the web root; deploying into a new release directory and switching it at the end keeps the previous site online and supports REQ-PIPELINE-070.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Least-privilege deploy identity, host-key pinning and fail-closed gating across GitHub Actions and the VPS.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
