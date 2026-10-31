## Metadata

- **ID**: REQ-HTTP-030
- **Title**: Send nosniff and forbid framing of nina-laaf.de by other origins
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-3, `REF-ASVS-5`; parent https://github.com/dalini/nina-website/issues/12

## Requirement

- **Statement**: Every public response MUST send `X-Content-Type-Options: nosniff`, and the site MUST forbid being framed by other origins.
- **Rationale**: Prevents content-type confusion of uploaded files (T-7) and framing that misleads visitors (T-28).
- **Assumptions**: None
- **Out of Scope**: The editing and preview hosts (REQ-HTTP-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting
- **Security Traceability**: SEC-HTTP-3

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: All responses of `nina-laaf.de`
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HTTP-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Architecture
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-7, T-28; CWE-1021
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Visitor requests
- **Authoritative Enforcement Point**: Static Hosting (nginx on the VPS)
- **Independent Verification**: Checked from outside the VPS against the live host
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C Content Security Policy Level 3 (`frame-ancestors`)
- **Mapping Basis**: Other: `frame-ancestors` is defined by W3C CSP.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given any response, when its headers are checked, then it carries `X-Content-Type-Options: nosniff` and a framing prohibition.
2. **AC-02 — Boundary or failure behavior**: Given a test page on another origin that frames a site page, when it is loaded in each supported browser, then the frame does not display the site.
3. **AC-03 — Prohibited behavior**: Given a PDF or image response, when its headers are checked, then the `Content-Type` matches the file and nosniff is present.

## Failure Behavior

- **On Invalid Input**: Malformed requests are rejected by nginx with a 4xx status; no content changes
- **On Authentication Failure**: N/A: public responses need no sign-in
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Deny by default: a host without the required configuration does not serve the site
- **On External Dependency Failure**: N/A
- **On System Error**: nginx refuses to start with an invalid configuration; the configuration is checked before it is reloaded, so the running configuration stays in place
- **Logging / Audit**: nginx access logs as SEC-PRIV-6 (REQ-PRIV-020)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code
- **Integration Tests**: Header check in CI; framing test page
- **Security Tests**: Framing attempt from a different origin
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-030/AC-01`; AC-02 → `test:HTTP-030/AC-02`; AC-03 → `test:HTTP-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HTTP-010 (https://github.com/dalini/nina-website/issues/68)
- **Downstream Requirements**: REQ-HTTP-040 (https://github.com/dalini/nina-website/issues/76)
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: `frame-ancestors 'none'` is already in the SEC-HTTP-2 policy; `X-Frame-Options: DENY` additionally covers older browsers.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Two headers and a framing test.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
