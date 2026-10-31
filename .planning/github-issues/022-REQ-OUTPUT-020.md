## Metadata

- **ID**: REQ-OUTPUT-020
- **Title**: Treat content fields as data and never evaluate generator syntax inside them
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-6, SEC-CICD-6; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: The Site Build MUST treat content fields as data: Hugo template, shortcode or other generator syntax inside content MUST NOT be evaluated and MUST appear as encoded text.
- **Rationale**: Evaluated content would run editor input at build time with access to the build environment (T-18).
- **Assumptions**: None
- **Out of Scope**: HTML encoding (REQ-OUTPUT-010); secret isolation (REQ-PIPELINE-090).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (TB-4)
- **Security Traceability**: SEC-INPUT-6

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Rendering of every content field, front matter and body
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-020
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Confidentiality
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v2 T-18; CWE-1336
- **Abuse / Misuse Case**: A Text body containing `{{< shortcode >}}` or `{{ os.Getenv "X" }}` tries to run at build time
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

1. **AC-01 — Expected behavior**: Given a Text body containing `{{ .Site.Title }}` and `{{< figure src="x" >}}`, when the build runs, then both appear on the page as literal, encoded text.
2. **AC-02 — Boundary or failure behavior**: Given a front-matter field containing `{{ os.Getenv "HOME" }}`, when the build runs, then the page shows the literal text and no environment value appears anywhere in the output.
3. **AC-03 — Prohibited behavior**: Given any content file, when the build runs, then no shortcode, partial or template function is executed from it.

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: Rendering path for bodies and front-matter strings
- **Integration Tests**: Build with generator-syntax fixtures in every field type
- **Security Tests**: Canary environment variable set during the build: it never appears in output or logs
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-020/AC-01`; AC-02 → `test:OUTPUT-020/AC-02`; AC-03 → `test:OUTPUT-020/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Hugo evaluates shortcodes in content files by default; render the Content Store's Markdown as data and confirm with the fixtures.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Template-injection prevention depends on generator internals and how content is loaded (T-18).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
