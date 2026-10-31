## Metadata

- **ID**: REQ-OUTPUT-020
- **Title**: Treat content fields as data and never evaluate generator syntax inside them
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-6; ARCHITECTURE.md DR-3; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: The Site Build MUST NOT evaluate template, shortcode or other generator syntax that appears inside content fields; such syntax MUST appear in the output as encoded text.
- **Rationale**: Evaluated content would run at build time with access to the build environment and its secrets (SECURITY.md T-18).
- **Assumptions**: None
- **Out of Scope**: Templates maintained by the maintainers (DR-8).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build; DR-3, DR-8
- **Security Traceability**: SEC-INPUT-6

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Content loading and rendering in the Site Build
- **Actors**: Editor (author of content); Visitor (reader); Site Build
- **Preconditions**: Generator chosen: Hugo (ARCHITECTURE.md), so test inputs use Hugo template and shortcode syntax
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Confidentiality
- **Control Layers**: Input Validation, Output Encoding
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v1 T-18; CWE-1336
- **Abuse / Misuse Case**: An editor enters template or shortcode syntax that reads environment variables or files during the build.
- **Trust Boundary**: TB-4 (Content Store → Site Build, DF-5) and TB-1 (output served to visitors)
- **Untrusted Inputs or Assertions**: Every editor-provided text field and link, legacy content (T-41)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: The Site Build enforces the rule regardless of what the Editing Interface accepted
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: No standard control mapped; driven by T-18.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a text field containing the chosen generator's template expression and shortcode syntax, when the Site Build runs, then the page shows that syntax as literal text.
2. **AC-02 — Boundary or failure behavior**: Given a field whose template expression would read an environment variable set in the test build, when the Site Build runs, then the variable's value appears nowhere in the output or the build log.
3. **AC-03 — Prohibited behavior**: Given any content field, when the Site Build renders it, then no part of it is passed to the template engine as template source.

## Failure Behavior

- **On Invalid Input**: Syntax is rendered as encoded text; no evaluation
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: If a field cannot be loaded as plain data, the build fails (SEC-INPUT-3)
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing (SEC-INPUT-3)
- **Logging / Audit**: Build log names item and field of rejected content; never logs the rejected markup in full
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Content loader returns fields as strings, never as templates
- **Integration Tests**: Build with fixtures containing the generator's syntax in every field type
- **Security Tests**: Template-injection payloads for the chosen generator, including environment and file access
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-020/AC-01`; AC-02 → `test:OUTPUT-020/AC-02`; AC-03 → `test:OUTPUT-020/AC-03`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build); fixtures with hostile content

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Rendering content through the template engine (e.g. content files processed as templates); enabling shortcodes in editor-controlled fields
- **Implementation Guidance**: Configure the generator so content files are data inputs; check its documentation for content files processed as templates.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Template-injection prevention depends on generator internals and build-time secret exposure (T-18).
- **Required Human Review**: Security review
- **Open Decisions**: None (step 10: Hugo).
