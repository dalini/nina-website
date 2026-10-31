## Metadata

- **ID**: REQ-CONTENT-020
- **Title**: Define the Text, CV entry, Download and Page content models
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.4, FR-5.1, FR-5.2, FR-5.4, FR-6.1, FR-6.2, FR-7.1, FR-7.2, FR-9.1, FR-10.6; ARCHITECTURE.md Data model expectations, DR-3, DR-4; parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The content model MUST define: Text (title, body, optional year, manual position, publish state); CV entry (section — short bio, education, solo exhibitions, works in public space, group exhibitions, or awards and grants — year, text, publish state); Download (title, PDF file, publish state); and the Pages Home, Contact (with Nina's email address and the galerie burster link), Impressum and Datenschutzerklärung, each with page content. Text fields MUST hold German and English values. The rules MUST be: a Text needs a title and a body; a CV entry needs a section and a text, and a year in every section except the short bio; a Download needs a title and a PDF file; a publish MUST fail when the Contact email address, the Impressum or the Datenschutzerklärung is missing.
- **Rationale**: These items carry the Texts, CV, Downloads, Contact and legal pages; their rules are FR-10.6.
- **Assumptions**: None
- **Out of Scope**: Exhibition entries and News items (REQ-CONTENT-050); rendering; Editing Interface forms (REQ-EDIT-040); derived CV lines (REQ-CV-020).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (owns Text, CV entry, Download, Page); DR-3, DR-4
- **Security Traceability**: SEC-INPUT-3 (via REQ-CONTENT-030); SEC-OUT-1 (Text bodies, Page content and the CV bio are the rich-text fields of these items)

## Scope

- **Applies To**: Multiple
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Text, CV entry, Download and Page files in `dalini/nina-content`; model documentation
- **Actors**: Editors (Nina, Ives); Site Build
- **Preconditions**: REQ-CONTENT-010
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v2 T-12, T-49 (rules rechecked by the Site Build)
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

1. **AC-01 — Expected behavior**: Given a Text with title and body, a CV entry in section education with year 2010 and text, a Bio CV entry without year, and a Download with title and PDF, when the rule check runs, then all are valid.
2. **AC-02 — Boundary or failure behavior**: Given a Text without body, a CV entry in group exhibitions without year, a CV entry without section, and a Download without PDF, when the rule check runs, then each is reported with item identifier and field.
3. **AC-03 — Boundary or failure behavior**: Given content without a Contact email address, or without an Impressum or Datenschutzerklärung Page, when the publish rules run, then the publish is reported as failing with the missing item named (FR-10.6).
4. **AC-04 — Prohibited behavior**: Given a CV entry with a section outside the six FR-5.2 sections, when the rule check runs, then it is reported and not assigned to any section.
5. **AC-05 — Additional criterion**: Given a CV entry in section solo exhibitions with year and text, when the rule check runs, then it is valid as a CV-only line (FR-5.4).

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

- **Unit Tests**: Rule check per item type and field; publish rules for Contact email, Impressum and Datenschutzerklärung
- **Integration Tests**: Hugo reads fixture files of each item type
- **Security Tests**: Fixtures written directly to a test content repository, bypassing the Editing Interface, are rejected by the rule check
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-020/AC-01`; AC-02 → `test:CONTENT-020/AC-02`; AC-03 → `test:CONTENT-020/AC-03`; AC-04 → `test:CONTENT-020/AC-04`; AC-05 → `test:CONTENT-020/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}})
- **Downstream Requirements**: REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}}), REQ-OUTPUT-020 ({{ISSUE_URL:REQ-OUTPUT-020}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-CV-010 ({{ISSUE_URL:REQ-CV-010}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}}), REQ-CONTACT-010 ({{ISSUE_URL:REQ-CONTACT-010}}), REQ-LEGAL-010 ({{ISSUE_URL:REQ-LEGAL-010}}), REQ-EDIT-040 ({{ISSUE_URL:REQ-EDIT-040}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Markdown with YAML front matter in `dalini/nina-content`; model documented in `dalini/nina-site` (ARCHITECTURE.md Content Store, DR-3, DR-4); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Fields or rules that only the editing system or the generator can read (DR-4); rules defined twice (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: It follows the pattern REQ-CONTENT-010 sets; fields and rules come straight from FR-5 to FR-7 and FR-10.6.
- **Required Human Review**: Product review by Nina (fields as she will see them); architecture review by Ives (DR-3, DR-4)
- **Open Decisions**: None
