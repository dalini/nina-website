## Metadata

- **ID**: REQ-CONTENT-010
- **Title**: Define the Work and Image content model with its required-field rules
- **Version**: 0.4.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.4, FR-1.5, FR-3.1 to FR-3.8, FR-3.10, FR-5.4, FR-10.3, FR-10.6; ARCHITECTURE.md Data model expectations, Content Store, DR-2 to DR-4; parent https://github.com/dalini/nina-website/issues/3

## Requirement

- **Statement**: The content model MUST define Work and Image items as Markdown files with YAML front matter in `dalini/nina-content`, documented in `dalini/nina-site`, with: a work title held once and never translated; a year that is a single four-digit year; materials; dimensions holding two or three values in centimeters; edition; series; short text; an in-public-space flag; a manual position in the Works order; a publish state; a way to mark exactly one work as featured; a CV hide flag and a CV wording override for the work's derived works-in-public-space line; and an ordered list of images, each with alt text, caption and photo credit. Every text field except the title MUST hold a German and an English value. The model MUST define these rules: a work needs a title, a year and at least one image; an image needs alt text in at least one language; the caption is optional plain text; exactly one published work is featured, and where the Editing Interface cannot keep it so, the publish fails and names the works and the field (FR-10.3, FR-10.6).
- **Rationale**: One portable, documented model lets the Editing Interface check and the Site Build recheck the same rules (DR-3) and lets either system be replaced (DR-4).
- **Assumptions**: None
- **Out of Scope**: Rendering (REQ-WORKS-*); Editing Interface forms (REQ-EDIT-020); enforcing the rules in the build (REQ-CONTENT-030); the legacy import (REQ-MIGRATION-*). There is no Works order that replaces the year order (FR-3.1).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (owns Work and Image, flags, orders, image order, alt text and credits); DR-2, DR-3, DR-4
- **Security Traceability**: SEC-INPUT-3 (rules rechecked by the Site Build, via REQ-CONTENT-030)

## Scope

- **Applies To**: Multiple
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Work and Image files in `dalini/nina-content`; model documentation in `dalini/nina-site`; the shared rule check used by REQ-CONTENT-030
- **Actors**: Editors (Nina, Ives); Site Build
- **Preconditions**: None
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-12, T-49 (rules rechecked by the Site Build)
- **Abuse / Misuse Case**: An editor token writes a file that skips required fields directly to the Content Store (T-49)
- **Trust Boundary**: TB-3 (Editing Interface → Content Store), TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Content files in `dalini/nina-content`, whoever wrote them
- **Authoritative Enforcement Point**: Site Build (recheck, REQ-CONTENT-030); Editing Interface (first check)
- **Independent Verification**: The Site Build rechecks every rule (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: No external standard applies to the content model itself.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a work file with title `tiptoe`, year `2018`, one image with German alt text and no other fields, when the rule check runs, then the work is valid.
2. **AC-02 — Boundary or failure behavior**: Given a work without a title, a work without a year, a work without any image, and an image whose alt text is empty in both languages, when the rule check runs, then each is reported with its item identifier and the missing field name (FR-10.6).
3. **AC-03 — Boundary or failure behavior**: Given a year of `2018–2019`, `18` or `20188`, when the rule check runs, then the year is reported as not a single four-digit year (FR-3.2).
4. **AC-04 — Boundary or failure behavior**: Given dimensions with one value or with four values, when the rule check runs, then the dimensions field is reported; two or three values pass (FR-3.3).
5. **AC-05 — Prohibited behavior**: Given two works marked featured in files written directly to the test content repository, when the rule check runs, then both are reported with the field `featured`; and the title has no German or English variant (FR-1.5).
6. **AC-06 — Additional criterion**: Given the model documentation in `dalini/nina-site`, when it is reviewed, then every field lists its type, language handling, whether it is required and which rule checks it (DR-3, DR-4).

## Failure Behavior

- **On Invalid Input**: The rule check reports the content item and the field; the Site Build fails and publishes nothing (REQ-CONTENT-030)
- **On Authentication Failure**: N/A: the model makes no authentication decision
- **On Authorization Failure**: N/A: write access to the Content Store is REQ-STORE-010
- **On Security-Decision Failure**: N/A
- **On External Dependency Failure**: N/A
- **On System Error**: An unreadable or unparsable file is reported like a rule violation; nothing is published
- **Logging / Audit**: Rule violations are reported with item identifier and field name only
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Rule check per field: present, missing, malformed year, dimension count, alt text in one, both or neither language; featured representation
- **Integration Tests**: Hugo reads fixture work files in the documented format
- **Security Tests**: Fixtures written directly to a test content repository, bypassing the Editing Interface, are rejected by the rule check
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-010/AC-01`; AC-02 → `test:CONTENT-010/AC-02`; AC-03 → `test:CONTENT-010/AC-03`; AC-04 → `test:CONTENT-010/AC-04`; AC-05 → `test:CONTENT-010/AC-05`; AC-06 → `test:CONTENT-010/AC-06` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-CONTENT-020 (https://github.com/dalini/nina-website/issues/19), REQ-CONTENT-050 (https://github.com/dalini/nina-website/issues/20), REQ-CONTENT-060 (https://github.com/dalini/nina-website/issues/21), REQ-CONTENT-030 (https://github.com/dalini/nina-website/issues/22), REQ-CONTENT-040 (https://github.com/dalini/nina-website/issues/23), REQ-OUTPUT-010 (https://github.com/dalini/nina-website/issues/27), REQ-OUTPUT-020 (https://github.com/dalini/nina-website/issues/28), REQ-OUTPUT-030 (https://github.com/dalini/nina-website/issues/29), REQ-NAV-020 (https://github.com/dalini/nina-website/issues/32), REQ-WORKS-020 (https://github.com/dalini/nina-website/issues/36), REQ-IMAGES-010 (https://github.com/dalini/nina-website/issues/37), REQ-HOME-010 (https://github.com/dalini/nina-website/issues/38), REQ-WORKS-010 (https://github.com/dalini/nina-website/issues/39), REQ-EDIT-020 (https://github.com/dalini/nina-website/issues/112), REQ-MIGRATION-010 (https://github.com/dalini/nina-website/issues/121)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Markdown with YAML front matter in `dalini/nina-content`; model documented in `dalini/nina-site` (ARCHITECTURE.md Content Store, DR-3, DR-4); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Fields or rules that only the editing system or the generator can read (DR-4); rules defined twice (DR-3)
- **Implementation Guidance**: A single reference to the featured work (for example on the Home page item) instead of a flag on every work makes 'exactly one' structural; the CMS behavior is REQ-EDIT-020. Alt text in one language is enough because FR-1.7 shows the other language and FR-11.3 counts German-only interim alt text as alt text.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: It sets the shared rule set and portable format that every page, validation and editing issue reads; a modelling mistake here spreads everywhere.
- **Required Human Review**: Product review by Nina (fields as she will see them); architecture review by Ives (DR-3, DR-4)
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-59: no order replaces the year order: newest year first, the manual position orders works within a year only (FR-3.1).
