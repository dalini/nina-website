## Metadata

- **ID**: REQ-CONTENT-030
- **Title**: Fail the Site Build and publish nothing when content violates the content model
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: SECURITY.md SEC-INPUT-3; REQUIREMENTS.md FR-10.6, FR-10.8; ARCHITECTURE.md Site Build, DR-3; parent https://github.com/dalini/nina-website/issues/3

## Requirement

- **Statement**: Before any output is deployed, the Site Build MUST run every rule of the content model (REQ-CONTENT-010, -020, -050, -060) and the publish rules of FR-10.6 (exactly one published featured work exists, FR-10.3; the Contact email address, the Impressum and the Datenschutzerklärung exist); on any violation it MUST fail, report the failing item and field, and publish nothing, leaving the previous site online.
- **Rationale**: A direct write can bypass the Editing Interface (T-12, T-49); failing closed keeps the live site intact and tells Nina what to fix (FR-10.8).
- **Assumptions**: None
- **Out of Scope**: Showing the failure to Nina in the Editing Interface (REQ-EDIT-090); file type, size, pixel and SVG rechecks (REQ-IMAGES-040, REQ-IMAGES-050); content-format and front-matter checks (REQ-CONTENT-080); checks of the generated pages (REQ-OUTPUT-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (rechecks content rules, fails the publish); DR-3
- **Security Traceability**: SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: First stage of the Site Build; the failure result consumed by REQ-PIPELINE-120 (status file) and REQ-PIPELINE-110 (email)
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-060
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-12, T-49; Denial of Service T-24
- **Abuse / Misuse Case**: An editor token commits a work without a year directly through the GitHub API, hoping the build publishes a broken page (T-49)
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Every file and field in the Content Store checkout, including direct writes that bypassed the Editing Interface (T-49)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Input Validation Cheat Sheet (`REF-INPUT`)
- **Mapping Basis**: Other: `REF-INPUT` as cited in SECURITY.md for this rule.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given valid fixture content, when the Site Build runs, then the rule stage passes and the build continues.
2. **AC-02 — Boundary or failure behavior**: Given a published work without a year written directly to the test content repository, when the Site Build runs, then it fails, names that work's identifier and the field `year`, and deploys nothing.
3. **AC-03 — Boundary or failure behavior**: Given content in which no published work is featured, two works are featured, or the Impressum is missing, when the Site Build runs, then it fails and names the item and field.
4. **AC-04 — Prohibited behavior**: Given a failing build, when the live test site is requested afterwards, then it serves exactly the previously deployed version; no partial output was uploaded.
5. **AC-05 — Additional criterion**: Given several violations, when the Site Build fails, then every violation is listed with item and field, not only the first.

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

- **Unit Tests**: Each rule and publish rule against valid and invalid fixtures; result object with item and field
- **Integration Tests**: Full build against a test content repository with an invalid commit; deploy step not reached
- **Security Tests**: Direct-write fixtures (bypassing the Editing Interface) for each rule; the build log contains no content values
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-030/AC-01`; AC-02 → `test:CONTENT-030/AC-02`; AC-03 → `test:CONTENT-030/AC-03`; AC-04 → `test:CONTENT-030/AC-04`; AC-05 → `test:CONTENT-030/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 (https://github.com/dalini/nina-website/issues/18), REQ-CONTENT-020 (https://github.com/dalini/nina-website/issues/19), REQ-CONTENT-050 (https://github.com/dalini/nina-website/issues/20), REQ-CONTENT-060 (https://github.com/dalini/nina-website/issues/21)
- **Downstream Requirements**: REQ-CONTENT-070 (https://github.com/dalini/nina-website/issues/24), REQ-CONTENT-080 (https://github.com/dalini/nina-website/issues/25), REQ-OUTPUT-030 (https://github.com/dalini/nina-website/issues/29), REQ-OUTPUT-040 (https://github.com/dalini/nina-website/issues/30), REQ-HOME-010 (https://github.com/dalini/nina-website/issues/38), REQ-IMAGES-040 (https://github.com/dalini/nina-website/issues/43), REQ-IMAGES-050 (https://github.com/dalini/nina-website/issues/44), REQ-CONTACT-010 (https://github.com/dalini/nina-website/issues/57), REQ-LEGAL-010 (https://github.com/dalini/nina-website/issues/58), REQ-PIPELINE-010 (https://github.com/dalini/nina-website/issues/85), REQ-PIPELINE-120 (https://github.com/dalini/nina-website/issues/102)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Run the rule stage in the content-processing job without secrets (REQ-PIPELINE-090), before Hugo renders anything.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: It wires the existing rule set into a fail-closed first stage; SEC-INPUT-3 fully specifies the behavior.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
