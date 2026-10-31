# SECURITY

`SECURITY.md` owns the security posture of nina-laaf.de: threat model, security requirements, controls and trust-boundary enforcement. Behavior: `REQUIREMENTS.md`. Components and boundaries: `ARCHITECTURE.md`. Browser-facing design: `DESIGN.md`.

## Required Security Inputs

- Requirements source: REQUIREMENTS.md
- Design source: DESIGN.md
- Architecture source: ARCHITECTURE.md
- System purpose: See REQUIREMENTS.md
- Application profile: See ARCHITECTURE.md (Initial Architecture): static public site with no server-side code serving visitors, plus an editing interface and a build/publish pipeline.
- Users / actors / roles: Visitor (anonymous, read-only); Editor (Nina and people she authorizes); build/deploy service identity; Instagram (external). ASSUMPTION: a maintainer who changes templates, the pipeline and hosting settings; who that is: SQ-10.
- Public interfaces and trust boundaries: TB-1 visitor browser ↔ Static Hosting (including the domain and DNS of `nina-laaf.de`; provider UNKNOWN); TB-2 editor browser ↔ Editing Interface; TB-3 Editing Interface ↔ Content Store; TB-4 Content Store ↔ Site Build ↔ Static Hosting (pipeline); TB-5 Instagram ↔ Public Site or Site Build (OQ-6).
- Sensitive or regulated data: Editor credentials and sessions; build/deploy and Instagram access secrets; Nina's email address (published on purpose); image metadata (EXIF, possibly location) in uploaded photos; unpublished content. No visitor personal data is collected by the site itself; hosting access logs: UNKNOWN.
- External integrations: Instagram (feed); hosting provider and editing system (both TO BE DECIDED: ARCHITECTURE.md AQ-3, AQ-4).
- Authentication model: Editors only; mechanism TO BE DECIDED (SQ-1). Visitors: none.
- Authorization model: Single role "editor" with full content rights; anyone else has read-only access to published content (FR-10.5).
- Session model: TO BE DECIDED (editing interface only).
- Deployment and CI/CD model: TO BE DECIDED (ARCHITECTURE.md AQ-4).
- Applicable privacy or regulatory obligations: Operator in Germany: GDPR; Impressum and Datenschutzerklärung required (REQUIREMENTS.md FR-9.1). Exact legal wording: TO BE DECIDED (outside this document).
- Security assurance target: TO BE DECIDED
- Security verification reference: OWASP ASVS 5.0.0
- Threat model status: STRIDE threat model v1, 2026-10-31, of the documented system before implementation (Threat Model below: T-1 to T-42). Threats that depend on open decisions are recorded conditionally; re-run when SQ-1, OQ-6, AQ-2, AQ-3, AQ-4 or AQ-5 is decided, and before the first release.

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
- `REF-TM` OWASP Threat Modeling Cheat Sheet (STRIDE method used for the Threat Model) — https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html

## Provisional Security Rules

### Public site and HTTP boundary (TB-1)

- **SEC-HTTP-1** Static Hosting MUST serve every page and asset of `nina-laaf.de` over HTTPS only, MUST redirect HTTP to HTTPS, and MUST send an HSTS header (max-age TO BE DECIDED).
  - **Applies to:** Static Hosting, all public responses
  - **Verification:** Automated check that HTTP requests redirect and HTTPS responses send HSTS
  - **References:** `REF-ASVS-5`
- **SEC-HTTP-2** Public responses MUST send a Content Security Policy that allows resources only from the site's own origin, plus Instagram origins only if the feed model in OQ-6 requires them. Exact directives: TO BE DECIDED.
  - **Applies to:** Static Hosting, Public Site
  - **Verification:** Header check in CI; browser test that no third-party request occurs on page load
  - **References:** `REF-XSS`, `REF-ASVS-5`
- **SEC-HTTP-3** Public responses MUST send `X-Content-Type-Options: nosniff` and MUST forbid framing of the site by other origins.
  - **Applies to:** Static Hosting
  - **Verification:** Header check in CI
  - **References:** `REF-ASVS-5`
- **SEC-HTTP-4** The Editing Interface MUST be served over HTTPS only, MUST redirect HTTP to HTTPS and send an HSTS header, MUST send `X-Content-Type-Options: nosniff`, and MUST forbid framing by other origins.
  - **Applies to:** Editing Interface (TB-2)
  - **Verification:** The SEC-HTTP-1 and SEC-HTTP-3 header checks, run against the Editing Interface
  - **References:** `REF-ASVS-5`
  - **Threats:** T-3, T-4

### Domain, DNS and hosting accounts (TB-1)

- **SEC-HOST-1** The registrar, DNS and hosting accounts for `nina-laaf.de` MUST be held by named people (SQ-13) with individual logins and a second factor wherever the provider offers one; renewal notices MUST go to a monitored address; a DNS record MUST be removed when the target it points to is retired.
  - **Applies to:** Domain and DNS of `nina-laaf.de`, Static Hosting
  - **Verification:** Yearly manual review of account holders, second factors, renewal date and DNS records against live targets
  - **References:** `REF-ASVS-5`, `REF-SSDF`
  - **Threats:** T-26, T-27

### Third-party content and privacy

- **SEC-PRIV-1** The Public Site MUST NOT request any third-party resource (fonts, scripts, images, embeds such as Instagram/Meta, video or maps) in the visitor's browser before the visitor gives consent; fonts and all site assets MUST be self-hosted.
  - **Applies to:** Public Site, Site Build templates
  - **Verification:** Automated browser test recording all network requests on every page type before consent
  - **References:** `REF-PC-2024`
- **SEC-PRIV-2** If the browser-loaded feed model is chosen (OQ-6 (a)), Instagram content MUST load only after the visitor activates the consent placeholder on that page, and the choice MUST NOT be stored beyond what the Datenschutzerklärung describes (storage: TO BE DECIDED, SQ-4).
  - **Applies to:** Instagram Feed Integration, Public Site
  - **Verification:** Browser test: no Instagram request before the click; requests only after it
  - **References:** `REF-PC-2024`
- **SEC-PRIV-3** If the build-time feed model is chosen (OQ-6 (b)), the Site Build MUST copy post images into the static output so that visitors never request them from Instagram.
  - **Applies to:** Instagram Feed Integration, Site Build
  - **Verification:** Build output contains no Instagram/Meta URLs for media; browser test as SEC-PRIV-1
  - **References:** `REF-PC-2024`
- **SEC-PRIV-4** The Site Build or upload process MUST remove EXIF and other embedded metadata (including location) from published images, and MUST check published PDFs for embedded metadata (author, software, location in embedded images) and clean or reject them, except fields Nina chooses to keep (TO BE DECIDED, SQ-7).
  - **Applies to:** Editing Interface uploads, Site Build image processing
  - **Verification:** Automated check that no image or PDF file anywhere in the build output contains GPS or device metadata
  - **References:** `REF-PC-2024`
  - **Threats:** T-25 (strengthened: PDFs, whole build output)
- **SEC-PRIV-5** The site MUST NOT include analytics or tracking unless REQUIREMENTS.md OQ-8 decides for them; any analytics added MUST be cookieless and self-hosted (SQ-5).
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
- **SEC-OUT-3** Content imported from Instagram (captions, links) MUST be treated as untrusted text and encoded like editor content. The Site Build MUST fetch it over HTTPS with certificate validation, and fetched media MUST pass the type checks of SEC-INPUT-1 and the limits of SEC-INPUT-5.
  - **Applies to:** Instagram Feed Integration, Site Build
  - **Verification:** Build test with a caption containing markup; build test with a fetched file that is not an image or exceeds the SEC-INPUT-5 limit
  - **References:** `REF-XSS`
  - **Threats:** T-38 (strengthened: transport and media checks)
- **SEC-OUT-4** The Editing Interface MUST display content (lists, forms, previews) with the same encoding and restricted rich-text rules as SEC-OUT-1.
  - **Applies to:** Editing Interface
  - **Verification:** Open content containing `<script>`, event-handler attributes and `javascript:` links in every Editing Interface view: no script runs
  - **References:** `REF-XSS`
  - **Threats:** T-5
- **SEC-OUT-5** If the Public Site has client-side code (ARCHITECTURE.md AQ-2), that code MUST insert values from the URL, browser storage or feed data into the page only as text, never as HTML.
  - **Applies to:** Public Site client-side code
  - **Verification:** Browser test with markup in the query string, fragment and stored values: rendered as text, no script runs
  - **References:** `REF-XSS`
  - **Threats:** T-32
- **SEC-OUT-6** If the browser-loaded feed model is chosen (OQ-6 (a)), script from Instagram/Meta MUST NOT run in the site's own origin. Isolation method: TO BE DECIDED (SQ-4).
  - **Applies to:** Instagram Feed Integration, Public Site
  - **Verification:** Browser test after consent: no third-party script executes in the site's origin
  - **References:** `REF-XSS`, `REF-PC-2024`
  - **Threats:** T-35

### Editing access (TB-2, TB-3)

- **SEC-AUTHN-1** The Editing Interface MUST require authentication for every action that reads unpublished content or changes content. Mechanism, factors and session timeouts: TO BE DECIDED (SQ-1).
  - **Applies to:** Editing Interface
  - **Verification:** Automated test: unauthenticated requests to every editing action are rejected
  - **References:** `REF-AUTH`, `REF-SESSION`
- **SEC-AUTHN-2** Every editor MUST sign in with an individual account (no shared accounts), and the sign-in (the editing system's own or an identity provider's) MUST resist credential guessing by limiting or slowing repeated failed attempts. Whether a second factor is required: TO BE DECIDED (SQ-1).
  - **Applies to:** Editing Interface (TB-2)
  - **Verification:** Review: one account per listed editor; automated test: repeated failed sign-ins are limited
  - **References:** `REF-AUTH`
  - **Threats:** T-1
- **SEC-AUTHN-3** Editing sessions MUST get a new session identifier at sign-in, end on sign-out, expire after inactivity and after a maximum lifetime (values TO BE DECIDED, SQ-1), and MUST NOT appear in URLs or logs. State-changing editing actions MUST be protected against cross-site request forgery.
  - **Applies to:** Editing Interface (TB-2)
  - **Verification:** Automated tests: session invalid after sign-out and timeout; a forged cross-site request is rejected; URLs and logs contain no session values
  - **References:** `REF-SESSION`
  - **Threats:** T-2, T-3
- **SEC-AUTHZ-1** The Content Store MUST accept changes only from authenticated editors on its allow-list (Nina and the people she authorizes) and MUST deny everything else by default, leaving the site unchanged.
  - **Applies to:** Content Store, Editing Interface
  - **Verification:** Test with a non-listed account: change is rejected and the site is unchanged
  - **References:** `REF-ASVS-5`, `REF-PC-2024`
- **SEC-AUTHZ-2** Adding or removing editors MUST be possible only for Nina (or an owner she names) and MUST be recorded in the Content Store's change history.
  - **Applies to:** Editing Interface, Content Store
  - **Verification:** Manual review of access settings; history shows the change
  - **References:** `REF-ASVS-5`
- **SEC-AUTHZ-3** Editors' write access, and the credential the Editing Interface uses to write to the Content Store, MUST be limited to content and media (plus adding and removing editors under SEC-AUTHZ-2). They MUST NOT be able to change templates, build configuration or pipeline definitions (ARCHITECTURE.md DR-8).
  - **Applies to:** Content Store, Editing Interface, Site Build (TB-3, TB-4)
  - **Verification:** Test with an editor account and with the Editing Interface credential: a change to a template or pipeline file is rejected
  - **References:** `REF-CICD`, `REF-ASVS-5`
  - **Threats:** T-11
- **SEC-AUTHZ-4** The Content Store, including its history and media originals, MUST NOT be publicly readable; read access is limited to editors, the build identity and the maintainers (SQ-10).
  - **Applies to:** Content Store (TB-3)
  - **Verification:** Unauthenticated read request is rejected; review of the access list
  - **References:** `REF-ASVS-5`
  - **Threats:** T-13

### Change history and recovery

- **SEC-AUDIT-1** Every change in the Content Store MUST record which authenticated editor made it, taken from the Editing Interface sign-in rather than from self-declared author fields, and when. Editors, the Editing Interface and the build identity MUST NOT be able to rewrite or delete history. Exceptions for content that must be removed for legal reasons: TO BE DECIDED (SQ-11).
  - **Applies to:** Content Store, Editing Interface
  - **Verification:** Changes by two editors show each identity in the history; an attempt to rewrite history with editor, Editing Interface or build credentials is rejected
  - **References:** `REF-ASVS-5`
  - **Threats:** T-9, T-15
- **SEC-AUDIT-2** Every production deploy MUST record the Content Store version it was built from and what triggered it, and the previously deployed version MUST be restorable without a new content change.
  - **Applies to:** Site Build, Static Hosting (TB-4)
  - **Verification:** Review of deploy records; restore drill to the previous version
  - **References:** `REF-SSDF`
  - **Threats:** T-23, T-27
- **SEC-AVAIL-1** Content and media originals MUST be backed up to a location that does not depend on the Content Store's account or provider, and restoring from the backup MUST be tested. Frequency, location and retention: TO BE DECIDED (SQ-12).
  - **Applies to:** Content Store
  - **Verification:** Restore test into an empty store; the Site Build produces the same site from it
  - **References:** `REF-SSDF`
  - **Threats:** T-16

### Uploads and content validation

- **SEC-INPUT-1** The Editing Interface MUST accept only image files and PDFs for upload, check the actual file type (not only the extension), and reject files above a size limit (TO BE DECIDED, SQ-8).
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
- **SEC-INPUT-5** Image processing (at upload and in the Site Build, including Instagram media under OQ-6 (b)) MUST reject images whose pixel dimensions exceed a limit (TO BE DECIDED, SQ-8) before decoding them fully.
  - **Applies to:** Editing Interface uploads, Site Build image processing
  - **Verification:** Upload and build tests with a small file declaring extreme pixel dimensions: upload rejected; build fails under SEC-INPUT-3 without exhausting memory or time
  - **References:** `REF-INPUT`
  - **Threats:** T-8, T-38
- **SEC-INPUT-6** The Site Build MUST treat content fields as data: template, shortcode or other generator syntax inside content MUST NOT be evaluated.
  - **Applies to:** Site Build (TB-4)
  - **Verification:** Build test with generator template and shortcode syntax in a text field: the output shows it as encoded text
  - **References:** `REF-INPUT`
  - **Threats:** T-18

### Secrets and pipeline (TB-4)

- **SEC-SECRET-1** Deploy credentials, editing-system credentials and any Instagram access token MUST be kept out of the repository, generated output, client code and logs, and stored in the CI/hosting secret store (product TO BE DECIDED, SQ-3).
  - **Applies to:** Site Build, Static Hosting, Instagram Feed Integration
  - **Verification:** Secret scanning on every change; inspection of build output
  - **References:** `REF-SECRETS`
- **SEC-SECRET-2** Deploy and Instagram secrets MUST be available only to the production build of the Content Store's main branch, not to builds of other branches, previews or external contributions.
  - **Applies to:** Site Build pipeline (TB-4)
  - **Verification:** CI configuration review; a test build of another branch cannot read the secrets
  - **References:** `REF-CICD`, `REF-SECRETS`
  - **Threats:** T-20
- **SEC-SECRET-3** Instagram access (OQ-6 (b)) MUST use the narrowest read-only access the chosen method offers and MUST NOT allow posting or account changes.
  - **Applies to:** Instagram Feed Integration (TB-5)
  - **Verification:** Review of the granted access scope
  - **References:** `REF-SECRETS`
  - **Threats:** T-37
- **SEC-CICD-1** The build/deploy identity MUST have only the permissions needed to publish the site, and only changes on the main branch of the Content Store MAY deploy to production.
  - **Applies to:** Site Build, Static Hosting
  - **Verification:** Review of CI permissions and branch protection
  - **References:** `REF-CICD`, `REF-SSDF`
- **SEC-CICD-2** Third-party CI actions/steps, and build tools installed outside the lockfile (generator, image processing), MUST be pinned to exact versions (commit SHA where the platform supports it); downloaded tools MUST be verified against a published checksum or signature.
  - **Applies to:** Site Build pipeline
  - **Verification:** CI configuration review
  - **References:** `REF-CICD`, `REF-SUPPLY`
  - **Threats:** T-19 (strengthened: build tools, integrity check)
- **SEC-CICD-3** If the hosting or CI produces previews of unpublished content (ARCHITECTURE.md AQ-4), they MUST be reachable only by authenticated editors; otherwise previews MUST be turned off.
  - **Applies to:** Site Build pipeline, Static Hosting
  - **Verification:** Request a preview URL without signing in: denied
  - **References:** `REF-CICD`
  - **Threats:** T-21
- **SEC-CICD-4** Build and publish triggers MUST be authenticated; trigger secrets follow SEC-SECRET-1.
  - **Applies to:** Editing Interface, Site Build pipeline
  - **Verification:** An unauthenticated trigger request starts no build
  - **References:** `REF-CICD`
  - **Threats:** T-22
- **SEC-CICD-5** Only the Site Build's generated output MUST be deployed to Static Hosting; repository metadata, source content and configuration MUST NOT be reachable on the site.
  - **Applies to:** Site Build pipeline, Static Hosting
  - **Verification:** Post-deploy check that `/.git/`, source-content and configuration paths return not found
  - **References:** `REF-CICD`
  - **Threats:** T-29

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
| SEC-INPUT-4 | FR-10.1, FR-11.2 | Site Build | CONFIRMED |
| SEC-SECRET-1 | FR-8.2, FR-10.4 | Site Build, Static Hosting, Instagram Feed Integration (TB-4) | PROVISIONAL |
| SEC-CICD-1, -2 | FR-10.4 | Site Build pipeline (TB-4) | PROVISIONAL |
| SEC-HTTP-4 | FR-10.5 | Editing Interface (TB-2) | PROVISIONAL |
| SEC-HOST-1 | FR-7.1, NFR-4 | Domain and DNS, Static Hosting (TB-1) | PARTIALLY DEFINED (SQ-13) |
| SEC-OUT-4 | FR-10.1 | Editing Interface | PROVISIONAL |
| SEC-OUT-5 | FR-1.3, FR-8.4 | Public Site | TO BE DECIDED (AQ-2) |
| SEC-OUT-6 | FR-8.2, FR-8.3 | Instagram Feed Integration, Public Site (TB-5) | TO BE DECIDED (OQ-6) |
| SEC-AUTHN-2, -3 | FR-10.5 | Editing Interface (TB-2) | PARTIALLY DEFINED (SQ-1) |
| SEC-AUTHZ-3 | FR-10.5 | Content Store, Editing Interface, Site Build (TB-3, TB-4) | PROVISIONAL (SQ-10) |
| SEC-AUTHZ-4 | FR-10.5, FR-11.2 | Content Store (TB-3) | PROVISIONAL |
| SEC-AUDIT-1 | FR-10.7 | Content Store, Editing Interface | PARTIALLY DEFINED (SQ-11) |
| SEC-AUDIT-2 | FR-10.4 | Site Build, Static Hosting (TB-4) | PROVISIONAL |
| SEC-AVAIL-1 | FR-10.1 | Content Store | PARTIALLY DEFINED (SQ-12) |
| SEC-INPUT-5 | FR-10.2, NFR-1 | Editing Interface, Site Build | PARTIALLY DEFINED (SQ-8) |
| SEC-INPUT-6 | FR-10.1 | Site Build (TB-4) | PROVISIONAL |
| SEC-SECRET-2 | FR-10.4 | Site Build pipeline (TB-4) | PROVISIONAL |
| SEC-SECRET-3 | FR-8.2 | Instagram Feed Integration (TB-5) | TO BE DECIDED (OQ-6) |
| SEC-CICD-3, -4, -5 | FR-10.4, FR-11.2 | Site Build pipeline, Static Hosting (TB-4) | PROVISIONAL (AQ-4) |

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

## Threat Model

STRIDE per element (`REF-TM`) of the documented system; no implementation exists. Version 1, 2026-10-31. Threats that depend on an open decision say so and point to it.

### Analysis surface

- **Actors:** Visitor (anonymous); Editor (Nina and people she authorizes); Owner (Nina or an owner she names, SEC-AUTHZ-2); build/deploy identity; Instagram (external); maintainer (ASSUMPTION, SQ-10).
- **Components:** Editing Interface, Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration (ARCHITECTURE.md Initial Architecture).
- **Trust boundaries:** TB-1 to TB-5 (Required Security Inputs). TB-1 includes the domain and DNS of `nina-laaf.de`.
- **Data flows** (labels for this analysis):
  - DF-1 editor browser → Editing Interface: sign-in, edits, uploads.
  - DF-2 Editing Interface → Content Store: content and media writes.
  - DF-3 legacy import → Content Store (one-time, FR-11.1).
  - DF-4 publish trigger → Site Build: from the Editing Interface (FR-10.4) and on a schedule (ASSUMPTION, ARCHITECTURE.md Site Build).
  - DF-5 Content Store, templates and design tokens → Site Build.
  - DF-6 Site Build → Static Hosting: deploy.
  - DF-7 Static Hosting → visitor browser.
  - DF-8 Instagram → visitor browser after consent (OQ-6 (a)) or → Site Build (OQ-6 (b)).
  - DF-9 publish status read by the Editing Interface (FR-10.8, added for T-24).
- **Browser-facing surfaces** (DESIGN.md Components): links and language switch, images with caption and credit, downloads, the consent placeholder and its button; in the Editing Interface, inputs and form feedback.
- **Sensitive data:** see Required Security Inputs.

### STRIDE coverage

Every element was checked against all six categories. A cell lists the threats found or states that none is credible.

| Element | Spoofing | Tampering | Repudiation | Information Disclosure | Denial of Service | Elevation of Privilege |
|---|---|---|---|---|---|---|
| Editing Interface (TB-2, DF-1) | T-1, T-2 | T-3, T-7 | T-9 | T-4 | T-8, T-10 | T-5, T-6 |
| Content Store (TB-3, DF-2) | No credible threat identified beyond T-1 and T-12 | T-12 | T-15 | T-13, T-14 | T-16, T-17 | T-11 |
| Site Build and pipeline (TB-4, DF-4 to DF-6) | T-22 | T-19 | T-23 | T-20, T-21, T-25 | T-24 | T-18 |
| Static Hosting, domain and DNS (TB-1, DF-7) | T-26 | T-27, T-28 | No credible threat identified | T-29, T-30 | T-31 | No credible threat identified |
| Public Site (visitor browser) | No credible threat identified | T-32 | No credible threat identified | T-34 | No credible threat identified | T-33 |
| Instagram Feed Integration (TB-5, DF-8) | T-36 | T-38 | No credible threat identified | T-40 | T-39 | T-35, T-37 |
| Legacy import (DF-3) | No credible threat identified | T-41 | No credible threat identified | T-41 | No credible threat identified | No credible threat identified |
| Contact by email (FR-7.1) | No credible threat identified | No credible threat identified | No credible threat identified | T-42 | No credible threat identified | No credible threat identified |

Why no credible threat was identified:

- **Content Store, Spoofing:** the store has no sign-in of its own in the documents; impersonating an editor is T-1, writing as a non-listed party is T-12.
- **Static Hosting, Repudiation:** visitors perform no actions; unattributed operator changes are covered by T-23 and T-27.
- **Static Hosting, Elevation of Privilege:** no server-side code serves visitors (ARCHITECTURE.md); misuse of hosting accounts is T-27.
- **Public Site, Spoofing:** visitors have no identity; impersonating the site itself is T-26.
- **Public Site, Repudiation:** visitors are anonymous and change nothing.
- **Public Site, Denial of Service:** pages are static and do no processing a visitor can exhaust; flooding the host is T-31.
- **Instagram, Repudiation:** the integration is read-only and performs no user actions.
- **Legacy import, Spoofing / Repudiation / DoS / EoP:** a one-time import of known files; the result passes the same build checks (SEC-INPUT-3, SEC-INPUT-6) and change history (SEC-AUDIT-1) as editor content.
- **Contact by email, all but Information Disclosure:** the site publishes only a `mailto:` link; the mail system is outside this system; tampering with the page is T-27; spam volume is part of T-42.

### Threats

| Threat ID | STRIDE category | Element (component / boundary / flow) | Threat description | Existing mitigation (FR-* / SEC-* or NONE) | Gap and proposed change |
|---|---|---|---|---|---|
| T-1 | Spoofing | Editing Interface sign-in (TB-2, DF-1) | An external attacker obtains or guesses an editor's credentials (phishing, reused password, repeated guessing) and signs in as Nina; they can publish, deface, unpublish or delete any content. | SEC-AUTHN-1, SEC-AUTHZ-1 | Mechanism undecided; nothing limits guessing or forbids shared accounts. Added SEC-AUTHN-2; second factor stays open in SQ-1. |
| T-2 | Spoofing | Editing session (TB-2) | An attacker who obtains a live editing session (left signed in on a shared computer, token leaked in a URL or log, fixed session) continues as the editor without credentials. | SEC-AUTHN-1 (timeouts TO BE DECIDED) | No session rules. Added SEC-AUTHN-3; values in SQ-1. |
| T-3 | Tampering | Editing actions (TB-2, DF-1) | While Nina is signed in, another website makes her browser send an editing action (cross-site request forgery) or tricks her into clicking one inside a hidden frame (clickjacking), e.g. deleting a work. | NONE (SEC-HTTP-3 covers Static Hosting only) | Added SEC-AUTHN-3 (forgery protection) and SEC-HTTP-4 (no framing). |
| T-4 | Information Disclosure | Editor browser ↔ Editing Interface (TB-2) | A network attacker (e.g. public Wi-Fi) reads credentials, session tokens or unpublished content if the Editing Interface is reachable without HTTPS. | NONE (SEC-HTTP-1 covers Static Hosting only) | Added SEC-HTTP-4. |
| T-5 | Elevation of Privilege | Editing Interface views and previews | Script stored in content (by an editor, a stolen editor account, or imported legacy HTML) runs in the Editing Interface when another editor opens the item, with that editor's session; if the viewer is the owner, it can add editors (SEC-AUTHZ-2). | SEC-OUT-1 (Site Build output only) | Added SEC-OUT-4: the same encoding in the Editing Interface. |
| T-6 | Elevation of Privilege | Editor management (TB-2, TB-3) | An editor who is not the owner adds or removes editor accounts to keep access or lock Nina out. | SEC-AUTHZ-2 | No gap. |
| T-7 | Tampering | Uploads (DF-1, DF-2) | An editor account uploads a file that is not what it claims (an executable renamed `.jpg` or `.pdf`, an SVG with script, a PDF with active content), and the site distributes it to visitors. | SEC-INPUT-1, SEC-INPUT-2, SEC-HTTP-3 | PDFs are published as uploaded; residual risk accepted while every upload needs an editor account (T-1). No change. |
| T-8 | Denial of Service | Uploads → Site Build image processing (DF-1, DF-5) | An image within the file-size limit but with extreme pixel dimensions (decompression bomb) exhausts memory or time in image processing, so every publish fails until it is removed. | SEC-INPUT-1 (file size only), SEC-INPUT-3 (previous site stays online) | Added SEC-INPUT-5 (pixel limit, value in SQ-8). |
| T-9 | Repudiation | Content changes (DF-2, Content Store history) | A change or deletion (by an editor, or by an attacker using an editor account) cannot be attributed: history authorship may be self-declared, or the editing system may write every change under its own service identity. Nina cannot tell who changed what, or undo it. | SEC-AUTHZ-2 (editor-list changes only); change history (ARCHITECTURE.md Content Store) | Added SEC-AUDIT-1 and REQUIREMENTS.md FR-10.7 (Nina sees history and can restore). |
| T-10 | Denial of Service | Editing Interface availability | The editing system or its sign-in provider is down; Nina cannot edit or publish. | ARCHITECTURE.md DR-1 (the public site does not depend on it) | Accepted: the public site stays online. No change. |
| T-11 | Elevation of Privilege | Content Store write scope (TB-3 → TB-4) | If templates, build configuration or pipeline definitions sit where editors write (ASSUMPTION in ARCHITECTURE.md AQ-5: one repository), an editor account or the Editing Interface's own store credential can change build code and run it in the Site Build with the deploy and Instagram secrets. | NONE | Added SEC-AUTHZ-3 and ARCHITECTURE.md DR-8; the maintainer role is open in SQ-10. |
| T-12 | Tampering | Direct writes to the Content Store (TB-3) | A person or token not on the editor allow-list writes to the store directly, bypassing the Editing Interface's checks (FR-10.6). | SEC-AUTHZ-1, SEC-INPUT-3 | No gap. |
| T-13 | Information Disclosure | Content Store read access (TB-3) | If the store is readable by others (e.g. a public repository), anyone sees unpublished content, deleted items in history, image originals with location metadata, and editor identities. | SEC-AUTHN-1 (Editing Interface only); SEC-AUTHZ-1 covers writes only | Added SEC-AUTHZ-4 (no public read access). |
| T-14 | Information Disclosure | Content Store history and backups | Content Nina deletes (FR-10.1), e.g. an image whose rights were withdrawn or personal data entered by mistake, remains in history and backups and can be recovered by anyone with read access. | NONE | Needs a decision that weighs it against SEC-AUDIT-1: SQ-11. |
| T-15 | Repudiation | Content Store history | Someone with administrative rights on the store rewrites or deletes history, hiding tampering and destroying the record. | NONE | Added SEC-AUDIT-1 (history not rewritable by editors, the Editing Interface or the build identity). |
| T-16 | Denial of Service | Content Store as the single source of truth | The store is lost: account locked or deleted, provider failure, or mass deletion through a compromised editor account. All content and originals are gone. | Change history (protects against edits, not against losing the account) | Added SEC-AVAIL-1 (independent backup, tested restore); details in SQ-12. |
| T-17 | Denial of Service | Storage of large originals (Content Store) | ~4 MB originals exhaust a storage or transfer quota (ASSUMPTION: large-file storage, ARCHITECTURE.md AQ-5), blocking uploads and builds. | NONE | Depends on AQ-5; pointer added there. No rule until it is decided. |
| T-18 | Elevation of Privilege | Content Store → Site Build (TB-4, DF-5) | The generator evaluates template or shortcode syntax inside content fields, so text an editor enters runs at build time with access to the build environment and its secrets. | SEC-OUT-1 (encodes output, does not forbid evaluation) | Added SEC-INPUT-6. |
| T-19 | Tampering | Site Build toolchain (TB-4) | A compromised generator release, image tool, dependency or CI step injects script into every page or steals secrets. | SEC-CICD-2, DEP-1 to DEP-8 | Tools installed outside a lockfile were not covered: SEC-CICD-2 strengthened (build tools pinned, checksum or signature checked). |
| T-20 | Information Disclosure | Pipeline secrets (TB-4) | Builds of non-production branches (editing-system drafts, external pull requests) run with deploy or Instagram secrets, so unreviewed changes can read them. | SEC-SECRET-1, SEC-CICD-1 (only main deploys) | Added SEC-SECRET-2. |
| T-21 | Information Disclosure | Preview deployments (TB-4 → TB-1) | The hosting or CI publishes previews of drafts at public URLs, exposing unpublished content. | SEC-INPUT-4 (production output only) | Added SEC-CICD-3; whether previews exist depends on ARCHITECTURE.md AQ-4. |
| T-22 | Spoofing | Publish trigger (DF-4) | Anyone who learns the build trigger (e.g. a webhook URL) poses as the Editing Interface and starts builds repeatedly, using up build capacity and delaying real publishes. | SEC-CICD-1 (only main deploys, so content cannot be changed this way) | Added SEC-CICD-4 (authenticated triggers). |
| T-23 | Repudiation | Deploys (DF-6) | After a defacement or a bad publish, nobody can tell which Content Store version is live or what triggered the deploy, and the previous site cannot be restored quickly. | NONE | Added SEC-AUDIT-2. |
| T-24 | Denial of Service | Publishing (DF-4 to DF-6) | Publishing fails without anyone noticing (invalid content under SEC-INPUT-3, a failed scheduled build, an Instagram fetch error): Nina's changes never appear, and upcoming/current/past grouping (FR-4.1) goes stale. | SEC-INPUT-3 (previous site stays online) | Added REQUIREMENTS.md FR-10.8 (publish status shown to Nina) and the DF-9 status read in ARCHITECTURE.md; feed failures: T-39. |
| T-25 | Information Disclosure | Build output (DF-6) | Full-resolution originals or files with metadata end up in the static output (generators can copy page resources), exposing GPS and device data and unprotected originals; PDFs carry author, software or embedded-image metadata. | SEC-PRIV-4 (images), NFR-1 | SEC-PRIV-4 strengthened (PDFs; every file in the output checked). Whether originals may be published: REQUIREMENTS.md OQ-9; fields to keep: SQ-7. |
| T-26 | Spoofing | Domain and DNS of `nina-laaf.de` (TB-1) | An attacker controls the domain or a DNS record (registrar or DNS account takeover, lapsed registration, a record still pointing to a retired host) and serves a fake nina-laaf.de with a valid certificate, e.g. with a false contact address to approach collectors. | NONE (SEC-HTTP-1 does not help once DNS is controlled) | Added SEC-HOST-1; account holders: SQ-13. |
| T-27 | Tampering | Hosting account (TB-1) | Someone with a hosting login or a leaked deploy credential replaces served files directly, bypassing the pipeline (defacement, injected script). | SEC-CICD-1, SEC-SECRET-1 (deploy identity only) | Human hosting accounts were not covered: SEC-HOST-1; recovery: SEC-AUDIT-2. |
| T-28 | Tampering | Visitor browser ↔ Static Hosting (TB-1) | A network attacker alters pages in transit or downgrades to HTTP; another site frames the pages to mislead visitors. | SEC-HTTP-1, SEC-HTTP-3 | No gap. |
| T-29 | Information Disclosure | Static Hosting content (TB-1) | The deploy uploads more than the generated output (repository metadata such as `.git`, source content including unpublished items, configuration), all reachable by URL. | SEC-INPUT-4 (generated content only) | Added SEC-CICD-5. |
| T-30 | Information Disclosure | Hosting access logs | The host logs visitor IP addresses (personal data under GDPR) with unknown retention and access. | NONE | Open in SQ-6, now tied to T-30. |
| T-31 | Denial of Service | Static Hosting (TB-1) | A traffic flood makes the site unavailable or, with usage-based pricing, causes costs. | NONE | Depends on the provider (ARCHITECTURE.md AQ-4): SQ-14. |
| T-32 | Tampering | Client-side code in the Public Site (ARCHITECTURE.md AQ-2) | If client-side code is added (consent placeholder, language preference, feed rendering) and writes values from the URL, browser storage or the feed into the page as HTML, a crafted link runs script in the site's origin (DOM-based XSS). | SEC-HTTP-2 (CSP, partial) | Added SEC-OUT-5 (applies only if AQ-2 adds client-side code). |
| T-33 | Elevation of Privilege | Public Site origin ↔ Editing Interface (TB-1, TB-2) | If the Editing Interface is served from the public origin (e.g. a path under nina-laaf.de) and keeps editor credentials in the browser, any script that runs in the Public Site (T-32, T-35, a gap in SEC-OUT-1) acts as the editor. | SEC-OUT-1, SEC-HTTP-2 | Added ARCHITECTURE.md DR-9 (separate origin); AQ-3 must meet it. |
| T-34 | Information Disclosure | Public Site before consent | Editor content or a template references a third-party resource (an external image URL, an embed), sending visitors' IP addresses to third parties without consent. | SEC-PRIV-1, SEC-OUT-1, SEC-HTTP-2 | No gap. |
| T-35 | Elevation of Privilege | Instagram content in the browser (OQ-6 (a), TB-5) | After consent, script from Instagram/Meta runs in the site's own origin and can read or change any page, and the editor's session if T-33 applies. | SEC-PRIV-2 (only after consent), SEC-HTTP-2 (allowed origins) | Added SEC-OUT-6 (only if OQ-6 (a)); isolation method in SQ-4. |
| T-36 | Spoofing | @ninalaaf account → site (TB-5) | An attacker who takes over the Instagram account posts as Nina, and the posts appear on nina-laaf.de (live in (a), at the next build in (b)) without her review. | SEC-OUT-3 (encoding only) | Needs a decision: SQ-15. |
| T-37 | Elevation of Privilege | Instagram access token (OQ-6 (b)) | A token with more than read access, or a long-lived one, leaks and lets an attacker post as Nina or read account data. | SEC-SECRET-1 | Added SEC-SECRET-3 (read-only); lifetime and rotation: SQ-3, SQ-4. |
| T-38 | Tampering | Instagram → Site Build (OQ-6 (b), DF-8) | Fetched captions, links or media are malicious (markup in a caption, a crafted image aimed at the image processor, a non-HTTPS link) or altered in transit. | SEC-OUT-2, SEC-OUT-3, SEC-PRIV-3 | SEC-OUT-3 strengthened (verified HTTPS; media pass SEC-INPUT-1 and SEC-INPUT-5). |
| T-39 | Denial of Service | Instagram availability (TB-5) | Instagram is unavailable or the token has expired: in (a) the feed does not load; in (b) the whole build can fail and block all publishing. | FR-8.4 (the page still renders) | Build-time case not covered: added REQUIREMENTS.md FR-8.5. |
| T-40 | Information Disclosure | Instagram after consent (OQ-6 (a)) | Once the visitor consents, Meta receives the visitor's IP address and browser data. | SEC-PRIV-1, SEC-PRIV-2 | Accepted: consented, and to be described in the Datenschutzerklärung. No change. |
| T-41 | Tampering | Legacy import (DF-3) | Legacy text contains raw HTML (the legacy CV and contact pages in `docs/legacy-content/pages/` contain `<a>`, `<p>` and `<div>`), and legacy images keep EXIF data; the one-time import bypasses the Editing Interface. | SEC-OUT-1, SEC-INPUT-3, SEC-INPUT-4, SEC-PRIV-4, FR-11.2 | No gap. |
| T-42 | Information Disclosure | Contact by email (FR-7.1) | Nina's published email address is harvested for spam and phishing. | NONE | Accepted: published on purpose (FR-7.1). No change. |

## Prompt Placeholders To Resolve

- `{{CODE_QUALITY_PROMPT}}` — RESOLVED (no local prompt found): low cyclomatic and cognitive complexity; small cohesive functions and modules; separated presentation, business-rule, persistence and integration concerns; explicit error handling and trust-boundary transitions; no duplicated security-sensitive logic; testability without hidden global state.
- `{{API_SECURITY_PROMPT}}` — TO BE DECIDED: the public site has no API; the editing system's API and the Instagram access method are not yet chosen (AQ-3, OQ-6).
- `{{BACKEND_FRAMEWORK_PROMPT}}` — TO BE DECIDED: static site generator not chosen (AQ-1).
- `{{FRONTEND_FRAMEWORK_PROMPT}}` — TO BE DECIDED: no client framework chosen (AQ-2). `REF-XSS` serves as the rendering baseline meanwhile.
- `{{AUTH_PROMPT}}` — TO BE DECIDED: editor authentication mechanism not chosen (SQ-1); `REF-AUTH` and `REF-SESSION` remain provisional background.
- `{{DEPLOYMENT_PROMPT}}` — PARTIALLY RESOLVED: `REF-SSDF` and `REF-CICD` apply; provider-specific guidance TO BE DECIDED (AQ-4).

## Open Security Questions

- **SQ-1** How do editors authenticate to the Editing Interface (e.g. through the editing system's own login or an identity provider), is a second factor required, and what are the session timeouts (SEC-AUTHN-2, SEC-AUTHN-3; T-1, T-2)?
- **SQ-2** Who besides Nina may edit, and does Nina name another owner who may add or remove editors (SEC-AUTHZ-2)?
- **SQ-3** Where are deploy and Instagram secrets stored, and who can rotate them (T-37)?
- **SQ-4** For the Instagram feed (OQ-6): if loaded in the browser, how is consent stored and for how long, and how is Instagram content isolated from the site's origin (SEC-OUT-6, T-35); if copied at build time, which access method and token lifetime (T-37)?
- **SQ-5** If analytics are used (REQUIREMENTS.md OQ-8), which cookieless, self-hosted option?
- **SQ-6** What does the hosting provider log about visitors (IP addresses, retention), and how is that covered in the Datenschutzerklärung (T-30)?
- **SQ-7** Which image and PDF metadata (e.g. copyright, photographer, author) should be kept when location and device data are removed (SEC-PRIV-4, T-25)?
- **SQ-8** Upload size limits for images and PDFs (SEC-INPUT-1), and the pixel-dimension limit for images (SEC-INPUT-5, T-8)?
- **SQ-9** What is the security assurance target (e.g. a selected ASVS 5.0.0 level)?
- **SQ-10** Who maintains templates, build configuration, the pipeline and hosting settings (the maintainer role assumed in Required Security Inputs), and which access does that role hold (SEC-AUTHZ-3, SEC-AUTHZ-4; T-11, T-27)?
- **SQ-11** When content must be removed for legal reasons (withdrawn image rights, personal data entered by mistake), must it also be removed from the Content Store history and backups, and who may do that given SEC-AUDIT-1 (T-14)?
- **SQ-12** How often are backups made, where are they kept, how long are they retained, and who tests restoring (SEC-AVAIL-1, T-16)?
- **SQ-13** Who holds the registrar, DNS and hosting accounts for `nina-laaf.de` today, and who will (SEC-HOST-1, T-26; ARCHITECTURE.md AQ-4)?
- **SQ-14** Does the chosen hosting provider absorb traffic floods, and do usage-based costs need a spending limit or alert (T-31)?
- **SQ-15** Are Instagram posts shown on the site without Nina reviewing them, and how does Nina remove a post from the site quickly if her Instagram account is misused (T-36)?
