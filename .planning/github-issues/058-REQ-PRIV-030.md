## Metadata

- **ID**: REQ-PRIV-030
- **Title**: Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-7, SEC-PRIV-5 (property owned by Ives, Nina read access, data processing terms, default settings); T-70; parent {{ISSUE_URL:REQ-CONSENT-000}}

## Requirement

- **Statement**: The Google Analytics property MUST be owned by Ives's Google account with a second factor, with Ives as the only Administrator, Nina with the Viewer role and nobody else; Google's data processing terms MUST be accepted; the data collection settings of the property and its web data stream, links to other Google products and additional tag destinations MUST stay at Google's defaults unless SEC-PRIV-7, SEC-PRIV-5, SEC-HTTP-2 and the Datenschutzerklärung are updated first; email redaction MUST stay on; and Ives MUST review all of this yearly as part of the SEC-HOST-1 review.
- **Rationale**: The property settings change what the Google tag collects in visitors' browsers without a change in `dalini/nina-site` (T-70).
- **Assumptions**: None
- **Out of Scope**: The tag on the site (REQ-CONSENT-020); the Datenschutzerklärung text (REQ-LEGAL-010).
- **Design Traceability**: N/A
- **Architecture Traceability**: Public Site (Google Analytics measurements to Google); Google Analytics property (external)
- **Security Traceability**: SEC-PRIV-7, SEC-PRIV-5

## Scope

- **Applies To**: External Integration
- **Components**: Google Analytics property (external)
- **Interfaces / Operations**: Google Analytics property administration, user list, data stream settings, change history
- **Actors**: Ives (Administrator); Nina (Viewer); Google
- **Preconditions**: None
- **Data Classification**: Confidential
- **Personal or Regulated Data**: Personal Data (visitor data in the property)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; processing terms with Google; no article mapping (SECURITY.md)

## Security Context

- **Security Objectives**: Privacy, Integrity
- **Control Layers**: Authentication, Authorization, Data Protection
- **Threat References**: STRIDE Tampering, Spoofing and Information Disclosure: SECURITY.md Threat Model v3 T-70
- **Abuse / Misuse Case**: Someone who takes over the owning account, or who is given a role, widens collection beyond the Datenschutzerklärung or adds readers
- **Trust Boundary**: TB-6 (Google Analytics property settings → Google tag, DF-15)
- **Untrusted Inputs or Assertions**: Changes to property settings made outside `dalini/nina-site`
- **Authoritative Enforcement Point**: Google Analytics property access control; the yearly review by Ives
- **Independent Verification**: Property change history compared with the review record
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: Google Analytics Help: enhanced measurement (`REF-GA4-EM`), data redaction (`REF-GA4-REDACT`)
- **Mapping Basis**: Other: `REF-GA4-EM` and `REF-GA4-REDACT` as cited in SECURITY.md SEC-PRIV-7.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the property, when the review runs, then the owning Google account has a second factor, Ives is the only Administrator, Nina has the Viewer role, nobody else has access, Google's data processing terms are accepted, and email redaction is on.
2. **AC-02 — Boundary or failure behavior**: Given a data collection setting of the property or its web data stream differs from Google's default, or a link to another Google product or an additional tag destination exists, when the review compares them, then it is recorded as a finding and reverted unless SEC-PRIV-7, SEC-PRIV-5, SEC-HTTP-2 and the Datenschutzerklärung were updated first.
3. **AC-03 — Prohibited behavior**: Given the review record in `docs/ops/`, when it is read, then it holds no measurement data, no account credentials and no personal data beyond the editors' names.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: Google Analytics unavailable: statistics only are lost; the site works (T-84)
- **On System Error**: N/A
- **Logging / Audit**: Yearly review record in `docs/ops/` (date, findings, changes reverted); Google's property change history
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: settings and review, no first-party code
- **Integration Tests**: N/A
- **Security Tests**: Review of the user list, second factor and settings against Google's defaults and the change history
- **Compliance Tests / Evidence**: Review record in `docs/ops/`; acceptance of Google's data processing terms
- **Acceptance-Criteria Traceability**: AC-01 → `test:PRIV-030/AC-01`; AC-02 → `test:PRIV-030/AC-02`; AC-03 → `test:PRIV-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The production Google Analytics property (settings review only)

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}})
- **External Dependencies**: Google Analytics
- **Dependency Assumptions**: ASSUMPTION (SECURITY.md): the property settings cannot add script of their own choosing to the Google tag; Google Tag Manager containers are not used
- **Failure Impact**: Wider collection than the Datenschutzerklärung describes, or readers who should not see the statistics

## Implementation Notes

- **Constraints**: Runbook and review record in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1)
- **Prohibited Approaches**: Shared Google accounts; enabling Google signals, user-provided data collection or product links without updating the rules first
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A settings checklist and its yearly review, fully specified by SEC-PRIV-7; no code.
- **Required Human Review**: Privacy review by Ives; Nina confirms her Viewer access
- **Open Decisions**: None
