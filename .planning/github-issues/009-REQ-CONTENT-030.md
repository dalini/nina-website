## Metadata

- **ID**: REQ-CONTENT-030
- **Title**: Fail the Site Build and publish nothing when content violates the content model
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Reliability
- **Source / Parent**: SECURITY.md SEC-INPUT-3; REQUIREMENTS.md FR-10.6 (recheck), FR-10.8 (which item and field); ARCHITECTURE.md DR-3, Site Build; parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The Site Build MUST validate all content against the content model before generating output and, on any violation, MUST end with a failure, write no deployable output, and report each violating content item and field.
- **Rationale**: DR-3 requires the Site Build to recheck content rules; SEC-INPUT-3 keeps the previous site online when content is invalid; FR-10.8 needs the failing item and field.
- **Assumptions**: None
- **Out of Scope**: Showing the failure in the Editing Interface (FR-10.8 display, blocked by PQ-1); who else is notified (FR-10.8 TO BE DECIDED); keeping the previous site online is verified with the deploy step in REQ-PIPELINE-010.
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (rechecks content rules, fails the publish, publishes status with the failing item); DR-1, DR-3
- **Security Traceability**: SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Site Build run; build result and failure report
- **Actors**: Editor (indirectly, through stored content); Site Build
- **Preconditions**: REQ-CONTENT-010 and REQ-CONTENT-020 rule sets exist
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation, Input Validation
- **Threat References**: STRIDE Tampering and Denial of Service: SECURITY.md Threat Model v1 T-12, T-24, T-41
- **Abuse / Misuse Case**: Invalid or crafted content is written directly to the store (T-12) or imported (T-41) and would otherwise be published.
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
- **Mapping Basis**: Fail-closed build gate; no standard control mapped.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given content that satisfies the content model, when the Site Build runs, then it ends successfully and generates the site.
2. **AC-02 — Boundary or failure behavior**: Given content with one Work missing its year and one Image missing alt text, when the Site Build runs, then it ends with a failure and the report names both items and both fields.
3. **AC-03 — Prohibited behavior**: Given any content violation, when the Site Build ends, then no deployable output directory or artifact exists for that run.

## Failure Behavior

- **On Invalid Input**: Build fails; report lists each violation (item, field); no output
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A: the Site Build only reads content
- **On Security-Decision Failure**: A validator error or crash counts as failure; nothing is published
- **On External Dependency Failure**: N/A
- **On System Error**: Build fails without output; temporary files are removed or excluded from any deployable artifact
- **Logging / Audit**: Build log: content type, item identifier and field for each violation; no content values beyond the identifier; no secrets
- **Alerting**: Who besides Nina is notified of a failed publish: TO BE DECIDED (REQUIREMENTS.md FR-10.8)

## Test Strategy

- **Unit Tests**: Validator aggregation of multiple violations; mapping of violations to item and field
- **Integration Tests**: Full build with valid fixtures (pass) and invalid fixtures (fail, no output)
- **Security Tests**: Build with an unparseable content file fails without output and without a stack trace that prints content values
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-030/AC-01`; AC-02 → `test:CONTENT-030/AC-02`; AC-03 → `test:CONTENT-030/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1)

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Skipping invalid items and publishing the rest; continuing the build after a validation error
- **Implementation Guidance**: Run validation as the first build step so no rendering starts on invalid content.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Wires the existing rule set into a fail-closed first build step; behavior is fully specified by SEC-INPUT-3.
- **Required Human Review**: Architecture review (DR-3, DR-4); product review by Nina of field names shown in error messages
- **Open Decisions**: Non-blocking: PQ-16. Publish-status format read by the Editing Interface (DF-9) is part of PQ-1 (blocked).
