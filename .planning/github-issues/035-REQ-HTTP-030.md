## Metadata

- **ID**: REQ-HTTP-030
- **Title**: Send nosniff and forbid framing by other origins
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-3; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: Public responses MUST send `X-Content-Type-Options: nosniff` and MUST forbid framing of the site by other origins.
- **Rationale**: Content-type sniffing can turn files into script; framing lets other sites mislead visitors (SECURITY.md T-7, T-28).
- **Assumptions**: None
- **Out of Scope**: The same headers for the Editing Interface (SEC-HTTP-4; blocked by PQ-1).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting
- **Security Traceability**: SEC-HTTP-3

## Scope

- **Applies To**: Web Client
- **Components**: Static Hosting, Public Site
- **Interfaces / Operations**: Headers on every public response
- **Actors**: Visitor; network attacker
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Architecture
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v1 T-7, T-28; CWE-1021
- **Abuse / Misuse Case**: Another site frames nina-laaf.de to mislead visitors, or a mislabeled upload is sniffed as script.
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Embedding origins; served file types
- **Authoritative Enforcement Point**: Static Hosting configuration
- **Independent Verification**: Automated checks against the deployed site, independent of the hosting configuration
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Other: `REF-ASVS-5` as cited in SECURITY.md (no identifier verified).

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the deployed site, when any page, image or PDF response is inspected, then it carries `X-Content-Type-Options: nosniff` and a framing restriction (CSP `frame-ancestors` and/or `X-Frame-Options`) that allows no other origin.
2. **AC-02 — Boundary or failure behavior**: Given a test page on another origin that frames a site page, when it is loaded, then the browser refuses to render the framed page.
3. **AC-03 — Prohibited behavior**: Given the header configuration, when it is reviewed, then no path is excluded from these headers.

## Failure Behavior

- **On Invalid Input**: N/A: no input
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Missing header fails the CI header check and blocks the release
- **On External Dependency Failure**: N/A
- **On System Error**: If the post-deploy check fails, the deploy is reported as failed
- **Logging / Audit**: Post-deploy check report: URL, expected and observed header values
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Header check in CI on pages, images and PDFs
- **Security Tests**: Framing test from another origin
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-030/AC-01`; AC-02 → `test:HTTP-030/AC-02`; AC-03 → `test:HTTP-030/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Deployed test site on the chosen host (PQ-17); HTTP client for header checks

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: Hosting provider (PQ-17)
- **Dependency Assumptions**: The host applies configured headers to every response, including error pages
- **Failure Impact**: Headers missing on some responses weakens the protection for those responses

## Implementation Notes

- **Constraints**: Host: TO BE DECIDED (PQ-17); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Setting headers in HTML `<meta>` only where the header is not supported that way
- **Implementation Guidance**: `frame-ancestors` can live in the same CSP as REQ-HTTP-020.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Two headers and a framing test.
- **Required Human Review**: Security review
- **Open Decisions**: Non-blocking: PQ-17.
