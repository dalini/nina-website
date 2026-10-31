## Metadata

- **ID**: REQ-INSTAGRAM-040
- **Title**: Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: REQUIREMENTS.md FR-8.4, FR-8.5, FR-8.6; ARCHITECTURE.md Instagram Feed Integration, DR-1; SECURITY.md SEC-CICD-1; parent https://github.com/dalini/nina-website/issues/10

## Requirement

- **Statement**: When the Instagram refresh fails, the Site Build MUST still publish all other content and MUST reuse the post list and images already deployed on the VPS, read over the deploy SSH channel with a pinned host key, applying the current hidden-post list and feed switch; when none are deployed, the home page shows only the link (FR-8.4).
- **Rationale**: Publishing must not depend on Instagram's availability or the token's state (T-39).
- **Assumptions**: None
- **Out of Scope**: Token storage and refresh (REQ-INSTAGRAM-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build may read its own earlier output from Static Hosting (DR-1); Instagram Feed Integration
- **Security Traceability**: SEC-CICD-1 (deploy channel, pinned host key)

## Scope

- **Applies To**: Background Processing
- **Components**: Instagram Feed Integration, Site Build, Static Hosting
- **Interfaces / Operations**: Feed fallback in the build; deploy channel read of the deployed feed directory
- **Actors**: Build identity; deploy account on the VPS (REQ-PIPELINE-010)
- **Preconditions**: REQ-INSTAGRAM-030, REQ-PIPELINE-010
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Availability
- **Threat References**: STRIDE Denial of Service / Spoofing: SECURITY.md Threat Model v3 T-39, T-57
- **Abuse / Misuse Case**: A host posing as the VPS serves forged feed files (T-57); the pinned host key prevents the connection
- **Trust Boundary**: TB-4 (Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Feed files read back from the VPS
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: The reused files pass the same post-list format check as a fresh fetch
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Input Validation Cheat Sheet (`REF-INPUT`)
- **Mapping Basis**: Other: `REF-INPUT` as cited in SECURITY.md for this rule.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a failed refresh and a deployed post list of 6 posts, when the build runs, then the home page shows those 6 posts and every other content change is published.
2. **AC-02 — Boundary or failure behavior**: Given a failed refresh and Nina has since hidden one of the deployed posts, when the build runs, then that post is not shown.
3. **AC-03 — Boundary or failure behavior**: Given a failed refresh and no deployed post list, when the build runs, then the home page shows only the @ninalaaf link and the build succeeds.
4. **AC-04 — Prohibited behavior**: Given the VPS presents an SSH host key other than the pinned one, when the build tries to read the deployed files, then no file is read and nothing from that host is published.

## Failure Behavior

- **On Invalid Input**: Reused files that fail the post-list format check are treated as absent
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: files that cannot be verified are not reused; the home page shows only the link
- **On External Dependency Failure**: VPS unreachable or host key mismatch: no posts are reused, the home page shows only the link, and the rest of the build publishes
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: N/A: a failed refresh is logged and does not fail the build

## Test Strategy

- **Unit Tests**: Fallback decision; post-list format check
- **Integration Tests**: Build against a test host with and without deployed feed files; refresh forced to fail
- **Security Tests**: Host-key mismatch test; malformed deployed files
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:INSTAGRAM-040/AC-01`; AC-02 → `test:INSTAGRAM-040/AC-02`; AC-03 → `test:INSTAGRAM-040/AC-03`; AC-04 → `test:INSTAGRAM-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Test target host with the deploy account of REQ-PIPELINE-010 and test keys; mock Instagram API that fails

## Dependencies

- **Upstream Requirements**: REQ-INSTAGRAM-030 (https://github.com/dalini/nina-website/issues/98), REQ-PIPELINE-010 (https://github.com/dalini/nina-website/issues/85)
- **Downstream Requirements**: None
- **External Dependencies**: Static Hosting (VPS) over SSH
- **Dependency Assumptions**: The deployed feed files are those the previous successful build wrote
- **Failure Impact**: Without reusable files the home page shows only the @ninalaaf link (FR-8.4)

## Implementation Notes

- **Constraints**: Deploy SSH channel and account of REQ-PIPELINE-010 (read of the deployed feed files only); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Failing the build because the refresh failed; reading anything outside the deployed feed directory
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: It reads back across a trust boundary from hosting and must fail safe in several ways.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
