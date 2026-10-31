## Metadata

- **ID**: REQ-INSTAGRAM-030
- **Title**: Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: REQUIREMENTS.md FR-8.2, FR-8.5; SECURITY.md SEC-PRIV-3, SEC-OUT-3, SEC-SECRET-3, SEC-INPUT-1, SEC-INPUT-5, SEC-CICD-6; ARCHITECTURE.md Instagram Feed Integration, DR-5; parent {{ISSUE_URL:REQ-INSTAGRAM-000}}

## Requirement

- **Statement**: The Site Build's Instagram step MUST call the Instagram API with Instagram Login (scope `instagram_business_basic` only) over HTTPS with certificate validation, fetch the recent posts of @ninalaaf, download their images into the build, process them like Content Store images (REQ-IMAGES-010, -020) and write a neutral post list (image, caption, link, date); a post whose media is not an image, exceeds 10 MB or 40 megapixels, or whose link is not `https:`, MUST be left out like a hidden post without failing the build.
- **Rationale**: Visitors never contact Instagram (SEC-PRIV-3); fetched data is untrusted (T-38); one bad post must not block publishing (T-68).
- **Assumptions**: None
- **Out of Scope**: How the token reaches this step from the VPS, and its monthly refresh: decided in step 10 again and drafted by the step 9 re-run (PQ-33). Reuse after a failed fetch (REQ-INSTAGRAM-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Instagram Feed Integration (build-time model (b)); Site Build stage that receives the Instagram token (REQ-PIPELINE-090); DR-5
- **Security Traceability**: SEC-PRIV-3, SEC-OUT-3, SEC-SECRET-3 (scope), SEC-INPUT-1, SEC-INPUT-5, SEC-CICD-6

## Scope

- **Applies To**: Background Processing
- **Components**: Instagram Feed Integration, Site Build
- **Interfaces / Operations**: Instagram fetch step; neutral post list consumed by REQ-INSTAGRAM-020
- **Actors**: Build identity (Instagram step); Instagram (external); the @ninalaaf account, which may be taken over (T-36)
- **Preconditions**: REQ-INSTAGRAM-020, REQ-IMAGES-020, REQ-IMAGES-040, REQ-PIPELINE-090
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Privacy, Availability
- **Control Layers**: Input Validation, Data Protection
- **Threat References**: STRIDE Tampering / Denial of Service / Elevation of Privilege: SECURITY.md Threat Model v2 T-36, T-37, T-38, T-39, T-68; CWE-295, CWE-434
- **Abuse / Misuse Case**: After an account takeover, a post with a video, a 60 MP image or a crafted file is published to break the build (T-68)
- **Trust Boundary**: TB-5 (Instagram ↔ Site Build)
- **Untrusted Inputs or Assertions**: Every API response, caption, link and media file from Instagram
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: The Site Build checks every fetched item itself; nothing from Instagram is trusted
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

1. **AC-01 — Expected behavior**: Given a mock API returning 8 image posts, when the step runs, then the post list holds them with self-hosted image paths, captions, `https:` permalinks and dates, and no Instagram or Meta media URL appears in the output.
2. **AC-02 — Boundary or failure behavior**: Given posts whose media is a video, a non-image file, a 12 MB image or a 45 MP image, or whose permalink is `http:`, when the step runs, then each is left out, the others are kept, and the build succeeds.
3. **AC-03 — Boundary or failure behavior**: Given the API endpoint presents an invalid TLS certificate, when the step runs, then nothing is fetched and the step reports a failed refresh.
4. **AC-04 — Prohibited behavior**: Given the step's configuration, when it is reviewed, then it requests only `instagram_business_basic`, never a publishing or account-management scope, and the token appears in no log or output file.

## Failure Behavior

- **On Invalid Input**: A post failing a check is left out and logged by post identifier; the build continues (FR-8.5, SEC-OUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: a post whose checks cannot complete is left out; the build continues
- **On External Dependency Failure**: Instagram unreachable, token rejected or response malformed: the step reports a failed refresh and REQ-INSTAGRAM-040 reuses the last posts; all other content still publishes (FR-8.5)
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: Left-out posts are logged by post identifier and failed check; captions, media URLs and the token are not logged
- **Alerting**: N/A: a failed refresh is logged and does not fail the build; the token-refresh alert is decided in step 10 again; drafted by the step 9 re-run (PQ-33)

## Test Strategy

- **Unit Tests**: Response parsing; per-post checks; neutral list writer
- **Integration Tests**: Build against a mock Instagram API serving good, bad and hostile responses
- **Security Tests**: Hostile fixtures: markup in captions, crafted images, oversize media, redirects to other hosts, invalid certificates; secret scan of logs and output
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:INSTAGRAM-030/AC-01`; AC-02 → `test:INSTAGRAM-030/AC-02`; AC-03 → `test:INSTAGRAM-030/AC-03`; AC-04 → `test:INSTAGRAM-030/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Mock Instagram API over HTTPS with a test certificate authority; a test token; the build stage layout of REQ-PIPELINE-090

## Dependencies

- **Upstream Requirements**: REQ-INSTAGRAM-020 ({{ISSUE_URL:REQ-INSTAGRAM-020}}), REQ-IMAGES-020 ({{ISSUE_URL:REQ-IMAGES-020}}), REQ-IMAGES-040 ({{ISSUE_URL:REQ-IMAGES-040}}), REQ-PIPELINE-090 ({{ISSUE_URL:REQ-PIPELINE-090}})
- **Downstream Requirements**: REQ-INSTAGRAM-040 ({{ISSUE_URL:REQ-INSTAGRAM-040}})
- **External Dependencies**: Instagram API with Instagram Login (@ninalaaf must be a Business or Creator account)
- **Dependency Assumptions**: The API returns recent media with type, media URL, caption, permalink and timestamp; responses may be malicious
- **Failure Impact**: Feed refresh fails; the home page shows the last refreshed posts (REQ-INSTAGRAM-040)

## Implementation Notes

- **Constraints**: Instagram API with Instagram Login, `instagram_business_basic` (ARCHITECTURE.md Instagram Feed Integration); the step is the only content-side step that receives the Instagram token (SEC-CICD-6); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Hot-linking Instagram media; passing Instagram API data beyond the neutral post list (DR-5); failing the build because of one post
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Security-sensitive integration with untrusted external data, a secret and per-item failure handling.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Answered in step 10 again (`.planning/OPEN_QUESTIONS.md`): PQ-33: each secret file is owned by the one unprivileged account that uses it; the Instagram token belongs to the deploy account, which also runs the refresh (SEC-SECRET-1). Parts that were excluded as blocked scope are drafted by the step 9 re-run.
