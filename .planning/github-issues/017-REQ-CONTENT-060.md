## Metadata

- **ID**: REQ-CONTENT-060
- **Title**: Define the Instagram settings: hidden posts and the feed switch
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-8.4, FR-8.6; ARCHITECTURE.md Data model expectations, Content Store, DR-5; parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The content model MUST hold the Instagram settings in the Content Store: a list of hidden posts, each identifying one post of @ninalaaf, and a feed switch (on or off); changes are saved and published like any other content.
- **Rationale**: Nina must be able to hide a post or switch the feed off without reviewing every post (FR-8.6, T-36).
- **Assumptions**: None
- **Out of Scope**: Fetching posts (REQ-INSTAGRAM-030); rendering (REQ-INSTAGRAM-020); the form (REQ-EDIT-070).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (owns the Instagram settings); DR-5 (the setting identifies posts; Instagram access stays in the Instagram Feed Integration)
- **Security Traceability**: N/A

## Scope

- **Applies To**: Multiple
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Instagram settings file in `dalini/nina-content`
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

1. **AC-01 — Expected behavior**: Given settings with the feed switched on and one hidden post, when the rule check runs, then they are valid and expose the hidden post identifier and the switch to the Site Build.
2. **AC-02 — Boundary or failure behavior**: Given a settings file without the feed switch, when the rule check runs, then it is reported with the missing field.
3. **AC-03 — Prohibited behavior**: Given the settings, when they are reviewed, then they hold no Instagram token, account credential or API data (DR-5, SEC-SECRET-1).

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

- **Unit Tests**: Rule check for the switch and the hidden-post list
- **Integration Tests**: Hugo reads the fixture settings file
- **Security Tests**: Fixtures written directly to a test content repository, bypassing the Editing Interface, are rejected by the rule check
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-060/AC-01`; AC-02 → `test:CONTENT-060/AC-02`; AC-03 → `test:CONTENT-060/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}})
- **Downstream Requirements**: REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-INSTAGRAM-020 ({{ISSUE_URL:REQ-INSTAGRAM-020}}), REQ-EDIT-070 ({{ISSUE_URL:REQ-EDIT-070}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Markdown with YAML front matter in `dalini/nina-content`; model documented in `dalini/nina-site` (ARCHITECTURE.md Content Store, DR-3, DR-4); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Fields or rules that only the editing system or the generator can read (DR-4); rules defined twice (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: A small, fully specified settings item.
- **Required Human Review**: Product review by Nina (fields as she will see them); architecture review by Ives (DR-3, DR-4)
- **Open Decisions**: None. Answered in step 10 again (`.planning/OPEN_QUESTIONS.md`): PQ-53: Nina pastes the post link; the build matches the shortcode (FR-8.6).
