## Metadata

- **ID**: REQ-OUTPUT-030
- **Title**: Allow only https: and mailto: links in content and open external links with noopener
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-OUT-2; REQUIREMENTS.md FR-4.3, FR-7.1, FR-7.2, FR-8.1; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: The Site Build MUST reject content links whose scheme is not `https:` or `mailto:` and MUST render every external link with `rel="noopener"`.
- **Rationale**: Other schemes can run script or mislead visitors; `noopener` keeps target pages from controlling the site's window (REF-XSS, REF-INPUT).
- **Assumptions**: None
- **Out of Scope**: Links inside Instagram content (PQ-7); exhibition external links (FR-4.3, blocked by PQ-11) follow this rule once that scope is unblocked.
- **Design Traceability**: DESIGN.md Components (Links)
- **Architecture Traceability**: Site Build; DR-3
- **Security Traceability**: SEC-OUT-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Rendering of link fields (Contact page, rich-text links, galerie burster link)
- **Actors**: Editor (author of content); Visitor (reader); Site Build
- **Preconditions**: REQ-CONTENT-010 and REQ-CONTENT-020 identify link fields
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation, Output Encoding
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v1 T-34, T-41; CWE-79, CWE-1022
- **Abuse / Misuse Case**: A content link uses `javascript:`, `data:` or `http:`, or a target page uses `window.opener` to redirect the site tab.
- **Trust Boundary**: TB-4 (Content Store → Site Build, DF-5) and TB-1 (output served to visitors)
- **Untrusted Inputs or Assertions**: Every editor-provided text field and link, legacy content (T-41)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: The Site Build enforces the rule regardless of what the Editing Interface accepted
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP XSS Prevention and Input Validation Cheat Sheets (`REF-XSS`, `REF-INPUT`)
- **Mapping Basis**: Other: `REF-XSS`, `REF-INPUT` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given content links with `https:` and `mailto:` schemes, when the Site Build runs, then they render as links and every external `https:` link carries `rel="noopener"`.
2. **AC-02 — Boundary or failure behavior**: Given a content link with scheme `http:`, `javascript:`, `data:` or `ftp:`, when the Site Build runs, then the build fails naming the item and field (SEC-INPUT-3).
3. **AC-03 — Prohibited behavior**: Given the generated site, when every `<a>` element pointing to another origin is inspected, then none lacks `noopener`.

## Failure Behavior

- **On Invalid Input**: Build fails with item and field; nothing published
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Links whose scheme cannot be parsed are rejected
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing (SEC-INPUT-3)
- **Logging / Audit**: Build log names item and field of rejected content; never logs the rejected markup in full
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Scheme allow-list including mixed case and leading whitespace (`JavaScript:`, ` javascript:`)
- **Integration Tests**: Build with link fixtures in every link field
- **Security Tests**: Scheme-bypass corpus (case, whitespace, entity-encoded colon); output scan for external links without `noopener`
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-030/AC-01`; AC-02 → `test:OUTPUT-030/AC-02`; AC-03 → `test:OUTPUT-030/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); fixtures with hostile content

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: REQ-CONTACT-010 ({{ISSUE_URL:REQ-CONTACT-010}}), REQ-INSTAGRAM-010 ({{ISSUE_URL:REQ-INSTAGRAM-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Fixing schemes by rewriting them silently; allow-listing by substring match
- **Implementation Guidance**: Parse URLs with the platform's URL parser before checking the scheme.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Small allow-list with a known bypass corpus; well specified by SEC-OUT-2.
- **Required Human Review**: Security review
- **Open Decisions**: Non-blocking: PQ-16.
