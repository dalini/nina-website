## Metadata

- **ID**: REQ-INSTAGRAM-020
- **Title**: Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-2.4, FR-8.2, FR-8.4, FR-8.6; ARCHITECTURE.md DR-5; SECURITY.md SEC-OUT-3, SEC-PRIV-3; DESIGN.md Components (Instagram posts); FR-8.7; parent {{ISSUE_URL:REQ-INSTAGRAM-000}}

## Requirement

- **Statement**: The home page MUST show, below the featured image and the Exhibition entry (when shown), the 6 most recent posts from the Site Build's neutral post list (image, caption, link, date) that are not hidden, each image self-hosted, linking to its post and with the alt text of FR-8.7 (the first 120 characters of the caption; without a caption "Instagram post by @ninalaaf, <date>" / "Instagram-Beitrag von @ninalaaf, <Datum>"), followed by the @ninalaaf link; when the feed is switched off or no posts exist, the home page MUST render and show only the link.
- **Rationale**: Recent posts keep the home page current (FR-8.2); Nina can hide posts or switch the feed off (FR-8.6, T-36).
- **Assumptions**: None
- **Out of Scope**: Fetching and checking posts (REQ-INSTAGRAM-030); reuse after a failed fetch (REQ-INSTAGRAM-040).
- **Design Traceability**: DESIGN.md Components (Instagram posts: up to 6 images in the works grid, then the @ninalaaf link); Layout and Spacing (Grid); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (consumes only the neutral post list, DR-5); Public Site
- **Security Traceability**: SEC-OUT-3 (captions encoded as untrusted text), SEC-PRIV-3 (images self-hosted)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Home page Instagram section
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding
- **Threat References**: STRIDE Spoofing / Tampering: SECURITY.md Threat Model v3 T-36, T-38; CWE-79
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-1 (static pages served to visitors)
- **Untrusted Inputs or Assertions**: Captions and links in the post list, which come from Instagram
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: N/A
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a post list of 9 posts with 2 hidden, when the home page is generated, then it shows the 6 most recent non-hidden posts in the works grid, each linking to its post, then the @ninalaaf link.
2. **AC-02 — Boundary or failure behavior**: Given the feed switch is off, or the post list is empty, when the home page is generated, then the build succeeds and the section shows only the @ninalaaf link.
3. **AC-03 — Boundary or failure behavior**: Given a caption containing `<script>alert(1)</script>`, when the home page is generated, then it appears only as encoded text.
4. **AC-04 — Prohibited behavior**: Given the home page, when its HTML and network requests are inspected, then no image or other resource is referenced from an Instagram or Meta host.
5. **AC-05 — Additional criterion**: Given a post with a 150-character caption and a post without caption, when the home page is generated, then the first image's alt text is the caption's first 120 characters and the second's is the FR-8.7 text with the post's date, in English on the English page and in German on the German page.

## Failure Behavior

- **On Invalid Input**: Content that violates the content model fails the build (REQ-CONTENT-030); nothing is published
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the failing content item and field; it contains no secrets (SEC-SECRET-1)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Selection of the 6 most recent non-hidden posts; feed switch
- **Integration Tests**: Build with fixture post lists (full, partly hidden, empty, switched off)
- **Security Tests**: Caption and link fixtures with markup and non-https links (left out upstream by REQ-INSTAGRAM-030); network recording
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:INSTAGRAM-020/AC-01`; AC-02 → `test:INSTAGRAM-020/AC-02`; AC-03 → `test:INSTAGRAM-020/AC-03`; AC-04 → `test:INSTAGRAM-020/AC-04`; AC-05 → `test:INSTAGRAM-020/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-060 ({{ISSUE_URL:REQ-CONTENT-060}}), REQ-HOME-020 ({{ISSUE_URL:REQ-HOME-020}}), REQ-INSTAGRAM-010 ({{ISSUE_URL:REQ-INSTAGRAM-010}})
- **Downstream Requirements**: REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}}), REQ-INSTAGRAM-030 ({{ISSUE_URL:REQ-INSTAGRAM-030}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Rendering from a neutral list with a small selection rule; the risky fetch is a separate issue.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: PQ-65 (non-blocking): the date format in the fallback alt text.
