## Metadata

- **ID**: REQ-HTTP-020
- **Title**: Send the self-only Content Security Policy on every public response
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-2, `REF-XSS`; REQUIREMENTS.md NFR-3; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: Every public response of `nina-laaf.de` MUST send `Content-Security-Policy: default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; font-src 'self'; base-uri 'self'; form-action 'none'; frame-ancestors 'none'`, and the generated pages MUST work under it without violations.
- **Rationale**: The policy blocks injected and third-party script, styles and images (T-32, T-34).
- **Assumptions**: None
- **Out of Scope**: The preview and editing hosts (REQ-HTTP-040, REQ-HTTP-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting; Public Site
- **Security Traceability**: SEC-HTTP-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: All responses of `nina-laaf.de`, including error pages
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HTTP-010, REQ-NAV-020
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding, Architecture
- **Threat References**: STRIDE Tampering / Information Disclosure: SECURITY.md Threat Model v2 T-32, T-34; CWE-79, CWE-693
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
- **Other**: W3C Content Security Policy Level 3; OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: the W3C CSP specification defines the header; `REF-XSS` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given any page or error response, when its headers are checked, then the CSP header equals the SEC-HTTP-2 policy exactly.
2. **AC-02 — Boundary or failure behavior**: Given every page type is loaded in a browser, when CSP violation reports are collected, then there are none.
3. **AC-03 — Prohibited behavior**: Given the generated output, when it is searched, then it contains no inline script, inline event handler or inline style attribute that the policy would block.

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
- **Integration Tests**: Header check in CI against the test server; browser run over every page type collecting CSP violations
- **Security Tests**: Inject a test inline script into a fixture page: it is blocked
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-020/AC-01`; AC-02 → `test:HTTP-020/AC-02`; AC-03 → `test:HTTP-020/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HTTP-010 ({{ISSUE_URL:REQ-HTTP-010}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}})
- **Downstream Requirements**: REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}})
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: A strict CSP has to be fitted to the generator's output (inline styles, scripts) without weakening it.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
