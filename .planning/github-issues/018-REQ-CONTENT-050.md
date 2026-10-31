## Metadata

- **ID**: REQ-CONTENT-050
- **Title**: Define the Exhibition entry and News item content models
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.4, FR-3.7, FR-4.1 to FR-4.7, FR-5.3, FR-5.4, FR-10.6; ARCHITECTURE.md Data model expectations, Content Store, DR-3, DR-4; SECURITY.md SEC-INPUT-6 (slug field); parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The content model MUST define Exhibition entry (title, type — solo, two-person, group or fair — venue, city, start date, end date, co-exhibitors, external link, linked works, a description, an ordered list of installation images, a slug that is the URL segment of the entry's page, publish state, a CV hide flag and a CV wording override) and News item (title, date, text, external link, image, publish state). Text fields MUST hold German and English values; the description is plain text. The rules MUST be: an Exhibition entry needs a title, a type, a venue, a city, a start date and an end date, and its end date MUST NOT be before its start date (the end date is the reported field); a News item needs a title and a date; installation images and the News image follow the Image rules of REQ-CONTENT-010.
- **Rationale**: Exhibition entries drive the Exhibitions/News page and their own pages, the home page entry and the derived CV sections (FR-2.3, FR-4, FR-5.3).
- **Assumptions**: None
- **Out of Scope**: How the slug is filled in and what happens when two entries have the same slug (blocked: PQ-57); the slug's character rule (REQ-CONTENT-080); rendering (REQ-EXHIBITIONS-*, REQ-NEWS-010); forms (REQ-EDIT-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (owns Exhibition entry and News item); DR-3, DR-4
- **Security Traceability**: SEC-INPUT-3 (via REQ-CONTENT-030); SEC-OUT-2 (external links, via REQ-OUTPUT-030)

## Scope

- **Applies To**: Multiple
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Exhibition entry and News item files in `dalini/nina-content`; model documentation
- **Actors**: Editors (Nina, Ives); Site Build
- **Preconditions**: REQ-CONTENT-010
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

1. **AC-01 — Expected behavior**: Given an Exhibition entry with title, type group, venue, city, start 2026-05-01, end 2026-06-30, two linked works, a German and English description and one installation image with alt text, when the rule check runs, then it is valid and its linked works resolve to Work items.
2. **AC-02 — Boundary or failure behavior**: Given six Exhibition entries each missing one of title, type, venue, city, start date or end date, and a News item without a title or without a date, when the rule check runs, then each is reported with item identifier and the missing field (FR-10.6).
3. **AC-03 — Boundary or failure behavior**: Given an Exhibition entry with type `talk`, when the rule check runs, then the type field is reported; only solo, two-person, group and fair are accepted (FR-4.2).
4. **AC-04 — Boundary or failure behavior**: Given an entry starting 2026-06-30 and ending 2026-06-01, when the rule check runs, then it is reported with the field `end date`; an entry that starts and ends on the same day is valid (FR-4.1, FR-10.6).
5. **AC-05 — Prohibited behavior**: Given a News item, when the model is reviewed, then it has no Exhibition type and is never treated as an Exhibition entry (FR-4.6).
6. **AC-06 — Additional criterion**: Given an installation image without alt text, when the rule check runs, then it is reported like a work image without alt text (FR-3.7, FR-10.6).

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

- **Unit Tests**: Rule check for each required field, end-before-start and same-day dates, type values, linked-work resolution, installation images and News fields
- **Integration Tests**: Hugo reads fixture Exhibition and News files
- **Security Tests**: Fixtures written directly to a test content repository, bypassing the Editing Interface, are rejected by the rule check
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-050/AC-01`; AC-02 → `test:CONTENT-050/AC-02`; AC-03 → `test:CONTENT-050/AC-03`; AC-04 → `test:CONTENT-050/AC-04`; AC-05 → `test:CONTENT-050/AC-05`; AC-06 → `test:CONTENT-050/AC-06` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}})
- **Downstream Requirements**: REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-CONTENT-080 ({{ISSUE_URL:REQ-CONTENT-080}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}}), REQ-EXHIBITIONS-010 ({{ISSUE_URL:REQ-EXHIBITIONS-010}}), REQ-EDIT-030 ({{ISSUE_URL:REQ-EDIT-030}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Markdown with YAML front matter in `dalini/nina-content`; model documented in `dalini/nina-site` (ARCHITECTURE.md Content Store, DR-3, DR-4); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Fields or rules that only the editing system or the generator can read (DR-4); rules defined twice (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A field list taken directly from FR-4 and FR-10.6, following the REQ-CONTENT-010 pattern.
- **Required Human Review**: Product review by Nina (fields as she will see them); architecture review by Ives (DR-3, DR-4)
- **Open Decisions**: PQ-57 (blocking) for the excluded slug part; nothing else.
