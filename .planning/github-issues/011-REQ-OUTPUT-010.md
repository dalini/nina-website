## Metadata

- **ID**: REQ-OUTPUT-010
- **Title**: HTML-encode editor text and render rich text from a restricted format
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-OUT-1; REQUIREMENTS.md FR-1.4, FR-5.1, FR-10.1; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: The Site Build MUST HTML-encode all editor-provided text by default and MUST render rich-text fields (Text bodies, Page content, News text, work short text, CV bio) from a restricted format allowing only paragraphs, headings h2–h3, bold, italic, links, lists, block quotes and, in Text bodies only, images from the Content Store, removing or rejecting raw HTML, scripts, inline event handlers, iframes and `javascript:` URLs.
- **Rationale**: Stored content is untrusted; script in it would run for every visitor (SECURITY.md T-5, T-41; REF-XSS).
- **Assumptions**: None
- **Out of Scope**: Encoding in the Editing Interface (SEC-OUT-4) and Instagram captions (SEC-OUT-3), both decomposed in the step 9 re-run; client-side insertion (SEC-OUT-5; small first-party JavaScript is allowed, ARCHITECTURE.md).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build templates only render (DR-3)
- **Security Traceability**: SEC-OUT-1

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Rendering of every text and rich-text field on every page type
- **Actors**: Editor (author of content); Visitor (reader); Site Build
- **Preconditions**: REQ-CONTENT-010 and REQ-CONTENT-020 identify the text and rich-text fields
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding, Sanitization
- **Threat References**: STRIDE Tampering / Elevation of Privilege: SECURITY.md Threat Model v1 T-5, T-34, T-41; CWE-79
- **Abuse / Misuse Case**: An editor account or legacy import stores `<script>`, `onerror=` or `javascript:` content that would run in visitors' browsers.
- **Trust Boundary**: TB-4 (Content Store → Site Build, DF-5) and TB-1 (output served to visitors)
- **Untrusted Inputs or Assertions**: Every editor-provided text field and link, legacy content (T-41)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: The Site Build enforces the rule regardless of what the Editing Interface accepted
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`, unversioned web resource cited by SECURITY.md)
- **Mapping Basis**: Other: OWASP XSS Prevention Cheat Sheet (`REF-XSS`) as cited in SECURITY.md; no versioned control identifiers assigned.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a plain-text field containing `<b>Bronze & Stahl</b>`, when the Site Build renders it, then the page shows the characters literally and the HTML contains them encoded.
2. **AC-02 — Expected behavior**: Given a rich-text field using headings, emphasis, links and lists, when the Site Build renders it, then those elements appear in the page.
3. **AC-03 — Boundary or failure behavior**: Given rich-text content containing `<script>`, an `onclick=` attribute, an `<iframe>` and a `javascript:` link, when the Site Build runs, then either the build fails naming the item and field or the output contains none of these constructs.
4. **AC-04 — Prohibited behavior**: Given the hostile fixtures above, when the generated pages are loaded in a browser, then no script from content executes.

## Failure Behavior

- **On Invalid Input**: Removed or rejected per SEC-OUT-1; if rejected, the build fails (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Content the sanitizer cannot parse is rejected (build fails), never passed through
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing (SEC-INPUT-3)
- **Logging / Audit**: Build log names item and field of rejected content; never logs the rejected markup in full
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Encoding of `< > & " '` in every text-field helper; sanitizer allow-list for the restricted format
- **Integration Tests**: Full build with hostile fixtures in every page type
- **Security Tests**: XSS payload corpus (script tags, event handlers, `javascript:` and `data:` URLs, SVG-in-HTML, malformed tags) in every text and rich-text field; browser check that nothing executes
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-010/AC-01`; AC-02 → `test:OUTPUT-010/AC-02`; AC-03 → `test:OUTPUT-010/AC-03`; AC-04 → `test:OUTPUT-010/AC-04`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build); fixtures with hostile content

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: REQ-CV-010 ({{ISSUE_URL:REQ-CV-010}}), REQ-LEGAL-010 ({{ISSUE_URL:REQ-LEGAL-010}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}}), REQ-WORKS-020 ({{ISSUE_URL:REQ-WORKS-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added; DEP-1: use a vetted HTML sanitizer or the generator's built-in safe rendering, not custom sanitizing code
- **Prohibited Approaches**: Disabling the generator's auto-escaping; marking content as safe HTML; regex-based HTML sanitizing
- **Implementation Guidance**: Prefer a rich-text format that cannot express raw HTML; keep one rendering helper per field kind.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Security-critical sanitizing and escaping; needs careful use of a vetted sanitizer and a hostile test corpus.
- **Required Human Review**: Security review
- **Open Decisions**: None (step 10: Hugo; allowed elements and rich-text fields as in SECURITY.md SEC-OUT-1).
