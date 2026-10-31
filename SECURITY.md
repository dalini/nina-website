# SECURITY

`SECURITY.md` owns the security posture of nina-laaf.de: threat model, security requirements, controls and trust-boundary enforcement. Behavior: `REQUIREMENTS.md`. Components and boundaries: `ARCHITECTURE.md`. Browser-facing design: `DESIGN.md`.

## Required Security Inputs

- Requirements source: REQUIREMENTS.md
- Design source: DESIGN.md
- Architecture source: ARCHITECTURE.md
- System purpose: Public portfolio website for artist Nina Laaf, edited by her through a web-based editing interface.
- Application profile: Statically rendered public website (no server-side code serving visitors, no visitor accounts) plus a web-based content editing interface and a build/publish pipeline.
- Users / actors / roles: Visitor (anonymous, read-only); Editor (Nina and people she authorizes); build/deploy service identity; Instagram (external).
- Public interfaces and trust boundaries: TB-1 visitor browser ↔ Static Hosting; TB-2 editor browser ↔ Editing Interface; TB-3 Editing Interface ↔ Content Store; TB-4 Content Store ↔ Site Build ↔ Static Hosting (pipeline); TB-5 Instagram ↔ Public Site or Site Build (OQ-6).
- Sensitive or regulated data: Editor credentials and sessions; build/deploy and Instagram access secrets; Nina's email address (published on purpose); image metadata (EXIF, possibly location) in uploaded photos; unpublished content. No visitor personal data is collected by the site itself; hosting access logs: UNKNOWN.
- External integrations: Instagram (feed); hosting provider and editing system (both TO BE DECIDED).
- Authentication model: Editors only; mechanism TO BE DECIDED. Visitors: none.
- Authorization model: Single role "editor" with full content rights; anyone else has read-only access to published content (FR-10.5).
- Session model: TO BE DECIDED (editing interface only).
- Deployment and CI/CD model: TO BE DECIDED (ARCHITECTURE.md AQ-4).
- Applicable privacy or regulatory obligations: Operator in Germany: GDPR; Impressum and Datenschutzerklärung required (PROJECT_NOTES.md §3, FR-9.1). Exact legal wording: TO BE DECIDED (outside this document).
- Security assurance target: TO BE DECIDED
- Security verification reference: OWASP ASVS 5.0.0
- Threat model status: TO BE DECIDED

## Selected Security References and Prompt Imports

No local secure-coding prompt library was found in this repository. Selected public references (cited, not copied; ASVS identifiers are not used until verified):

- `REF-ASVS-5` OWASP ASVS 5.0.0 — https://github.com/OWASP/ASVS/releases/tag/v5.0.0_release
- `REF-PC-2024` OWASP Top 10 Proactive Controls 2024 — https://top10proactive.owasp.org/archive/2024/the-top-10/
- `REF-XSS` OWASP XSS Prevention Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
- `REF-AUTH` OWASP Authentication Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
- `REF-SESSION` OWASP Session Management Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html
- `REF-INPUT` OWASP Input Validation Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html
- `REF-SECRETS` OWASP Secrets Management Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html
- `REF-SSDF` NIST SSDF 1.1 — https://csrc.nist.gov/pubs/sp/800/218/final
- `REF-CICD` OWASP CI/CD Security Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/CI_CD_Security_Cheat_Sheet.html
- `REF-SUPPLY` OWASP Software Supply Chain Security Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Software_Supply_Chain_Security_Cheat_Sheet.html
- `REF-DEPS` OWASP Vulnerable Dependency Management Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Vulnerable_Dependency_Management_Cheat_Sheet.html

## Provisional Security Rules

### Public site and HTTP boundary (TB-1)

- **SEC-HTTP-1** Static Hosting MUST serve every page and asset of `nina-laaf.de` over HTTPS only and MUST redirect HTTP to HTTPS.
  - **Applies to:** Static Hosting, all public responses
  - **Verification:** Automated check that HTTP requests redirect and HTTPS responses send HSTS (max-age TO BE DECIDED)
  - **References:** `REF-ASVS-5`
- **SEC-HTTP-2** Public responses MUST send a Content Security Policy that allows resources only from the site's own origin, plus Instagram origins only if the feed model in OQ-6 requires them. Exact directives: TO BE DECIDED.
  - **Applies to:** Static Hosting, Public Site
  - **Verification:** Header check in CI; browser test that no third-party request occurs on page load
  - **References:** `REF-XSS`, `REF-ASVS-5`
- **SEC-HTTP-3** Public responses MUST send `X-Content-Type-Options: nosniff` and MUST forbid framing of the site by other origins.
  - **Applies to:** Static Hosting
  - **Verification:** Header check in CI
  - **References:** `REF-ASVS-5`

### Third-party content and privacy

- **SEC-PRIV-1** The Public Site MUST NOT request any third-party resource (fonts, scripts, images, embeds) before the visitor gives consent; fonts and all site assets MUST be self-hosted (NFR-3).
  - **Applies to:** Public Site, Site Build templates
  - **Verification:** Automated browser test recording all network requests on every page type before consent
  - **References:** `REF-PC-2024`
- **SEC-PRIV-2** If the browser-loaded feed model is chosen (OQ-6 (a)), Instagram content MUST load only after the visitor activates the consent placeholder on that page, and the choice MUST NOT be stored beyond what the Datenschutzerklärung describes (storage: TO BE DECIDED).
  - **Applies to:** Instagram Feed Integration, Public Site
  - **Verification:** Browser test: no Instagram request before the click; requests only after it
  - **References:** `REF-PC-2024`
- **SEC-PRIV-3** If the build-time feed model is chosen (OQ-6 (b)), the Site Build MUST copy post images into the static output so that visitors never request them from Instagram.
  - **Applies to:** Instagram Feed Integration, Site Build
  - **Verification:** Build output contains no Instagram/Meta URLs for media; browser test as SEC-PRIV-1
  - **References:** `REF-PC-2024`
- **SEC-PRIV-4** The Site Build or upload process MUST remove EXIF and other embedded metadata (including location) from published images, except fields Nina chooses to keep (TO BE DECIDED).
  - **Applies to:** Editing Interface uploads, Site Build image processing
  - **Verification:** Automated check that published images contain no GPS or device metadata
  - **References:** `REF-PC-2024`
- **SEC-PRIV-5** The site MUST NOT include analytics or tracking unless decided in SQ-5; any analytics added MUST be cookieless and self-hosted.
  - **Applies to:** Public Site
  - **Verification:** Network test (SEC-PRIV-1); dependency review
  - **References:** `REF-PC-2024`

### Output encoding and safe rendering

- **SEC-OUT-1** The Site Build MUST HTML-encode all editor-provided text by default. Rich text MUST be rendered from a restricted format (e.g. headings, emphasis, links, lists); raw HTML, scripts, inline event handlers, iframes and `javascript:` URLs in content MUST be removed or rejected.
  - **Applies to:** Site Build templates, content model
  - **Verification:** Build tests with content containing `<script>`, event-handler attributes and `javascript:` links; output must contain none of them
  - **References:** `REF-XSS`
- **SEC-OUT-2** External links from content (exhibition links, gallery, Instagram) MUST use only `https:` or `mailto:` schemes and MUST open without giving the target page access to the site (`rel="noopener"`).
  - **Applies to:** Content model, Site Build
  - **Verification:** Build test rejecting other schemes
  - **References:** `REF-XSS`, `REF-INPUT`
- **SEC-OUT-3** Content imported from Instagram (captions, links) MUST be treated as untrusted text and encoded like editor content.
  - **Applies to:** Instagram Feed Integration, Site Build
  - **Verification:** Build test with a caption containing markup
  - **References:** `REF-XSS`

### Editing access (TB-2, TB-3)

- **SEC-AUTHN-1** The Editing Interface MUST require authentication for every action that reads unpublished content or changes content. Mechanism, factors and session timeouts: TO BE DECIDED (SQ-1).
  - **Applies to:** Editing Interface
  - **Verification:** Automated test: unauthenticated requests to every editing action are rejected
  - **References:** `REF-AUTH`, `REF-SESSION`
- **SEC-AUTHZ-1** The Content Store MUST accept changes only from authenticated editors on its allow-list and MUST deny everything else by default (FR-10.5).
  - **Applies to:** Content Store, Editing Interface
  - **Verification:** Test with a non-listed account: change is rejected and the site is unchanged
  - **References:** `REF-ASVS-5`, `REF-PC-2024`
- **SEC-AUTHZ-2** Adding or removing editors MUST be possible only for Nina (or an owner she names) and MUST be recorded in the Content Store's change history.
  - **Applies to:** Editing Interface, Content Store
  - **Verification:** Manual review of access settings; history shows the change
  - **References:** `REF-ASVS-5`

### Uploads and content validation

- **SEC-INPUT-1** The Editing Interface MUST accept only image files and PDFs for upload, check the actual file type (not only the extension), and reject files above a size limit (TO BE DECIDED).
  - **Applies to:** Editing Interface, Content Store
  - **Verification:** Upload tests with a renamed executable, an SVG with script, and an oversized file
  - **References:** `REF-INPUT`
- **SEC-INPUT-2** SVG uploads MUST be rejected unless sanitized; this applies to editor uploads, not to the site's own `logo.svg`.
  - **Applies to:** Editing Interface
  - **Verification:** Upload test with an SVG containing script
  - **References:** `REF-INPUT`, `REF-XSS`
- **SEC-INPUT-3** The Site Build MUST fail and publish nothing when content violates the content model (FR-10.6, ARCHITECTURE.md DR-3), leaving the previous site online.
  - **Applies to:** Site Build, Static Hosting
  - **Verification:** Build with an invalid work entry: build fails; live site unchanged
  - **References:** `REF-INPUT`
- **SEC-INPUT-4** Unpublished content MUST NOT appear in the static output (FR-11.2).
  - **Applies to:** Site Build
  - **Verification:** Build test with an unpublished work: no page, no image, no listing entry
  - **References:** `REF-ASVS-5`

### Secrets and pipeline (TB-4)

- **SEC-SECRET-1** Deploy credentials, editing-system credentials and any Instagram access token MUST be kept out of the repository, generated output, client code and logs, and stored in the CI/hosting secret store (product TO BE DECIDED).
  - **Applies to:** Site Build, Static Hosting, Instagram Feed Integration
  - **Verification:** Secret scanning on every change; inspection of build output
  - **References:** `REF-SECRETS`
- **SEC-CICD-1** The build/deploy identity MUST have only the permissions needed to publish the site, and only changes on the main branch of the Content Store MAY deploy to production.
  - **Applies to:** Site Build, Static Hosting
  - **Verification:** Review of CI permissions and branch protection
  - **References:** `REF-CICD`, `REF-SSDF`
- **SEC-CICD-2** Third-party CI actions/steps MUST be pinned to exact versions (commit SHA where the platform supports it).
  - **Applies to:** Site Build pipeline
  - **Verification:** CI configuration review
  - **References:** `REF-CICD`, `REF-SUPPLY`

## Requirement and Architecture Traceability

| Rule | Requirements | Component / boundary | Status |
|---|---|---|---|
| SEC-HTTP-1, -3 | NFR-4 | Static Hosting (TB-1) | PROVISIONAL |
| SEC-HTTP-2 | NFR-3, FR-8.3 | Static Hosting, Public Site | PARTIALLY DEFINED |
| SEC-PRIV-1 | NFR-3 | Public Site, Site Build | CONFIRMED |
| SEC-PRIV-2, -3 | FR-8.2–8.4 | Instagram Feed Integration (TB-5) | TO BE DECIDED (OQ-6) |
| SEC-PRIV-4 | FR-10.2 | Editing Interface, Site Build | PROVISIONAL |
| SEC-PRIV-5 | NFR-3 | Public Site | PROVISIONAL (OQ-8) |
| SEC-OUT-1, -2 | FR-1.4, FR-4.3, FR-5.1, FR-10.1 | Site Build | PROVISIONAL |
| SEC-OUT-3 | FR-8.2 | Instagram Feed Integration, Site Build | PROVISIONAL |
| SEC-AUTHN-1 | FR-10.5 | Editing Interface (TB-2) | PARTIALLY DEFINED |
| SEC-AUTHZ-1, -2 | FR-10.5 | Content Store, Editing Interface (TB-3) | CONFIRMED |
| SEC-INPUT-1, -2 | FR-10.2 | Editing Interface | PROVISIONAL |
| SEC-INPUT-3 | FR-10.6 | Site Build, Static Hosting | PROVISIONAL |
| SEC-INPUT-4 | FR-11.2 | Site Build | CONFIRMED |
| SEC-SECRET-1 | FR-8.2, FR-10.4 | Site Build, Static Hosting, Instagram Feed Integration (TB-4) | PROVISIONAL |
| SEC-CICD-1, -2 | FR-10.4 | Site Build pipeline (TB-4) | PROVISIONAL |

## Dependency Security Rules

These are prospective rules; no dependency has been assessed yet. References: `REF-SUPPLY`, `REF-DEPS`, `REF-SSDF`.

- **DEP-1** The project MUST NOT add a dependency when the standard library or a small amount of straightforward, non-security-sensitive first-party code is safer and sufficient. The project MUST NOT replace vetted cryptography, authentication, authorization, protocol parsing, output encoding, HTML sanitization, or other security-critical functionality with custom code merely to avoid a dependency.
- **DEP-2** The project SHOULD prefer zero new dependencies. Every new dependency MUST be justified in the pull request description, including its purpose and why existing code or platform functionality is insufficient.
- **DEP-3** A new dependency MUST show evidence of active maintenance through a stable release, security response, or substantive maintainer activity within the previous 12 months. A mature project with less frequent releases requires an explicit documented exception and evidence that security reports are still handled.
- **DEP-4** The project MUST use the latest stable release from the latest actively supported major version unless a documented compatibility constraint requires another actively supported major version. Deprecated, abandoned, end-of-life, or pre-release packages MUST NOT be introduced into production.
- **DEP-5** Before a dependency is added or updated, direct and transitive dependencies MUST be checked for known vulnerabilities. A dependency with a known unpatched vulnerability applicable to the intended use MUST NOT be introduced without explicit, time-bounded risk acceptance, documented compensating controls, and a remediation plan.
- **DEP-6** Dependency review MUST include the complete transitive dependency graph. A small direct dependency with a disproportionately large, opaque, abandoned, or unvetted transitive tree SHOULD be rejected.
- **DEP-7** Production builds MUST resolve dependencies to exact versions through a committed lockfile or equivalent ecosystem mechanism. Production and CI builds MUST use frozen or reproducible dependency resolution and MUST NOT resolve floating versions at build or deployment time.
- **DEP-8** When multiple suitable libraries exist, the project SHOULD prefer the library with the narrowest required scope, smallest dependency tree, active security response process, clear provenance, and established security track record.

## Prompt Placeholders To Resolve

- `{{CODE_QUALITY_PROMPT}}` — RESOLVED (no local prompt found): low cyclomatic and cognitive complexity; small cohesive functions and modules; separated presentation, business-rule, persistence and integration concerns; explicit error handling and trust-boundary transitions; no duplicated security-sensitive logic; testability without hidden global state.
- `{{API_SECURITY_PROMPT}}` — TO BE DECIDED: the public site has no API; the editing system's API and the Instagram access method are not yet chosen (AQ-3, OQ-6).
- `{{BACKEND_FRAMEWORK_PROMPT}}` — TO BE DECIDED: static site generator not chosen (AQ-1; Hugo is a candidate).
- `{{FRONTEND_FRAMEWORK_PROMPT}}` — TO BE DECIDED: no client framework chosen (AQ-2). `REF-XSS` serves as the rendering baseline meanwhile.
- `{{AUTH_PROMPT}}` — TO BE DECIDED: editor authentication mechanism not chosen (SQ-1); `REF-AUTH` and `REF-SESSION` remain provisional background.
- `{{DEPLOYMENT_PROMPT}}` — PARTIALLY RESOLVED: `REF-SSDF` and `REF-CICD` apply; provider-specific guidance TO BE DECIDED (AQ-4).

## Open Security Questions

- **SQ-1** How do editors authenticate to the Editing Interface (e.g. through the editing system's own login or an identity provider), and is a second factor required?
- **SQ-2** Who besides Nina may edit, and who may add or remove editors?
- **SQ-3** Where are deploy and Instagram secrets stored, and who can rotate them?
- **SQ-4** For the Instagram feed (OQ-6): if loaded in the browser, how is consent stored and for how long; if copied at build time, which access method and token lifetime?
- **SQ-5** Are analytics used at all (OQ-8)? If yes, which cookieless, self-hosted option?
- **SQ-6** What does the hosting provider log about visitors (IP addresses, retention), and how is that covered in the Datenschutzerklärung?
- **SQ-7** Which image metadata (e.g. copyright, photographer) should be kept when location and device data are removed (SEC-PRIV-4)?
- **SQ-8** Upload size limits for images and PDFs (SEC-INPUT-1)?
- **SQ-9** What is the security assurance target (e.g. a selected ASVS 5.0.0 level)?
