## Metadata

- **ID**: REQ-EDIT-100
- **Title**: Install the Editing Interface files on the VPS by hand from a tagged release of dalini/nina-site, owned by root and read-only to nginx and the OAuth helper
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-2 (Editing Interface files on the VPS, installed by hand by Ives; step 10 a third time, SQ-22 / PQ-54), SEC-AUTHN-4 (helper installed from a tagged release), SEC-CICD-1 (the deploy account writes nothing on `edit.nina-laaf.de` except the publish status file); REQUIREMENTS.md FR-10.1; ARCHITECTURE.md Editing Interface (Installation); T-82, DF-16; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: `dalini/nina-site` MUST contain an install script that Ives, as VPS administrator, runs from a checkout of a tagged release; before it changes anything, the script MUST verify the pinned Sveltia CMS bundle against its published checksum and the OAuth helper's dependencies against its lockfile, and MUST stop without changing the installed files when a check fails or the checkout is not at a release tag; it then MUST place the CMS bundle, the CMS configuration and the OAuth helper's code and lockfile on the VPS, owned by root and read-only to nginx and to the helper's account; no pipeline, deploy key or deploy account may install or write these files, and the deploy account MUST be able to write nothing on `edit.nina-laaf.de` except the publish status file.
- **Rationale**: The Editing Interface files run with every editor's GitHub token; whoever can change them can take over the Content Store (T-82), and Nina can only edit once they are installed (FR-10.1).
- **Assumptions**: None
- **Out of Scope**: Pinning and verifying the bundle and serving it only from `edit.nina-laaf.de` (REQ-EDIT-010); the helper's Deno permissions, account and service (REQ-AUTHN-030); the account and secret file layout (REQ-HOST-030); the CSP of the Editing Interface (REQ-HTTP-050); writing the publish status file (REQ-PIPELINE-120).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (Installation: Ives checks out a tagged release and runs its install script; files owned by root and read-only to nginx and the helper's account); Static Hosting; DF-16
- **Security Traceability**: SEC-CICD-2, SEC-AUTHN-4, SEC-CICD-1, SEC-HOST-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Editing Interface, Static Hosting
- **Interfaces / Operations**: The install script in `dalini/nina-site`; the installed file tree of `edit.nina-laaf.de` and of the OAuth helper on the VPS; file ownership and modes
- **Actors**: Maintainer (Ives) as VPS administrator
- **Preconditions**: REQ-EDIT-010, REQ-AUTHN-030
- **Data Classification**: Internal
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Supply Chain, Authorization
- **Threat References**: STRIDE Tampering and Elevation of Privilege: SECURITY.md Threat Model v3 T-82, T-46; CWE-494, CWE-732
- **Abuse / Misuse Case**: A leaked deploy key, a flaw in nginx or in the OAuth helper is used to replace the CMS bundle or the helper's code with a version that sends editors' tokens elsewhere
- **Trust Boundary**: TB-2 (code served to the editor's browser); inside the VPS: deploy and service accounts ↔ the installed Editing Interface files
- **Untrusted Inputs or Assertions**: The downloaded CMS bundle and the helper's dependencies until verified; the checkout's tag
- **Authoritative Enforcement Point**: The install script's verification step and the VPS operating system (file ownership and modes)
- **Independent Verification**: Checked from outside the account or process under test, against the live host or settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Software Supply Chain Security Cheat Sheet (`REF-SUPPLY`); OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-SUPPLY` and `REF-CICD` as cited in SECURITY.md SEC-CICD-2; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a checkout of a tagged release of `dalini/nina-site` on the test VPS, when Ives runs the install script, then it verifies the bundle checksum and the helper's lockfile, installs the CMS bundle, the CMS configuration and the helper's code and lockfile, every installed file is owned by root and readable but not writable by nginx and by the helper's account, and the Editing Interface opens with the installed release.
2. **AC-02 — Boundary or failure behavior**: Given a bundle whose checksum differs from the pinned value, a helper dependency that does not match the lockfile, or a checkout that is not at a release tag, when the install script runs, then it stops with a non-zero exit status before changing anything, and the previously installed files stay in place and keep working.
3. **AC-03 — Boundary or failure behavior**: Given the installed files, when the web server's account, the OAuth helper's account and the deploy account try to change, replace or delete any of them, and each production and preview deploy key tries to write to their paths, then every attempt is denied.
4. **AC-04 — Prohibited behavior**: Given the workflows and deploy configuration of `dalini/nina-site`, when they are reviewed, then no pipeline step, deploy key or deploy account installs or writes the Editing Interface files, and the deploy account can write nothing under `edit.nina-laaf.de` except the publish status file.
5. **AC-05 — Additional criterion**: Given a new Sveltia CMS or helper release, when Ives follows the documented update procedure (tag a release, check it out on the VPS, run the install script), then the new files replace the old ones in one run with the same ownership and modes, and the runbook records the installed release tag.

## Failure Behavior

- **On Invalid Input**: A checkout that is not at a release tag is refused before anything changes
- **On Authentication Failure**: N/A: run by Ives as VPS administrator over SSH with keys (SEC-HOST-2)
- **On Authorization Failure**: Write attempts by other accounts and keys are denied by the operating system; nothing changes
- **On Security-Decision Failure**: Deny by default: a failed checksum or lockfile check installs nothing
- **On External Dependency Failure**: The bundle or a dependency cannot be fetched: the script stops before changing anything; the installed release keeps running
- **On System Error**: The script stops with a non-zero exit status and leaves the previously installed files in place; a partly copied release is never served
- **Logging / Audit**: The script prints the release tag and the verified checksums; the installed tag is recorded in the runbook in `docs/ops/` (no secrets)
- **Alerting**: N/A: run by hand; a failure is shown to Ives at the terminal

## Test Strategy

- **Unit Tests**: Tag check; checksum and lockfile verification paths (match, mismatch, missing)
- **Integration Tests**: Install, update and failed install on the test VPS; Editing Interface opens after each successful run
- **Security Tests**: Write attempts on every installed path from nginx's, the helper's and the deploy account's shells and with each deploy key; review of workflows for any step that targets the Editing Interface paths
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-100/AC-01`; AC-02 → `test:EDIT-100/AC-02`; AC-03 → `test:EDIT-100/AC-03`; AC-04 → `test:EDIT-100/AC-04`; AC-05 → `test:EDIT-100/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-AUTHN-030 ({{ISSUE_URL:REQ-AUTHN-030}})
- **Downstream Requirements**: None
- **External Dependencies**: Sveltia CMS releases and their published checksums; the Deno module sources in the helper's lockfile
- **Dependency Assumptions**: Sveltia CMS publishes a checksum or the bundle is locked in the lockfile (SEC-CICD-2)
- **Failure Impact**: Without the installed files Nina cannot edit (FR-10.1); with writable files a leaked key or a server flaw can replace the code that holds editors' tokens (T-82)

## Implementation Notes

- **Constraints**: The install script and the pinned versions live in `dalini/nina-site`, where only Ives writes (DR-8); files owned by root, read-only to nginx and the helper's account (SEC-CICD-2); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Installing from an untagged checkout or a CDN; a pipeline job or deploy key that writes the Editing Interface files; making the files writable by nginx, the helper or the deploy account to simplify updates
- **Implementation Guidance**: Install into a new directory and switch over once the copy is complete, so a failed run never leaves a half-installed release.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: It is the supply-chain and file-permission control for the code that holds every editor's token, with a verification step that must fail closed.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-54: Ives installs the Editing Interface files by hand from a tagged, verified release of `dalini/nina-site`; root-owned, read-only to nginx and the helper; no deploy key writes them (SEC-CICD-2).
