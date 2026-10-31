## Metadata

- **ID**: REQ-CONTENT-080
- **Title**: Fail the publish for content files that are not Markdown, use front-matter fields outside the content model, or carry an unsafe slug
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-6 (content formats, front-matter fields, `security.allowContent`, slug characters), SEC-INPUT-3, `REF-HUGO-SEC`; REQUIREMENTS.md FR-4.7 (URL segment); ARCHITECTURE.md DR-3, DR-4; parent https://github.com/dalini/nina-website/issues/3

## Requirement

- **Statement**: Before any output is deployed, the Site Build MUST fail the publish, naming the file and field, when a Content Store content file is not Markdown, when its front matter holds a field that the content model in `dalini/nina-site` does not document, or when a slug taken from content (the Exhibition entry slug, FR-4.7) contains anything other than lowercase letters, digits and hyphens; the content model MUST NOT use a field that Hugo interprets itself to choose a page's URL, template, output or renderer (among others `url`, `aliases`, `layout`, `type`, `outputs`, `cascade`, `build`, `markup`), except `slug`, and Hugo's `security.allowContent` MUST keep its default.
- **Rationale**: Content must not control how the generator renders it: a direct write to the Content Store (T-49) could otherwise move an item to the Impressum's URL, make other URLs redirect to it, or emit raw HTML (T-77, T-76).
- **Assumptions**: None
- **Out of Scope**: Evaluation of generator syntax inside field values (REQ-OUTPUT-020); scripts in the generated pages (REQ-OUTPUT-040); generating the slug (REQ-EDIT-110); two entries with the same slug (REQ-CONTENT-090).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (rechecks content rules, DR-3); content model documented in `dalini/nina-site` (DR-4)
- **Security Traceability**: SEC-INPUT-6, SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Content-format and front-matter check run on the checkout before Hugo renders it
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-030, REQ-CONTENT-050
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-77, T-49, T-76; CWE-20, CWE-22
- **Abuse / Misuse Case**: A direct write with an editor token commits `url: /impressum/` or an HTML content file to take over a legal page or inject markup
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Content files and their front matter in the Content Store, whoever committed them
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Input Validation Cheat Sheet (`REF-INPUT`); Hugo front matter, content formats and security configuration (`REF-HUGO-SEC`)
- **Mapping Basis**: Other: `REF-INPUT` and `REF-HUGO-SEC` as cited in SECURITY.md SEC-INPUT-6.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given fixture content in which every file is Markdown and uses only documented front-matter fields, and an Exhibition entry with slug `faltungen-2025`, when the Site Build runs, then this check passes.
2. **AC-02 — Boundary or failure behavior**: Given a content file `impressum.html`, a Markdown file with `markup: html`, a work with `url: /impressum/`, a work with `aliases: [/kontakt/]`, a Text with `layout: raw` and a Page with `cascade:`, when the Site Build runs, then each fails the publish and names the file and field, and the live test site is unchanged.
3. **AC-03 — Boundary or failure behavior**: Given slugs `../impressum`, `a/b`, `Faltungen` and `faltungen_2025`, when the Site Build runs, then each fails the publish with the field `slug`.
4. **AC-04 — Prohibited behavior**: Given the Hugo configuration in `dalini/nina-site`, when it is reviewed, then `security.allowContent` keeps its default and no content-model field is one that Hugo interprets itself (`url`, `aliases`, `layout`, `type`, `outputs`, `cascade`, `build`, `markup`), except `slug`.
5. **AC-05 — Additional criterion**: Given a front-matter field that the content model documentation in `dalini/nina-site` does not list (e.g. `foo: bar` on a work), when the Site Build runs, then the publish fails and names the file and field.

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Allow-list of documented fields per content type; slug pattern; file-type classification by extension and front-matter `markup`
- **Integration Tests**: Build of a test content repository with each hostile fixture
- **Security Tests**: Reserved-field, HTML-content and path-traversal slug fixtures; review of the Hugo security configuration
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-080/AC-01`; AC-02 → `test:CONTENT-080/AC-02`; AC-03 → `test:CONTENT-080/AC-03`; AC-04 → `test:CONTENT-080/AC-04`; AC-05 → `test:CONTENT-080/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 (https://github.com/dalini/nina-website/issues/22), REQ-CONTENT-050 (https://github.com/dalini/nina-website/issues/20)
- **Downstream Requirements**: REQ-CONTENT-090 (https://github.com/dalini/nina-website/issues/26), REQ-EXHIBITIONS-030 (https://github.com/dalini/nina-website/issues/47)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known advisories (`REF-HUGO-ADV`, `REF-HUGO-SEC`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Derive the allow-list from the content model documentation so that the two cannot drift apart.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Generator-reserved fields and content formats are a Hugo-internals trust boundary (T-77); a gap lets a direct write take over the Impressum's URL or bring raw HTML into a page.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-57: the slug is generated from title and start year at first save (e.g. `faltungen-2025`) and never changes; duplicate slugs fail the publish, naming both entries (FR-4.7, SEC-INPUT-6). Slug generation is REQ-EDIT-110, the duplicate check REQ-CONTENT-090.
