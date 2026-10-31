## Metadata

- **ID**: REQ-HTTP-020
- **Title**: Send a Content Security Policy that allows only the site's own origin
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-2; REQUIREMENTS.md NFR-3; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: Public responses MUST send a Content Security Policy that allows resources only from the site's own origin.
- **Rationale**: A CSP limits the damage of any content-injection gap and blocks third-party loads (SECURITY.md T-32, T-34; REF-XSS).
- **Assumptions**: None
- **Out of Scope**: Instagram origins in the policy (only if the feed model needs them; blocked by PQ-7); exact directive list (TO BE DECIDED, PQ-17).
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

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C Content Security Policy (header semantics); OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS`, `REF-ASVS-5` as cited in SECURITY.md (no ASVS identifier verified); W3C CSP defines the header.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the deployed site, when any public response is inspected, then it carries a CSP whose source lists allow only the site's own origin.
2. **AC-02 — Boundary or failure behavior**: Given a test page in a test deploy that references a script and an image from another origin, when it is loaded in a browser, then both are blocked and a CSP violation is reported in the console.
3. **AC-03 — Prohibited behavior**: Given the CSP, when it is reviewed, then it contains no wildcard source, no `unsafe-inline` for scripts and no `unsafe-eval`.

## Failure Behavior

- **On Invalid Input**: N/A: no input
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
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
- **Prohibited Approaches**: Report-only mode as the final setting; allowing third-party origins before PQ-7 is decided
- **Implementation Guidance**: Inline styles or scripts in templates need hashes or moving to files to fit the policy.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: A strict CSP must be fitted to the generator's output (inline styles/scripts) without weakening it.
- **Required Human Review**: Security review
- **Open Decisions**: Non-blocking: PQ-17 (exact directives). Blocked part in Out of Scope (PQ-7).
