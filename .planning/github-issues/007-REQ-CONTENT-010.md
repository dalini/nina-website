## Metadata

- **ID**: REQ-CONTENT-010
- **Title**: Define the Work and Image content model with its required-field rules
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.4, FR-1.5, FR-3.2 to FR-3.8, FR-10.3, FR-10.6; ARCHITECTURE.md Data model expectations, DR-3, DR-4; parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The content model MUST define Work and Image, with Work holding title, year, materials, dimensions (two or three values in centimeters), optional edition, optional series, optional short text, ordered images, a featured flag, an in-public-space flag and a publish state, and Image holding the file, alt text and an optional photo credit; it MUST mark work title, year, at least one image and image alt text as required, and MUST be documented in the repository in a portable format.
- **Rationale**: DR-3 requires content rules to be defined once for the content model; DR-4 requires a documented, portable format so the editing system or generator can be replaced.
- **Assumptions**: FR-1.4 is read as written: every text field of Work and Image (materials, edition, series, short text, alt text, photo credit) holds a German and an English value; the work title holds a single value (FR-1.5).
- **Out of Scope**: Exhibition/News entries and work–exhibition links (FR-3.9, FR-4.5; blocked by PQ-11); whether materials and dimensions are required (blocked by PQ-13); empty-language behavior (FR-1.7, PQ-9); enforcement in the Editing Interface (PQ-1).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (owns Work, Image, featured and public-space flags, image order, alt text and credits); DR-2, DR-3, DR-4
- **Security Traceability**: SEC-INPUT-3 (the Site Build recheck uses these rules, REQ-CONTENT-030)

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Content model documentation and the validation rule set used by the Site Build
- **Actors**: Editor (indirectly, through stored content); Site Build
- **Preconditions**: None
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation, Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v1 T-12 (direct writes bypassing the Editing Interface checks)
- **Abuse / Misuse Case**: Content that skips required fields is written to the store directly (T-12) or by an import (T-41).
- **Trust Boundary**: TB-4 (Content Store → Site Build, DF-5)
- **Untrusted Inputs or Assertions**: Every content file read from the Content Store, including legacy imports (DF-3)
- **Authoritative Enforcement Point**: Site Build (recheck, DR-3); the Editing Interface check is blocked scope (PQ-1)
- **Independent Verification**: The Site Build validates content independently of the Editing Interface that wrote it (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Data-integrity rules only; no standard control mapped.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the documented content model, when it is reviewed against FR-3.2 to FR-3.8, FR-10.3 and FR-10.6, then every listed Work and Image field exists with its language rule (bilingual except the work title) and the four required-field rules of FR-10.6 are marked.
2. **AC-02 — Boundary or failure behavior**: Given a Work fixture with dimensions of one value or four values, when it is validated against the model, then validation reports a violation naming the work and the field `dimensions`; with two or three values it passes.
3. **AC-03 — Boundary or failure behavior**: Given Work fixtures missing the title, the year, all images, or one image's alt text, when each is validated, then each reports exactly one violation naming the work and the missing field.
4. **AC-04 — Prohibited behavior**: Given the content model, when it is inspected, then the work title has no per-language variant and no field depends on a specific editing system or generator (DR-4).

## Failure Behavior

- **On Invalid Input**: Validation returns every violation with item and field; the content is not changed
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A: the Site Build only reads content
- **On Security-Decision Failure**: Content that cannot be parsed counts as invalid (deny by default)
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build stops with a failure and writes no deployable output (SEC-INPUT-3)
- **Logging / Audit**: Build log: content type, item identifier and field for each violation; no content values beyond the identifier; no secrets
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Validation of each required field, dimension count 2 and 3 (pass) and 1 and 4 (fail), optional fields absent (pass), bilingual fields with one language empty (pass at model level)
- **Integration Tests**: Load fixture content in the chosen storage format and validate it with the same rule set the Site Build uses
- **Security Tests**: Malformed content file (unparseable) is rejected without crashing the validator
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-010/AC-01`; AC-02 → `test:CONTENT-010/AC-02`; AC-03 → `test:CONTENT-010/AC-03`; AC-04 → `test:CONTENT-010/AC-04`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1)

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}}), REQ-OUTPUT-020 ({{ISSUE_URL:REQ-OUTPUT-020}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}}), REQ-WORKS-010 ({{ISSUE_URL:REQ-WORKS-010}}), REQ-WORKS-020 ({{ISSUE_URL:REQ-WORKS-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: content storage format: TO BE DECIDED (PQ-18, ARCHITECTURE.md Data model expectations, DR-4); DEP-1 to DEP-8 apply to every dependency added; DR-4 (portable, documented)
- **Prohibited Approaches**: Rules defined in templates or in the visitor's browser (DR-3); fields tied to one editing system's or generator's format (DR-4)
- **Implementation Guidance**: Record the content model where the maintainers keep the code (location TO BE DECIDED with PQ-18), so REQ-CONTENT-030 and the editing system can share one rule set.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Sets the shared rule set and portable format every other issue reads; a modelling mistake here spreads to all page and validation issues.
- **Required Human Review**: Architecture review (DR-3, DR-4); product review by Nina of field names shown in error messages
- **Open Decisions**: Non-blocking: PQ-16 (generator), PQ-18 (storage format), PQ-24 (format of `year`).
