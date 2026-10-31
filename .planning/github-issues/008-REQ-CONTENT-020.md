## Metadata

- **ID**: REQ-CONTENT-020
- **Title**: Define the Text, CV entry, Download and Page content models
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.4, FR-5.1, FR-5.2, FR-6.1, FR-6.2, FR-7.1, FR-7.2, FR-9.1, FR-10.1; ARCHITECTURE.md Data model expectations, DR-2, DR-3, DR-4; parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The content model MUST define Text (title, body, optional year, manual list position, publish state), CV entry (one of the six FR-5.2 sections, year (required except in the short bio section), text, publish state), Download (title, PDF file, publish state) and the Pages Contact (content, Nina's email address, galerie burster link), Impressum and Datenschutzerklärung (content), with every text field holding a German and an English value, documented in the same portable format as REQ-CONTENT-010.
- **Rationale**: DR-2 makes the Content Store the only owner of content that editors change; DR-3 and DR-4 require one documented model.
- **Assumptions**: None. CV entries are content items of their own; the derived CV lines (FR-5.3, FR-5.4) are decomposed in the step 9 re-run.
- **Out of Scope**: Home page content (FR-2); Exhibition entries and News items (FR-4); derived CV lines and their overrides (FR-5.3, FR-5.4); build failure on a missing Contact email or legal Page (FR-10.6); empty-language behavior (FR-1.7). All decided in step 10 and decomposed in the step 9 re-run.
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (owns Text, CV entry, Download, Page); DR-2, DR-3, DR-4
- **Security Traceability**: SEC-OUT-2 (the Contact page links are checked by REQ-OUTPUT-030); SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Content model documentation and validation rule set
- **Actors**: Editor (indirectly, through stored content); Site Build
- **Preconditions**: REQ-CONTENT-010 defines the documentation format
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation, Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v1 T-12
- **Abuse / Misuse Case**: A Page or Text is written with structure the model does not allow (T-12, T-41).
- **Trust Boundary**: TB-4 (Content Store → Site Build, DF-5)
- **Untrusted Inputs or Assertions**: Every content file read from the Content Store, including legacy imports (DF-3)
- **Authoritative Enforcement Point**: Site Build (recheck, DR-3); the Editing Interface check (Sveltia CMS, FR-10.6) is decomposed in the step 9 re-run
- **Independent Verification**: The Site Build validates content independently of the Editing Interface that wrote it (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Data-integrity rules only; no standard control mapped.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the documented content model, when it is reviewed against FR-5.1, FR-5.2, FR-6.1, FR-6.2, FR-7.1, FR-7.2 and FR-9.1, then Text, CV entry, Download and the three Pages exist with the listed fields, and every text field is bilingual.
2. **AC-02 — Boundary or failure behavior**: Given a CV entry fixture whose section is not one of short bio, education, solo exhibitions, works in public space, group exhibitions, awards and grants, when it is validated, then validation reports a violation naming the entry and the field `section`.
3. **AC-03 — Boundary or failure behavior**: Given a Download fixture whose file is not a PDF, when it is validated, then validation reports a violation naming the download and its file field.
4. **AC-04 — Prohibited behavior**: Given the content model, when it is inspected, then Nina's email address and the galerie burster link are content fields of the Contact Page and not template values (DR-2).

## Failure Behavior

- **On Invalid Input**: Validation returns every violation with item and field; content is not changed
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A: the Site Build only reads content
- **On Security-Decision Failure**: Unparseable content counts as invalid
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build stops with a failure and writes no deployable output (SEC-INPUT-3)
- **Logging / Audit**: Build log: content type, item identifier and field for each violation; no content values beyond the identifier; no secrets
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Section values (six valid, one invalid); Download file type check by content, not only by extension; bilingual field presence
- **Integration Tests**: Validate fixture Text, CV, Download and Page files with the shared rule set
- **Security Tests**: A Download fixture that is an HTML file renamed `.pdf` is rejected
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-020/AC-01`; AC-02 → `test:CONTENT-020/AC-02`; AC-03 → `test:CONTENT-020/AC-03`; AC-04 → `test:CONTENT-020/AC-04`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build)

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}})
- **Downstream Requirements**: REQ-CONTACT-010 ({{ISSUE_URL:REQ-CONTACT-010}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-CV-010 ({{ISSUE_URL:REQ-CV-010}}), REQ-DOWNLOADS-010 ({{ISSUE_URL:REQ-DOWNLOADS-010}}), REQ-LEGAL-010 ({{ISSUE_URL:REQ-LEGAL-010}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}}), REQ-OUTPUT-020 ({{ISSUE_URL:REQ-OUTPUT-020}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: content storage format: Markdown with YAML front matter in `dalini/nina-content` (ARCHITECTURE.md Data model expectations, DR-4); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Content that editors change kept in templates (DR-2)
- **Implementation Guidance**: Reuse the documentation format and validator structure of REQ-CONTENT-010.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Follows the pattern REQ-CONTENT-010 sets; field lists come straight from FR-5 to FR-7.
- **Required Human Review**: Architecture review (DR-3, DR-4); product review by Nina of field names shown in error messages
- **Open Decisions**: None (step 10: rich text only in Text bodies, Page content, News text, work short text and CV bio (SEC-OUT-1); no fixed list of PDFs (FR-6.1); CV structure FR-5.2 to FR-5.4).
