## Metadata

- **ID**: REQ-HTTP-020
- **Title**: Send a Content Security Policy that allows only the site's own origin
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-2; REQUIREMENTS.md NFR-3; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: Public responses MUST send the Content Security Policy `default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; font-src 'self'; base-uri 'self'; form-action 'none'; frame-ancestors 'none'`.
- **Rationale**: A CSP limits the damage of any content-injection gap and blocks third-party loads (SECURITY.md T-32, T-34; REF-XSS).
- **Assumptions**: None
- **Out of Scope**: The CSP of `edit.nina-laaf.de` (Sveltia CMS) and the preview site: decomposed in the step 9 re-run. No Instagram origin is needed (posts are self-hosted, SEC-PRIV-3).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting, Public Site; DR-6
- **Security Traceability**: SEC-HTTP-2

## Scope

- **Applies To**: Web Client
- **Components**: Static Hosting, Public Site
- **Interfaces / Operations**: CSP header on every public response
- **Actors**: Visitor; network attacker
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Privacy
- **Control Layers**: Architecture, Output Encoding
- **Threat References**: STRIDE Tampering / Information Disclosure: SECURITY.md Threat Model v1 T-32, T-34; CWE-79
- **Abuse / Misuse Case**: Injected markup tries to load a script or image from another origin.
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Any resource reference in generated pages
- **Authoritative Enforcement Point**: Static Hosting configuration
- **Independent Verification**: Automated checks against the deployed site, independent of the hosting configuration
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C Content Security Policy (header semantics); OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS`, `REF-ASVS-5` as cited in SECURITY.md (no ASVS identifier verified); W3C CSP defines the header.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the deployed site, when any public response is inspected, then it carries exactly the CSP of SECURITY.md SEC-HTTP-2.
2. **AC-02 — Boundary or failure behavior**: Given a test page in a test deploy that references a script and an image from another origin, when it is loaded in a browser, then both are blocked and a CSP violation is reported in the console.
3. **AC-03 — Prohibited behavior**: Given the CSP, when it is reviewed, then it contains no wildcard source, no `unsafe-inline` for scripts and no `unsafe-eval`.

## Failure Behavior

- **On Invalid Input**: N/A: no input
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Missing or weaker CSP fails the header check in CI and blocks the release
- **On External Dependency Failure**: N/A
- **On System Error**: If the post-deploy check fails, the deploy is reported as failed
- **Logging / Audit**: Post-deploy check report: URL, expected and observed header values
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Header check in CI on every page type; browser test with the cross-origin test page
- **Security Tests**: CSP evaluation against the rule; browser test for blocked cross-origin loads
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-020/AC-01`; AC-02 → `test:HTTP-020/AC-02`; AC-03 → `test:HTTP-020/AC-03`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Deployed test site on the VPS (nginx); HTTP client for header checks

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: VPS with nginx (ARCHITECTURE.md Static Hosting)
- **Dependency Assumptions**: The host applies configured headers to every response, including error pages
- **Failure Impact**: Headers missing on some responses weakens the protection for those responses

## Implementation Notes

- **Constraints**: Host: VPS with nginx (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Report-only mode as the final setting; allowing any third-party origin
- **Implementation Guidance**: Inline styles or scripts in templates need hashes or moving to files to fit the policy.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: A strict CSP must be fitted to the generator's output (inline styles/scripts) without weakening it.
- **Required Human Review**: Security review
- **Open Decisions**: None (step 10: exact directives in SECURITY.md SEC-HTTP-2).
