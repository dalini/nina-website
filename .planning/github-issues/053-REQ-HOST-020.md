## Metadata

- **ID**: REQ-HOST-020
- **Title**: Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HOST-2, SEC-HOST-1, `REF-ASVS-5`, `REF-SSDF`; ARCHITECTURE.md Static Hosting; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The VPS MUST receive operating-system and package security updates on a schedule, MUST allow administrative access only over SSH with keys (no password sign-in, no direct root sign-in), MUST expose only ports 22, 80 and 443 to the internet, and nginx worker processes MUST run under an unprivileged account.
- **Rationale**: One VPS runs every internet-facing process; one unpatched flaw or weak login would take over all three sites (T-61).
- **Assumptions**: None
- **Out of Scope**: How non-root processes read their root-only secrets, and the processes that need them (OAuth helper, content check, token refresh, backup): blocked scope (PQ-33, PQ-34). The deploy account (REQ-PIPELINE-010). The statistics tool (blocked scope, PQ-35).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (server hardening, process accounts)
- **Security Traceability**: SEC-HOST-2 (except the secret-reading part)

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: SSH daemon, firewall, package update job, nginx process accounts
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: None
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Confidentiality
- **Control Layers**: Architecture, Authentication
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v2 T-61, T-27; CWE-250, CWE-307
- **Abuse / Misuse Case**: Password guessing over SSH; an exploit against an unpatched package or a worker running as root
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: All inbound connections to the VPS
- **Authoritative Enforcement Point**: Static Hosting (nginx on the VPS)
- **Independent Verification**: Checked from outside the VPS against the live host
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

1. **AC-01 — Expected behavior**: Given the hardened VPS, when an external port scan runs, then only ports 22, 80 and 443 are open.
2. **AC-02 — Boundary or failure behavior**: Given SSH attempts with a password and as root with a valid key, when they are made, then both are rejected.
3. **AC-03 — Boundary or failure behavior**: Given a security update is published for an installed package, when the update schedule runs, then it is installed and logged.
4. **AC-04 — Prohibited behavior**: Given the process list, when it is checked, then no nginx worker runs as root.

## Failure Behavior

- **On Invalid Input**: Malformed requests are rejected by nginx with a 4xx status; no content changes
- **On Authentication Failure**: SSH rejects password and root sign-in attempts without revealing which accounts exist
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Deny by default: a host without the required configuration does not serve the site
- **On External Dependency Failure**: N/A
- **On System Error**: nginx refuses to start with an invalid configuration; the configuration is checked before it is reloaded, so the running configuration stays in place
- **Logging / Audit**: Update runs are logged on the VPS and checked in the yearly SEC-HOST-1 review (REQ-HOST-010)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code
- **Integration Tests**: Port scan from outside; SSH sign-in attempts; process list check on the test server
- **Security Tests**: Port scan; SSH password and root attempts
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HOST-020/AC-01`; AC-02 → `test:HOST-020/AC-02`; AC-03 → `test:HOST-020/AC-03`; AC-04 → `test:HOST-020/AC-04` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-PRIV-020 ({{ISSUE_URL:REQ-PRIV-020}}), REQ-HTTP-010 ({{ISSUE_URL:REQ-HTTP-010}}), REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Server hardening is security-sensitive and a mistake (e.g. locking out SSH) is costly to undo.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: PQ-41 (non-blocking): the security-update schedule; PQ-33 and PQ-34 (blocking for the excluded part).
