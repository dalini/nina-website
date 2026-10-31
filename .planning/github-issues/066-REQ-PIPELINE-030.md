## Metadata

- **ID**: REQ-PIPELINE-030
- **Title**: Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-2, DEP-4, DEP-5, DEP-7, `REF-HUGO-ADV`, `REF-SUPPLY`; ARCHITECTURE.md Site Build (Hugo version pinned); parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Every third-party CI action MUST be pinned to a commit SHA, Hugo and every build tool installed outside the lockfile MUST be pinned to an exact version and verified against a published checksum or signature, and builds MUST resolve dependencies from a committed lockfile with frozen resolution; the pinned Hugo version MUST NOT be affected by the advisories in `REF-HUGO-ADV`.
- **Rationale**: A compromised or floating release would run in every build with access to secrets (T-19, T-50).
- **Assumptions**: None
- **Out of Scope**: The Editing Interface bundle (REQ-EDIT-010); the OAuth helper code (decided in step 10 again; drafted by the step 9 re-run, PQ-34).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (Hugo version pinned)
- **Security Traceability**: SEC-CICD-2 (CI part), DEP-7

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Workflow files; tool install steps; lockfile
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Supply Chain
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v2 T-19, T-50; CWE-494, CWE-829
- **Abuse / Misuse Case**: An action tag is moved to malicious code, or a download is swapped in transit
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Downloaded tools and third-party actions
- **Authoritative Enforcement Point**: Site Build pipeline configuration in `dalini/nina-site` (maintainer-only, DR-8)
- **Independent Verification**: Configuration review by Ives and a test run that tries the prohibited path
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Software Supply Chain Security Cheat Sheet (`REF-SUPPLY`); OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-SUPPLY` and `REF-CICD` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the workflow files, when they are checked by a lint step, then every `uses:` reference is a full commit SHA.
2. **AC-02 — Boundary or failure behavior**: Given the Hugo download's checksum does not match, when the install step runs, then the run fails before any build step.
3. **AC-03 — Boundary or failure behavior**: Given a dependency missing from the lockfile, when the build installs dependencies, then the install fails instead of resolving a version.
4. **AC-04 — Prohibited behavior**: Given the pinned Hugo version, when it is compared with `REF-HUGO-ADV`, then it is at or above v0.165.0.

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

- **Unit Tests**: Lint script for SHA pins (if first-party)
- **Integration Tests**: Workflow run with a tampered checksum fixture
- **Security Tests**: Pin lint on every change; dependency vulnerability check (DEP-5)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-030/AC-01`; AC-02 → `test:PIPELINE-030/AC-02`; AC-03 → `test:PIPELINE-030/AC-03`; AC-04 → `test:PIPELINE-030/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); the `production` environment limited to `main` (PQ-31 answered in step 10 again: paid plan) is drafted by the step 9 re-run; until then secrets are referenced by name and the run is not pointed at production; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Mechanical pinning and checksum verification with a lint check.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
