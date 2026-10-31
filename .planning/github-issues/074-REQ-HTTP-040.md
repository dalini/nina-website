## Metadata

- **ID**: REQ-HTTP-040
- **Title**: Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-4, SEC-HTTP-1, SEC-HTTP-3; ARCHITECTURE.md DR-9; parent https://github.com/dalini/nina-website/issues/12

## Requirement

- **Statement**: `edit.nina-laaf.de` and `preview.nina-laaf.de` MUST be served over HTTPS only, MUST redirect HTTP to HTTPS and send an HSTS header, MUST send `X-Content-Type-Options: nosniff`, and MUST forbid framing by other origins; the preview site MUST send the Content Security Policy of SEC-HTTP-2 without the Google origins (`default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; font-src 'self'; base-uri 'self'; form-action 'none'; frame-ancestors 'none'`: the `connect-src` directive of SEC-HTTP-2 lists only Google origins, so it is left out and `default-src 'none'` applies).
- **Rationale**: Editor credentials and unpublished content must not travel in clear text or be framed (T-3, T-4); preview pages render unpublished content (T-66).
- **Assumptions**: None
- **Out of Scope**: The Editing Interface's own CSP (REQ-HTTP-050); preview sign-in (REQ-PREVIEW-010).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (serves `edit.` and `preview.`); DR-9 (Editing Interface on its own origin)
- **Security Traceability**: SEC-HTTP-4 (except the Editing Interface CSP)

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: All responses of `edit.nina-laaf.de` and `preview.nina-laaf.de`
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Integrity
- **Control Layers**: Architecture, Data Protection
- **Threat References**: STRIDE Tampering / Information Disclosure: SECURITY.md Threat Model v3 T-3, T-4, T-66; CWE-319, CWE-1021
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface and preview site)
- **Untrusted Inputs or Assertions**: Requests to the editing and preview hosts
- **Authoritative Enforcement Point**: Static Hosting (nginx on the VPS)
- **Independent Verification**: Checked from outside the VPS against the live host
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: IETF RFC 6797 (HTTP Strict Transport Security)
- **Mapping Basis**: Other: RFC 6797 defines HSTS.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given both hosts, when any path is requested over HTTPS, then the response carries HSTS, nosniff and a framing prohibition, and the preview response carries the SEC-HTTP-2 CSP without the Google origins exactly.
2. **AC-02 — Boundary or failure behavior**: Given either host, when any path is requested over HTTP, then the response is a redirect to HTTPS with no content.
3. **AC-03 — Prohibited behavior**: Given a page on another origin that frames either host, when it is loaded, then the frame does not display it.
4. **AC-04 — Additional criterion**: Given any preview response, when its CSP is checked, then it names no Google origin (SEC-HTTP-4, SEC-PRIV-5).

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
- **Integration Tests**: Header and redirect checks against both test hosts
- **Security Tests**: Framing attempts; HTTP request matrix
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-040/AC-01`; AC-02 → `test:HTTP-040/AC-02`; AC-03 → `test:HTTP-040/AC-03`; AC-04 → `test:HTTP-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HTTP-010 (https://github.com/dalini/nina-website/issues/68), REQ-HTTP-020 (https://github.com/dalini/nina-website/issues/69), REQ-HTTP-030 (https://github.com/dalini/nina-website/issues/75)
- **Downstream Requirements**: REQ-HTTP-060 (https://github.com/dalini/nina-website/issues/77), REQ-HTTP-070 (https://github.com/dalini/nina-website/issues/78), REQ-PREVIEW-010 (https://github.com/dalini/nina-website/issues/91), REQ-EDIT-010 (https://github.com/dalini/nina-website/issues/103), REQ-CONSENT-040 (https://github.com/dalini/nina-website/issues/104), REQ-AUTHN-010 (https://github.com/dalini/nina-website/issues/105), REQ-HTTP-050 (https://github.com/dalini/nina-website/issues/111)
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Reuses the public host's header set on two more hosts.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
