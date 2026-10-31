## Metadata

- **ID**: REQ-PREVIEW-010
- **Title**: Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHN-1, SEC-AUTHN-2, SEC-CICD-3, `REF-AUTH`; REQUIREMENTS.md FR-10.9; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Every request to `preview.nina-laaf.de` MUST require HTTP Basic Auth over HTTPS with one credential per editor (Nina, Ives); each password MUST be unique, MUST NOT be the editor's GitHub password, and MUST be stored only as a salted hash made with the strongest `crypt()` method the VPS supports; repeated failed sign-ins MUST be limited by nginx.
- **Rationale**: Previews show unpublished content and must be reachable only by editors (T-21, T-67).
- **Assumptions**: None
- **Out of Scope**: Sign-out or expiry of Basic Auth sessions (a property of Basic Auth, accepted in SECURITY.md T-67).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (protects `preview.nina-laaf.de` with per-editor HTTP Basic Auth)
- **Security Traceability**: SEC-AUTHN-1 (preview site), SEC-AUTHN-2 (preview credentials), SEC-CICD-3

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: nginx authentication for `preview.nina-laaf.de`; password file
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-HTTP-040, REQ-PIPELINE-060
- **Data Classification**: Confidential
- **Personal or Regulated Data**: Personal Data (editor credentials)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Authenticity
- **Control Layers**: Authentication
- **Threat References**: STRIDE Spoofing / Information Disclosure: SECURITY.md Threat Model v2 T-21, T-67; CWE-306, CWE-307, CWE-916
- **Abuse / Misuse Case**: Guessing preview passwords, or recovering weak hashes from a copied password file
- **Trust Boundary**: TB-2 (editor browser ↔ preview site)
- **Untrusted Inputs or Assertions**: Basic Auth credentials in requests
- **Authoritative Enforcement Point**: Static Hosting (nginx on the VPS)
- **Independent Verification**: Checked from outside the VPS against the live host
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Authentication Cheat Sheet (`REF-AUTH`)
- **Mapping Basis**: Other: `REF-AUTH` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given Nina's preview credential, when she opens any preview page over HTTPS, then it is served.
2. **AC-02 — Boundary or failure behavior**: Given no credential or a wrong one, when any preview page, image or PDF is requested, then the response is 401 and contains no preview content.
3. **AC-03 — Boundary or failure behavior**: Given many failed sign-ins from one address in a short time, when they continue, then further attempts are limited while a correct sign-in from another address still works.
4. **AC-04 — Prohibited behavior**: Given the password file, when every entry is reviewed, then each uses the strongest available `crypt()` method, none is plain, SHA-1 or MD5-based, and there is exactly one entry per editor.

## Failure Behavior

- **On Invalid Input**: Malformed requests are rejected by nginx with a 4xx status; no content changes
- **On Authentication Failure**: 401 with a Basic Auth challenge; no preview content, file names or existence of paths is disclosed
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Deny by default: a host without the required configuration does not serve the site
- **On External Dependency Failure**: N/A
- **On System Error**: nginx refuses to start with an invalid configuration; the configuration is checked before it is reloaded, so the running configuration stays in place
- **Logging / Audit**: nginx access logs as SEC-PRIV-6 (REQ-PRIV-020)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code
- **Integration Tests**: Requests with no, wrong and correct credentials for pages and media on the test preview host
- **Security Tests**: Brute-force simulation; hash method review of every entry
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PREVIEW-010/AC-01`; AC-02 → `test:PREVIEW-010/AC-02`; AC-03 → `test:PREVIEW-010/AC-03`; AC-04 → `test:PREVIEW-010/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}})
- **Downstream Requirements**: None
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Authentication setup with password-storage and guessing-limit requirements.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Answered in step 10 again (`.planning/OPEN_QUESTIONS.md`): PQ-42: 20 requests/s per IP (burst 40) and 20 connections per IP; preview: 5 failed sign-ins per IP per minute, then 429 (ARCHITECTURE.md Static Hosting).
