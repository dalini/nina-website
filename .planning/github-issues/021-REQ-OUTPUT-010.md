## Metadata

- **ID**: REQ-OUTPUT-010
- **Title**: HTML-encode editor text and render rich text only from the restricted format
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-OUT-1, `REF-XSS`; REQUIREMENTS.md FR-1.4, FR-5.1, FR-3.7; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: The Site Build MUST HTML-encode all editor-provided text by default. Rich text MUST be rendered only in Text bodies, Page content, News text, a work's short text and the CV bio, and only with paragraphs, headings h2 and h3, bold, italic, links, lists, block quotes and, in Text bodies only, images from the Content Store; every other field is plain text. Raw HTML, scripts, inline event handlers, iframes and `javascript:` URLs in content MUST be removed or rejected.
- **Rationale**: Editor content is untrusted input to every page (T-5, T-41); a restricted format keeps script out of the site.
- **Assumptions**: None
- **Out of Scope**: Link schemes and `noopener` (REQ-OUTPUT-030); generator syntax in content (REQ-OUTPUT-020); the Editing Interface's own display (REQ-EDIT-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (templates only render, DR-3)
- **Security Traceability**: SEC-OUT-1

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Every template that renders editor content
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding, Sanitization
- **Threat References**: STRIDE Tampering / Elevation of Privilege: SECURITY.md Threat Model v2 T-5, T-41; CWE-79
- **Abuse / Misuse Case**: Text containing `<script>`, `<img onerror=…>` or a Markdown link to `javascript:alert(1)` is entered or imported to run script in visitors' browsers
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Every file and field in the Content Store checkout, including direct writes that bypassed the Editing Interface (T-49)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS` as cited in SECURITY.md for this rule.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a Text body with a paragraph, an h2, bold, italic, a list, a block quote, an `https:` link and a Content Store image, when the build runs, then each renders as the matching element.
2. **AC-02 — Boundary or failure behavior**: Given content fields containing `<script>`, `onerror=` attributes, `<iframe>` and `javascript:` links, when the build runs, then the output contains none of them as markup.
3. **AC-03 — Boundary or failure behavior**: Given a plain-text field (e.g. materials) containing `<b>x</b>`, when its page is generated, then the text appears encoded as `&lt;b&gt;x&lt;/b&gt;`.
4. **AC-04 — Prohibited behavior**: Given rich text with an h1, a table or an image in a News text, when the build runs, then none is rendered as that element; images appear only in Text bodies.
5. **AC-05 — Additional criterion**: Given an image reference in a Text body that points outside the Content Store, when the build runs, then no external image is referenced in the output (SEC-PRIV-1).

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: Encoding helper; allowed-element renderer per field type
- **Integration Tests**: Build with the hostile fixture corpus across every page type
- **Security Tests**: Hostile corpus (script tags, event handlers, `javascript:` variants with case and whitespace tricks, SVG and iframe markup); output scan
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-010/AC-01`; AC-02 → `test:OUTPUT-010/AC-02`; AC-03 → `test:OUTPUT-010/AC-03`; AC-04 → `test:OUTPUT-010/AC-04`; AC-05 → `test:OUTPUT-010/AC-05` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-050 ({{ISSUE_URL:REQ-CONTENT-050}})
- **Downstream Requirements**: REQ-NAV-030 ({{ISSUE_URL:REQ-NAV-030}}), REQ-WORKS-020 ({{ISSUE_URL:REQ-WORKS-020}}), REQ-EXHIBITIONS-010 ({{ISSUE_URL:REQ-EXHIBITIONS-010}}), REQ-NEWS-010 ({{ISSUE_URL:REQ-NEWS-010}}), REQ-CV-010 ({{ISSUE_URL:REQ-CV-010}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}}), REQ-LEGAL-010 ({{ISSUE_URL:REQ-LEGAL-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Hugo's Markdown renderer with raw HTML disabled plus render hooks for links, images and headings is one way; verify with the hostile corpus rather than relying on defaults.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Security-critical encoding and sanitizing that needs careful use of the renderer and a hostile test corpus.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
