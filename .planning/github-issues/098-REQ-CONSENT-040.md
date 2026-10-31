## Metadata

- **ID**: REQ-CONSENT-040
- **Title**: Keep the Google tag loader and the measurement ID out of the preview output and the Editing Interface files
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-5 (`edit.` and `preview.` load no Google Analytics; no tag loader or measurement ID in the preview build or the Editing Interface files), SEC-HTTP-4; T-85; parent {{ISSUE_URL:REQ-CONSENT-000}}

## Requirement

- **Statement**: The preview build's output and the Editing Interface files (CMS bundle, CMS configuration, the OAuth helper's code) MUST contain neither the Google tag loader nor the measurement ID, checked at build time, and `edit.nina-laaf.de` and `preview.nina-laaf.de` MUST make no request to a Google origin.
- **Rationale**: Preview pages show unpublished titles and URLs; with the tag present, only the preview CSP would keep them from Google (T-85).
- **Assumptions**: None
- **Out of Scope**: Leaving the consent script and its footer link out of the preview build (decided in step 10 a third time, PQ-62; drafted by the next step 9 run); the preview CSP itself (REQ-HTTP-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (preview build); Editing Interface files in `dalini/nina-site`; DR-9
- **Security Traceability**: SEC-PRIV-5 (preview and Editing Interface), SEC-HTTP-4

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Editing Interface
- **Interfaces / Operations**: Output check of the preview build; check of the Editing Interface files in CI of `dalini/nina-site`
- **Actors**: Editors (preview visitors); build identity
- **Preconditions**: REQ-CONSENT-020, REQ-PIPELINE-060, REQ-EDIT-010, REQ-HTTP-040
- **Data Classification**: Internal
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Privacy
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-85; CWE-200
- **Abuse / Misuse Case**: N/A: the risk is unpublished titles and URLs reaching Google through a shared template
- **Trust Boundary**: TB-2 (preview and editing hosts), TB-6
- **Untrusted Inputs or Assertions**: Templates shared by production and preview
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: N/A: no external standard mapped; the rule is SEC-PRIV-5.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a preview build and the Editing Interface files built from `dalini/nina-site`, when the check runs, then neither contains the measurement ID or the Google tag loader, and the check passes.
2. **AC-02 — Boundary or failure behavior**: Given a template change that puts the measurement ID into the preview output, when the build runs, then the check fails, the preview is not deployed, and the change is reported.
3. **AC-03 — Prohibited behavior**: Given a browser opens pages on `preview.nina-laaf.de` and the Editing Interface on `edit.nina-laaf.de`, when their network requests are recorded, then no request goes to any Google origin.

## Failure Behavior

- **On Invalid Input**: A preview output or Editing Interface file containing the loader or the ID fails the check; nothing is deployed
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Search for the measurement ID pattern and the loader URL
- **Integration Tests**: Preview build of fixture content; CI run over the Editing Interface files
- **Security Tests**: Browser network recording on both hosts
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONSENT-040/AC-01`; AC-02 → `test:CONSENT-040/AC-02`; AC-03 → `test:CONSENT-040/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally for the preview output; the test `edit.` and `preview.` hosts; a test measurement ID

## Dependencies

- **Upstream Requirements**: REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known advisories (`REF-HUGO-ADV`, `REF-HUGO-SEC`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A build-time search over two outputs plus a browser check on two hosts; the rule is unambiguous.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-62: the preview build leaves out the consent script and its footer link (FR-9.2, SEC-PRIV-5).
