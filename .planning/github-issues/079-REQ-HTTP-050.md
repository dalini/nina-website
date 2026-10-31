## Metadata

- **ID**: REQ-HTTP-050
- **Title**: Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-4, SEC-CICD-2, `REF-XSS`; ARCHITECTURE.md Editing Interface, DR-9; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: The Editing Interface MUST send a Content Security Policy that allows script only from its own origin (no inline or third-party script), allows connections only to its own origin and the GitHub endpoints the CMS uses, and sets `frame-ancestors 'none'`; the full directive list MUST name each allowed origin.
- **Rationale**: Sveltia CMS keeps the editor's GitHub token in browser storage; any foreign script in that origin could steal it (T-45, T-46).
- **Assumptions**: None
- **Out of Scope**: Serving the CMS bundle (REQ-EDIT-010); the OAuth helper's sign-in flow (blocked scope, PQ-34).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (own origin, DR-9); Static Hosting
- **Security Traceability**: SEC-HTTP-4 (CSP for the Editing Interface)

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: All responses of `edit.nina-laaf.de`
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-HTTP-040, REQ-EDIT-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Output Encoding, Architecture
- **Threat References**: STRIDE Information Disclosure / Tampering: SECURITY.md Threat Model v2 T-5, T-45, T-46; CWE-79, CWE-829
- **Abuse / Misuse Case**: Stored markup or a manipulated bundle runs script on `edit.nina-laaf.de` and sends the token in `sveltia-cms.user` to another host
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface)
- **Untrusted Inputs or Assertions**: Any script or connection target in the Editing Interface page
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

1. **AC-01 — Expected behavior**: Given the Editing Interface is loaded and used for listing, editing, uploading and saving in a test repository, when CSP violations are collected, then there are none and the CMS works.
2. **AC-02 — Boundary or failure behavior**: Given a test inline script and a script from another origin injected into the page, when it loads, then both are blocked.
3. **AC-03 — Prohibited behavior**: Given a test fetch to a host other than its own origin and the listed GitHub endpoints, when it runs in the page, then the browser blocks it.
4. **AC-04 — Additional criterion**: Given the final policy, when it is reviewed, then every directive and every allowed origin is listed and approved by Ives for SECURITY.md SEC-HTTP-4.

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
- **Integration Tests**: Browser run of the main CMS workflows against a test repository with CSP violation collection
- **Security Tests**: Injected inline and cross-origin script; exfiltration attempt to a non-listed host
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-050/AC-01`; AC-02 → `test:HTTP-050/AC-02`; AC-03 → `test:HTTP-050/AC-03`; AC-04 → `test:HTTP-050/AC-04` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Test `edit.` host serving the pinned Sveltia CMS release (REQ-EDIT-010); test content repository; Chrome, Edge, Firefox, Safari

## Dependencies

- **Upstream Requirements**: REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}}), REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}})
- **Downstream Requirements**: None
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: SEC-HTTP-4 leaves the directive list to implementation; propose it in the pull request so Ives can record it in SECURITY.md.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Fitting a strict CSP to a third-party single-page app without weakening it protects the editors' tokens.
- **Required Human Review**: Security review by Ives, who approves the directive list for SECURITY.md
- **Open Decisions**: None (the directive list is set at implementation by SEC-HTTP-4 and approved by Ives).
