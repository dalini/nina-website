## Metadata

- **ID**: REQ-HTTP-010
- **Title**: Serve nina-laaf.de over HTTPS only, redirect HTTP and send HSTS
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-1; REQUIREMENTS.md NFR-4; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: Static Hosting MUST serve every page and asset of `nina-laaf.de` over HTTPS only, MUST redirect every HTTP request to HTTPS, and MUST send a `Strict-Transport-Security` header on HTTPS responses.
- **Rationale**: Pages altered or read in transit mislead visitors (SECURITY.md T-28).
- **Assumptions**: None
- **Out of Scope**: HSTS max-age value (TO BE DECIDED, PQ-17); domain and DNS account controls (SEC-HOST-1; blocked by PQ-12); the Editing Interface (SEC-HTTP-4; blocked by PQ-1).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting
- **Security Traceability**: SEC-HTTP-1

## Scope

- **Applies To**: Web Client
- **Components**: Static Hosting, Public Site
- **Interfaces / Operations**: All responses of `nina-laaf.de`
- **Actors**: Visitor; network attacker
- **Preconditions**: REQ-PIPELINE-010 (site deployed)
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Confidentiality
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v1 T-28; CWE-319
- **Abuse / Misuse Case**: A network attacker downgrades a visitor to HTTP and alters the page.
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Network path between visitor and host
- **Authoritative Enforcement Point**: Static Hosting configuration
- **Independent Verification**: Automated checks against the deployed site, independent of the hosting configuration
- **Zero Trust Relevance**: TO BE DECIDED: "all communication is secured regardless of network location" (NIST SP 800-207 tenets) is relevant; section not verified

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: IETF RFC 6797 (HTTP Strict Transport Security)
- **Mapping Basis**: Other: RFC 6797 defines HSTS.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the deployed site, when any page or asset is requested over HTTPS, then it is served with a valid certificate and a `Strict-Transport-Security` header.
2. **AC-02 — Boundary or failure behavior**: Given an HTTP request to any path, when it is sent, then the response is a redirect to the same path over HTTPS and contains no page content.
3. **AC-03 — Prohibited behavior**: Given the deployed site, when pages are inspected, then none references an asset over `http:`.

## Failure Behavior

- **On Invalid Input**: N/A: no input
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: A failing check blocks the next release
- **On External Dependency Failure**: N/A
- **On System Error**: If the post-deploy check fails, the deploy is reported as failed
- **Logging / Audit**: Post-deploy check report: URL, expected and observed header values
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Post-deploy checks of redirect and HSTS on page, asset and error responses
- **Security Tests**: Scan output for `http:` asset references; request HTTP variants of sample paths
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-010/AC-01`; AC-02 → `test:HTTP-010/AC-02`; AC-03 → `test:HTTP-010/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Deployed test site on the chosen host (PQ-17); HTTP client for header checks

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: Hosting provider (PQ-17)
- **Dependency Assumptions**: The host applies configured headers to every response, including error pages
- **Failure Impact**: Headers missing on some responses weakens the protection for those responses

## Implementation Notes

- **Constraints**: Host: TO BE DECIDED (PQ-17); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Serving content on HTTP; redirects that drop the path
- **Implementation Guidance**: HSTS preload and includeSubDomains depend on the DNS setup (PQ-12) and are not required here.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Host configuration and header checks fully specified by SEC-HTTP-1.
- **Required Human Review**: Security review
- **Open Decisions**: Non-blocking: PQ-17 (HSTS max-age).
