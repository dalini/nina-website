## Metadata

- **ID**: REQ-PIPELINE-030
- **Title**: Pin CI steps and build tools to exact versions and verify downloaded tools
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-2, DEP-7; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The pipeline MUST pin every third-party CI action or step and every build tool installed outside the lockfile (generator, image processing) to an exact version, using a commit SHA where the platform supports it, MUST verify downloaded tools against a published checksum or signature, and MUST resolve dependencies from a committed lockfile without floating versions.
- **Rationale**: A compromised or changed tool release would run in every build with access to secrets (SECURITY.md T-19).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: Dependency vetting before adding a dependency (DEP-1 to DEP-6, applied in each issue that adds one). Tools: Hugo, GitHub Actions (step 10).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline (TB-4)
- **Security Traceability**: SEC-CICD-2, DEP-7

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: CI configuration; tool installation steps; lockfile
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Restricted (deploy credentials); Public (output)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Supply Chain
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v1 T-19; CWE-494, CWE-829
- **Abuse / Misuse Case**: An attacker moves a CI action tag or replaces a tool download to inject script into pages or read secrets.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Third-party CI actions, tool downloads, package registries
- **Authoritative Enforcement Point**: CI/CD platform configuration and Static Hosting
- **Independent Verification**: Branch protection and CI permissions are enforced by the platform, not by the build script
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security and Software Supply Chain Security Cheat Sheets (`REF-CICD`, `REF-SUPPLY`)
- **Mapping Basis**: Other: `REF-CICD`, `REF-SUPPLY`, `REF-SSDF` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the CI configuration, when every third-party action or step reference is listed, then each is pinned to a commit SHA, or to an exact version where SHAs are not supported.
2. **AC-02 — Boundary or failure behavior**: Given a downloaded build tool whose checksum or signature does not match the published value, when the pipeline runs, then the step fails and no build output is produced.
3. **AC-03 — Boundary or failure behavior**: Given the lockfile does not match the declared dependencies, when the pipeline runs, then dependency installation fails instead of resolving new versions.
4. **AC-04 — Prohibited behavior**: Given the CI configuration, when it is reviewed, then no step uses a floating reference (branch name, `latest`, version range).

## Failure Behavior

- **On Invalid Input**: Unpinned reference or failed verification fails the pipeline
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: Missing published checksum: the tool is not used until a verifiable source is found
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Pipeline log: run identifier, trigger, branch, Content Store version, result; no secrets (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Pipeline run with a tampered checksum (fails) and with a lockfile mismatch (fails)
- **Security Tests**: Automated lint of CI configuration for floating references
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-030/AC-01`; AC-02 → `test:PIPELINE-030/AC-02`; AC-03 → `test:PIPELINE-030/AC-03`; AC-04 → `test:PIPELINE-030/AC-04`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: GitHub Actions in `dalini/nina-site` and the VPS with nginx (ARCHITECTURE.md) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions in `dalini/nina-site`; VPS with nginx (ARCHITECTURE.md)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; DEP-7 frozen resolution
- **Prohibited Approaches**: `latest` tags; install scripts piped from the network without verification
- **Implementation Guidance**: Keep pinned versions in one place so updates are a reviewed change.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Mechanical pinning and checksum verification with a lint check.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: None (step 10: Hugo; GitHub Actions).
