## Metadata

- **ID**: REQ-HOST-010
- **Title**: Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Operational
- **Source / Parent**: SECURITY.md SEC-HOST-1, SEC-CICD-8, `REF-ASVS-5`, `REF-SSDF`; ARCHITECTURE.md Static Hosting; parent https://github.com/dalini/nina-website/issues/2

## Requirement

- **Statement**: The registrar, DNS and VPS accounts for `nina-laaf.de` MUST be held by Ives with individual logins and a second factor wherever the provider offers one; renewal notices MUST go to a monitored address; a DNS record MUST be removed when the target it points to is retired; Ives MUST review all of this once a year.
- **Rationale**: Whoever controls the domain or DNS can serve a fake site with a valid certificate (T-26).
- **Assumptions**: None
- **Out of Scope**: VPS server hardening (REQ-HOST-020, REQ-HOST-030); the Google Analytics property review (REQ-PRIV-030); the Actions billing review (REQ-PIPELINE-100).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting; domain and DNS of `nina-laaf.de` (TB-1)
- **Security Traceability**: SEC-HOST-1

## Scope

- **Applies To**: Multiple
- **Components**: Domain and DNS of `nina-laaf.de`, Static Hosting
- **Interfaces / Operations**: Registrar, DNS and VPS provider accounts; DNS records of `nina-laaf.de`; the yearly review checklist
- **Actors**: Maintainer (Ives)
- **Preconditions**: None
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (account holder names)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Authenticity
- **Control Layers**: Authentication
- **Threat References**: STRIDE Spoofing / Tampering: SECURITY.md Threat Model v3 T-26, T-27
- **Abuse / Misuse Case**: An attacker takes over the registrar account or claims a dangling DNS record and serves a fake nina-laaf.de with a false contact address
- **Trust Boundary**: TB-1 (domain and DNS)
- **Untrusted Inputs or Assertions**: Sign-in attempts to the provider accounts
- **Authoritative Enforcement Point**: The providers' account settings, held by Ives
- **Independent Verification**: Yearly review against the live DNS records and provider settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-SSDF` as cited in SECURITY.md; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the registrar, DNS and VPS accounts, when the yearly review runs, then each is held by Ives with an individual login and, where the provider offers one, a second factor enabled.
2. **AC-02 — Boundary or failure behavior**: Given a DNS record whose target host has been retired, when the review compares records with live targets, then the record is removed in the same review.
3. **AC-03 — Prohibited behavior**: Given the accounts, when they are reviewed, then none is shared, and renewal notices go to an address Ives monitors.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: Provider sign-in requires the second factor; failed sign-ins follow the provider's limits
- **On Authorization Failure**: GitHub rejects the operation; the repository and the site stay unchanged
- **On Security-Decision Failure**: Deny by default: anything not explicitly granted stays denied
- **On External Dependency Failure**: GitHub unavailable: no change is possible; the public site stays online (T-10)
- **On System Error**: N/A: settings only
- **Logging / Audit**: A dated review record: account holders, second factors, renewal date, DNS records checked against live targets
- **Alerting**: Renewal notices reach the monitored address

## Test Strategy

- **Unit Tests**: N/A: settings, no first-party code
- **Integration Tests**: DNS records compared with the live targets of all three hosts
- **Security Tests**: Review that each provider account has a second factor enabled
- **Compliance Tests / Evidence**: Evidence: the dated yearly review record (it also covers the SEC-CICD-8 billing settings and the SEC-HOST-2 update log)
- **Acceptance-Criteria Traceability**: AC-01 → `test:HOST-010/AC-01`; AC-02 → `test:HOST-010/AC-02`; AC-03 → `test:HOST-010/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The real provider accounts, reviewed by Ives

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: None
- **External Dependencies**: Domain registrar, DNS provider and VPS provider
- **Dependency Assumptions**: Providers offer individual logins; second-factor support varies by provider
- **Failure Impact**: A lapsed domain or taken-over account lets an attacker impersonate the site (T-26)

## Implementation Notes

- **Constraints**: Accounts held by Ives (ARCHITECTURE.md Static Hosting)
- **Prohibited Approaches**: Shared logins; renewal notices to an unmonitored address
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; settings changes are made by Ives, who records them; a human reviews every change. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A checklist and its first review record; no code beyond a DNS check script.
- **Required Human Review**: Security review by Ives; Nina is informed of the account holder
- **Open Decisions**: None
