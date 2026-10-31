## Metadata

- **ID**: REQ-CONTENT-090
- **Title**: Fail the publish when two Exhibition entries share a slug, naming both entries and the slug field
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.7 (two entries with the same slug fail the publish, naming both entries and the slug field), FR-10.8; SECURITY.md SEC-INPUT-6 (the Site Build fails the publish when two entries share a slug), SEC-INPUT-3; ARCHITECTURE.md Site Build (rechecks unique Exhibition slugs); step 10 a third time, PQ-57; parent https://github.com/dalini/nina-website/issues/3

## Requirement

- **Statement**: Before any output is deployed, the Site Build MUST fail the publish and publish nothing when two or more Exhibition entries have the same slug, published or not (FR-4.7 names no publish state; the preview builds unpublished entries, FR-10.9), and MUST name every entry that shares it and the field `slug` in the failure, so that the publish status (FR-10.8) can show it to Nina; the previous site stays online.
- **Rationale**: Each Exhibition entry has its own page at `/exhibitions/<slug>/` (FR-4.7); two entries with one slug would silently replace each other's page.
- **Assumptions**: None
- **Out of Scope**: Generating the slug (REQ-EDIT-110); the slug's character rule (REQ-CONTENT-080); a missing slug (REQ-CONTENT-050); showing the failure to Nina (REQ-EDIT-090).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (rechecks content rules, including unique Exhibition slugs; fails the publish on invalid content); DR-3
- **Security Traceability**: SEC-INPUT-6, SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Uniqueness check over the slugs of all Exhibition entries in the checkout, run before Hugo renders it
- **Actors**: Editors (authors of content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-080
- **Data Classification**: Internal
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation, Business-Rule Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-77 (content controlling the page URL); CWE-694
- **Abuse / Misuse Case**: A direct write gives a new entry the slug of an existing one so that its page replaces the other entry's page
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: The slug fields of Exhibition entries, whoever committed them
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the Editing Interface (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Input Validation Cheat Sheet (`REF-INPUT`)
- **Mapping Basis**: Other: `REF-INPUT` as cited in SECURITY.md SEC-INPUT-6.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given fixture content with Exhibition entries `faltungen-2025` and `faltungen-2026`, when the Site Build runs, then this check passes and both pages are generated.
2. **AC-02 — Boundary or failure behavior**: Given two published entries with the slug `faltungen-2025`, when the Site Build runs, then the publish fails, the failure names both entries and the field `slug`, nothing is deployed and the live test site is unchanged.
3. **AC-03 — Boundary or failure behavior**: Given a published and an unpublished entry with the same slug, or three entries with the same slug, when the Site Build runs, then the publish fails and names every entry that shares the slug.
4. **AC-04 — Prohibited behavior**: Given two entries with the same slug, when the production and the preview build run, then neither generates a page for that slug in which one entry has replaced the other.

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the entries and the field `slug`, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the entries' identifiers and the field; no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Uniqueness check over slugs: none, two, three equal; published and unpublished mixed
- **Integration Tests**: Build of a test content repository with a duplicate slug: build fails, publish status names both entries
- **Security Tests**: Direct-write fixture that copies an existing entry's slug
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-090/AC-01`; AC-02 → `test:CONTENT-090/AC-02`; AC-03 → `test:CONTENT-090/AC-03`; AC-04 → `test:CONTENT-090/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-080 (https://github.com/dalini/nina-website/issues/25)
- **Downstream Requirements**: REQ-EXHIBITIONS-030 (https://github.com/dalini/nina-website/issues/47)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online
## Implementation Notes

- **Constraints**: Runs in the content-check stage before Hugo, without access to the deploy keys or the Instagram token (SEC-CICD-6); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Renaming or suffixing a slug in the build to make it unique (the slug never changes after the first save, FR-4.7); checks that run only in the Editing Interface (DR-3)
- **Implementation Guidance**: Report the failure in the same item-and-field form as the other content checks, so that REQ-PIPELINE-120 and REQ-EDIT-090 show it without changes.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A small, fully specified uniqueness check in an existing fail-closed stage.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-57: duplicate slugs fail the publish, naming both entries and the slug field (FR-4.7, SEC-INPUT-6).
