## Metadata

- **ID**: REQ-EXHIBITIONS-030
- **Title**: Give each Exhibition entry its own page with its facts, linked works, description and installation images, and link it from the list
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.7, FR-4.2, FR-4.3, FR-4.5, FR-3.7, FR-1.3; DESIGN.md Components (Exhibition entry page, Image with credit); parent https://github.com/dalini/nina-website/issues/8

## Requirement

- **Statement**: Each published Exhibition entry MUST have its own page at `/exhibitions/<slug>/` (German `/de/ausstellungen/<slug>/`) showing the fields of FR-4.2 and FR-4.3 (dates as in FR-1.8), its linked published works as thumbnails in the works grid linking to their work pages, and, when entered, its description (plain text in German and English) and its installation images with alt text, caption and credit; the Exhibitions/News list MUST link each entry's title to its page.
- **Rationale**: Work pages and the list need a target for each entry (FR-3.9, FR-4.7); the page carries the installation views and the text about the exhibition.
- **Assumptions**: Every published entry has a slug that passed REQ-CONTENT-080
- **Out of Scope**: Generating the slug (REQ-EDIT-110) and failing the publish for two entries with the same slug (REQ-CONTENT-090); formatting the dates (REQ-NAV-040); the list entries themselves (REQ-EXHIBITIONS-010); the link from work pages (REQ-WORKS-040).
- **Design Traceability**: DESIGN.md Components (Exhibition entry page: title semibold, then type, venue, city and dates in `secondary`; description in body text; installation images as Image with credit; linked works as thumbnails in the works grid; Interface labels for types); Layout and Spacing (Grid); Accessibility; `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (a page per Exhibition entry); Public Site
- **Security Traceability**: SEC-OUT-1 (description plain text), SEC-INPUT-4 (unpublished items), SEC-INPUT-6 (slug)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Exhibition entry page (EN and DE); title links on the Exhibitions/News page
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-EXHIBITIONS-010, REQ-WORKS-020, REQ-IMAGES-010, REQ-CONTENT-080, REQ-CONTENT-090
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding
- **Threat References**: N/A: rendering risks are handled by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-1 (static pages served to visitors)
- **Untrusted Inputs or Assertions**: Editor content rendered on the page (encoded by REQ-OUTPUT-010)
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

1. **AC-01 — Expected behavior**: Given a published entry with slug `faltungen-2025`, two linked published works, a description and two installation images, when the site is built, then `/exhibitions/faltungen-2025/` and `/de/ausstellungen/faltungen-2025/` show its title, type, venue, city, dates, the description, the installation images with alt text, caption and credit, and both works as thumbnails linking to their work pages.
2. **AC-02 — Boundary or failure behavior**: Given an entry without co-exhibitors, external link, description and installation images, when its page is generated, then no label or empty element for them appears.
3. **AC-03 — Boundary or failure behavior**: Given the Exhibitions/News page, when it is generated, then each entry's title links to its own page in the same language, and the language switch on an entry page opens the same entry in the other language.
4. **AC-04 — Prohibited behavior**: Given an entry linked to an unpublished work, and an unpublished entry, when the site is built, then the unpublished work is not shown or linked, and no page exists for the unpublished entry.
5. **AC-05 — Additional criterion**: Given a published entry from 2026-03-12 to 2026-05-03, when its pages are generated, then the English page shows `2026-03-12 – 2026-05-03` and the German page `12.03.2026 – 03.05.2026` (FR-1.8, REQ-NAV-040).

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

- **Unit Tests**: Optional-field handling; thumbnail list from linked published works only; URL per language
- **Integration Tests**: Build of a fixture set with full and minimal entries; link check between list, entry pages and work pages
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EXHIBITIONS-030/AC-01`; AC-02 → `test:EXHIBITIONS-030/AC-02`; AC-03 → `test:EXHIBITIONS-030/AC-03`; AC-04 → `test:EXHIBITIONS-030/AC-04`; AC-05 → `test:EXHIBITIONS-030/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-EXHIBITIONS-010 (https://github.com/dalini/nina-website/issues/46), REQ-WORKS-020 (https://github.com/dalini/nina-website/issues/36), REQ-IMAGES-010 (https://github.com/dalini/nina-website/issues/37), REQ-CONTENT-080 (https://github.com/dalini/nina-website/issues/25), REQ-CONTENT-090 (https://github.com/dalini/nina-website/issues/26)
- **Downstream Requirements**: REQ-WORKS-040 (https://github.com/dalini/nina-website/issues/48), REQ-PERF-010 (https://github.com/dalini/nina-website/issues/72)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Template work over fields the model already holds, following the DESIGN.md Exhibition entry page component.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-57: slug from title and start year, never changed, duplicates fail the publish (REQ-EDIT-110, REQ-CONTENT-090); PQ-65: numeric dates (FR-1.8, REQ-NAV-040).
