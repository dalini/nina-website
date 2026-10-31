# SECURITY

`SECURITY.md` owns the security posture of nina-laaf.de: threat model, security requirements, controls and trust-boundary enforcement. Behavior: `REQUIREMENTS.md`. Components and boundaries: `ARCHITECTURE.md`. Browser-facing design: `DESIGN.md`.

## Required Security Inputs

- Requirements source: REQUIREMENTS.md
- Design source: DESIGN.md
- Architecture source: ARCHITECTURE.md
- System purpose: See REQUIREMENTS.md
- Application profile: See ARCHITECTURE.md (Architecture): static public site built by Hugo with no server-side code serving visitors, plus a self-hosted Sveltia CMS editing interface with its GitHub OAuth helper (the only server-side code reachable from the internet) and a GitHub Actions build/publish pipeline; everything served by nginx on one rented VPS in Germany, which also runs the content check, the Instagram token refresh, the Content Store backup and the statistics.
- Users / actors / roles: Visitor (anonymous, read-only); Editor (Nina and Ives); Owner (Nina and Ives, SEC-AUTHZ-2); Maintainer (Ives: admin of `dalini/nina-site` and `dalini/nina-content`, CI, VPS, registrar and DNS); build/deploy identity; Instagram (external).
- Public interfaces and trust boundaries: TB-1 visitor browser ↔ Static Hosting (nginx on the VPS, including the domain and DNS of `nina-laaf.de`); TB-2 editor browser ↔ Editing Interface (`edit.nina-laaf.de`) and preview site (`preview.nina-laaf.de`); TB-3 Editing Interface ↔ Content Store (GitHub API, `dalini/nina-content`); TB-4 Content Store ↔ Site Build (GitHub Actions in `dalini/nina-site`) ↔ Static Hosting (rsync/SSH); TB-5 Instagram ↔ Site Build.
- Sensitive or regulated data: Editor GitHub credentials and OAuth tokens (the Editing Interface keeps the token in the editor's browser storage, T-45); preview Basic Auth credentials; deploy, preview-deploy and content-read secrets; the build-trigger token, the Instagram token and the GitHub OAuth client secret of the OAuth helper (on the VPS); Nina's email address (published on purpose); image and PDF metadata (EXIF, possibly location); unpublished content, including copies cached in editors' browsers (T-48), the publish status file (T-59) and the backup on the VPS (T-65); visitor IP addresses in nginx access logs (SEC-PRIV-6) and statistics reports (SEC-PRIV-5).
- External integrations: GitHub (Content Store, OAuth sign-in, Actions); Instagram (Instagram API with Instagram Login, build time); VPS provider in Germany.
- Authentication model: Editors sign in with individual GitHub accounts through GitHub OAuth, second factor required (SEC-AUTHN-1, SEC-AUTHN-2); preview site: per-editor HTTP Basic Auth over HTTPS. Visitors: none.
- Authorization model: Single role "editor" with full content rights in `dalini/nina-content`; owners add and remove editors; only the maintainer writes `dalini/nina-site`; anyone else has read-only access to published content (FR-10.5).
- Session model: Editing Interface sessions end after 30 minutes of inactivity and after at most 8 hours (SEC-AUTHN-3). The decided mechanism cannot enforce this yet (T-47, SQ-16).
- Deployment and CI/CD model: GitHub Actions in `dalini/nina-site` (started by the VPS content check through `workflow_dispatch`, plus a nightly schedule) build with Hugo and deploy only the output over rsync/SSH with restricted deploy keys (production and preview); secrets in GitHub Actions secrets of a `production` environment limited to `main` (SEC-SECRET-1, SEC-SECRET-2). Environments and branch protection for private repositories depend on the GitHub plan (T-52, SQ-18).
- Applicable privacy or regulatory obligations: Operator in Germany: GDPR; Impressum and Datenschutzerklärung required (REQUIREMENTS.md FR-9.1). Wording: drafted with a German legal-text generator (e.g. eRecht24), completed and approved by Nina before release; it must describe the access logs (SEC-PRIV-6) and the log statistics (SEC-PRIV-5). No GDPR article mapping is kept in this document or the issues.
- Security assurance target: OWASP ASVS 5.0.0 Level 1 for the whole system. NIST SP 800-53 and Zero Trust mappings are not applicable to this project.
- Security verification reference: OWASP ASVS 5.0.0
- Threat model status: STRIDE threat model v2, 2026-10-31, of the documented system before implementation (Threat Model below: T-1 to T-68). v1 (T-1 to T-42) modeled the system after step 7; step 10 (2026-10-31) decided SQ-1 to SQ-15, OQ-6, AQ-2, AQ-3, AQ-4 and AQ-5, and the rows point to those decisions. v2 re-ran step 8 against the decided architecture, as step 10 required: T-43 to T-68 are new, and the rules they changed name them. Conditional threats of the browser-loaded Instagram model (T-35, T-40) no longer apply. Open: SQ-16 to SQ-21; resolve them (step 10) before the first release.

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
- `REF-SVELTIA-AUTH` Sveltia CMS Authenticator (GitHub OAuth helper; default scope `repo,user`, allowed-domains setting, `state` check) — https://github.com/sveltia/sveltia-cms-auth
- `REF-GH-PLANS` GitHub Docs on plan availability for private repositories: protected branches — https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches ; environments, environment secrets and deployment branches — https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments
- `REF-HUGO-ADV` Hugo symlink confinement advisories GHSA-fw87-fv5r-9fpw (fixed in v0.162.0) and GHSA-vrv5-r5rf-6v4j / CVE-2026-89258 (fixed in v0.165.0) — https://github.com/gohugoio/hugo/security/advisories/GHSA-fw87-fv5r-9fpw , https://osv.dev/vulnerability/CVE-2026-89258

## Security Rules

### Public site and HTTP boundary (TB-1)

- **SEC-HTTP-1** Static Hosting MUST serve every page and asset of `nina-laaf.de` over HTTPS only, MUST redirect HTTP to HTTPS, and MUST send the header `Strict-Transport-Security: max-age=31536000; includeSubDomains` (no preload).
  - **Applies to:** Static Hosting, all public responses
  - **Verification:** Automated check that HTTP requests redirect and HTTPS responses send HSTS
  - **References:** `REF-ASVS-5`
- **SEC-HTTP-2** Public responses MUST send the Content Security Policy `default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; font-src 'self'; base-uri 'self'; form-action 'none'; frame-ancestors 'none'`. No third-party origin is allowed (Instagram media is self-hosted, SEC-PRIV-3).
  - **Applies to:** Static Hosting, Public Site
  - **Verification:** Header check in CI; browser test that no third-party request occurs on page load
  - **References:** `REF-XSS`, `REF-ASVS-5`
- **SEC-HTTP-3** Public responses MUST send `X-Content-Type-Options: nosniff` and MUST forbid framing of the site by other origins.
  - **Applies to:** Static Hosting
  - **Verification:** Header check in CI
  - **References:** `REF-ASVS-5`
- **SEC-HTTP-4** The Editing Interface (`edit.nina-laaf.de`) and the preview site (`preview.nina-laaf.de`) MUST be served over HTTPS only, MUST redirect HTTP to HTTPS and send an HSTS header, MUST send `X-Content-Type-Options: nosniff`, and MUST forbid framing by other origins. The preview site MUST send the Content Security Policy of SEC-HTTP-2. The Editing Interface MUST send a Content Security Policy that allows script only from its own origin (no inline or third-party script), allows connections only to its own origin and the GitHub endpoints the CMS uses, and sets `frame-ancestors 'none'`; the full directive list is TO BE DECIDED at implementation, naming each allowed origin.
  - **Applies to:** Editing Interface, preview site (TB-2)
  - **Verification:** The SEC-HTTP-1 and SEC-HTTP-3 header checks, run against both hosts; CSP header check on both hosts; browser test that the Editing Interface loads no script from another origin
  - **References:** `REF-ASVS-5`, `REF-XSS`
  - **Threats:** T-3, T-4, T-45 (strengthened: CSP for the Editing Interface), T-66 (strengthened: CSP for the preview site)
- **SEC-HTTP-5** The TLS certificates of `nina-laaf.de`, `edit.nina-laaf.de` and `preview.nina-laaf.de` MUST be renewed automatically before they expire, and a failed renewal MUST alert Ives. Certificate source and alert mechanism: TO BE DECIDED.
  - **Applies to:** Static Hosting (TB-1, TB-2)
  - **Verification:** Automated check of each host's certificate expiry date; review of the renewal job and its alert
  - **References:** `REF-ASVS-5`
  - **Threats:** T-64

### Domain, DNS and hosting accounts (TB-1)

- **SEC-HOST-1** The registrar, DNS and VPS accounts for `nina-laaf.de` MUST be held by named people (today and in future: Ives) with individual logins and a second factor wherever the provider offers one; renewal notices MUST go to a monitored address; a DNS record MUST be removed when the target it points to is retired.
  - **Applies to:** Domain and DNS of `nina-laaf.de`, Static Hosting
  - **Verification:** Yearly manual review by Ives of account holders, second factors, renewal date and DNS records against live targets
  - **References:** `REF-ASVS-5`, `REF-SSDF`
  - **Threats:** T-26, T-27
- **SEC-HOST-2** The VPS MUST receive operating-system and package security updates (schedule TO BE DECIDED), MUST allow administrative access only over SSH with keys (no password sign-in, no direct root sign-in), and MUST expose only HTTP, HTTPS and SSH to the internet. Every process that handles input from outside the VPS (nginx workers, the OAuth helper, the statistics tool, the content check, the Instagram token refresh, the deploy account's rsync) MUST run under an unprivileged account, never as root. How such a process reads a secret that SEC-SECRET-1 keeps in a root-only file: SQ-20.
  - **Applies to:** Static Hosting (VPS)
  - **Verification:** External port scan shows only 22, 80 and 443; SSH password and root sign-in are rejected; the process list shows none of the listed processes running as root; the update log is checked in the yearly SEC-HOST-1 review
  - **References:** `REF-ASVS-5`, `REF-SSDF`
  - **Threats:** T-61, T-62

### Third-party content and privacy

- **SEC-PRIV-1** The Public Site MUST NOT request any third-party resource (fonts, scripts, images, embeds such as Instagram/Meta, video or maps) in the visitor's browser before the visitor gives consent; fonts and all site assets MUST be self-hosted.
  - **Applies to:** Public Site, Site Build templates
  - **Verification:** Automated browser test recording all network requests on every page type before consent
  - **References:** `REF-PC-2024`
- **SEC-PRIV-2** WITHDRAWN in step 10: it applied only to a browser-loaded Instagram feed (model (a)); the build-time model (b) was chosen, so visitors never contact Instagram (SEC-PRIV-3).
- **SEC-PRIV-3** The Site Build MUST copy Instagram post images into the static output so that visitors never request them from Instagram.
  - **Applies to:** Instagram Feed Integration, Site Build
  - **Verification:** Build output contains no Instagram/Meta URLs for media; browser test as SEC-PRIV-1
  - **References:** `REF-PC-2024`
- **SEC-PRIV-4** The Site Build or upload process MUST remove EXIF and other embedded metadata (including location) from published images, and MUST check published PDFs for embedded metadata (author, software, location in embedded images) and clean or reject them, except the copyright notice and the creator (photographer or author), which are kept.
  - **Applies to:** Editing Interface uploads, Site Build image processing
  - **Verification:** Automated check that no image or PDF file anywhere in the build output contains GPS or device metadata
  - **References:** `REF-PC-2024`
  - **Threats:** T-25 (strengthened: PDFs, whole build output)
- **SEC-PRIV-5** The site MUST NOT include analytics or tracking scripts, cookies or pixels in pages. Visitor statistics MUST be computed only from the nginx access logs on the VPS (SEC-PRIV-6) with a self-hosted, cookieless tool (e.g. GoAccess), and MUST be described in the Datenschutzerklärung. Statistics reports MUST be readable only by the maintainer, MUST NOT be served from `nina-laaf.de`, `edit.nina-laaf.de` or `preview.nina-laaf.de` (how Ives views them: TO BE DECIDED), and MUST NOT keep full IP addresses longer than the logs (14 days, SEC-PRIV-6). The statistics tool parses visitor-controlled log fields, so it MUST be kept at a version without known vulnerabilities (DEP-4, DEP-5) and run as in SEC-HOST-2.
  - **Applies to:** Public Site, Static Hosting
  - **Verification:** Network test (SEC-PRIV-1); no analytics script in the build output; dependency review of the statistics tool; report paths return not found on all three hosts; report file permissions and retention reviewed
  - **References:** `REF-PC-2024`
  - **Threats:** T-63 (strengthened: reports, retention, tool updates)
- **SEC-PRIV-6** nginx access logs MAY contain full visitor IP addresses for abuse handling; they MUST be deleted after 14 days, MUST be readable only by the maintainer, and MUST be described in the Datenschutzerklärung.
  - **Applies to:** Static Hosting
  - **Verification:** Review of nginx log configuration and log rotation (14 days); file permissions; Datenschutzerklärung text
  - **References:** `REF-PC-2024`
  - **Threats:** T-30

### Output encoding and safe rendering

- **SEC-OUT-1** The Site Build MUST HTML-encode all editor-provided text by default. Rich text is allowed only in Text bodies, Page content, News text, a work's short text and the CV bio, and MUST be rendered from a restricted format: paragraphs, headings h2–h3, bold, italic, links, lists, block quotes, and (in Text bodies only) images from the Content Store; every other field is plain text. Raw HTML, scripts, inline event handlers, iframes and `javascript:` URLs in content MUST be removed or rejected.
  - **Applies to:** Site Build templates, content model
  - **Verification:** Build tests with content containing `<script>`, event-handler attributes and `javascript:` links; output must contain none of them
  - **References:** `REF-XSS`
- **SEC-OUT-2** External links from content (exhibition links, gallery, Instagram) MUST use only `https:` or `mailto:` schemes and MUST open without giving the target page access to the site (`rel="noopener"`).
  - **Applies to:** Content model, Site Build
  - **Verification:** Build test rejecting other schemes
  - **References:** `REF-XSS`, `REF-INPUT`
- **SEC-OUT-3** Content imported from Instagram (captions, links) MUST be treated as untrusted text and encoded like editor content. The Site Build MUST fetch it over HTTPS with certificate validation, and fetched media MUST pass the type checks of SEC-INPUT-1 and the limits of SEC-INPUT-5. A fetched post that fails one of these checks MUST be left out like a hidden post (REQUIREMENTS.md FR-8.5) and MUST NOT fail the build.
  - **Applies to:** Instagram Feed Integration, Site Build
  - **Verification:** Build test with a caption containing markup; build test with a fetched file that is not an image or exceeds the SEC-INPUT-5 limit: the build publishes, the post is left out, nothing of it is in the output
  - **References:** `REF-XSS`
  - **Threats:** T-38 (strengthened: transport and media checks), T-68 (strengthened: a failing post does not fail the build)
- **SEC-OUT-4** The Editing Interface MUST display content (lists, forms, previews) with the same encoding and restricted rich-text rules as SEC-OUT-1. It MUST show the publish status file (DF-9) as text only.
  - **Applies to:** Editing Interface
  - **Verification:** Open content containing `<script>`, event-handler attributes and `javascript:` links in every Editing Interface view: no script runs; a status file containing markup is shown as text
  - **References:** `REF-XSS`
  - **Threats:** T-5, T-60 (strengthened: publish status file)
- **SEC-OUT-5** The Public Site's first-party client-side code (e.g. the image viewer) MUST insert values from the URL, browser storage or page data into the page only as text, never as HTML.
  - **Applies to:** Public Site client-side code
  - **Verification:** Browser test with markup in the query string, fragment and stored values: rendered as text, no script runs
  - **References:** `REF-XSS`
  - **Threats:** T-32
- **SEC-OUT-6** WITHDRAWN in step 10: it applied only to a browser-loaded Instagram feed (model (a)); with the build-time model no Instagram/Meta script reaches visitors (SEC-HTTP-2 allows only `'self'`).

### Editing access (TB-2, TB-3)

- **SEC-AUTHN-1** The Editing Interface MUST require authentication for every action that reads unpublished content or changes content. Mechanism: GitHub OAuth sign-in through the OAuth helper self-hosted on `edit.nina-laaf.de`; the preview site requires per-editor HTTP Basic Auth over HTTPS.
  - **Applies to:** Editing Interface, preview site
  - **Verification:** Automated test: unauthenticated requests to every editing action and to the preview site are rejected
  - **References:** `REF-AUTH`, `REF-SESSION`
- **SEC-AUTHN-2** Every editor MUST sign in with an individual account (no shared accounts), and the sign-in (the editing system's own or an identity provider's) MUST resist credential guessing by limiting or slowing repeated failed attempts (GitHub's sign-in for the CMS; nginx limits for the preview site). Every editor's GitHub account MUST have a second factor enabled. Preview passwords MUST be unique per editor, MUST NOT be the editor's GitHub password, and MUST be stored only as salted hashes made with the strongest `crypt()` method the VPS supports (no plain, SHA-1 or MD5-based entries).
  - **Applies to:** Editing Interface, preview site (TB-2)
  - **Verification:** Review: one GitHub account and one preview credential per listed editor, each with 2FA on GitHub; automated test: repeated failed preview sign-ins are limited; review of the hash method of every entry in the preview password file
  - **References:** `REF-AUTH`
  - **Threats:** T-1, T-67 (strengthened: preview password storage)
- **SEC-AUTHN-3** Editing sessions MUST get a new session identifier at sign-in, end on sign-out, expire after 30 minutes of inactivity and after at most 8 hours, and MUST NOT appear in URLs or logs. The implementation verifies what Sveltia CMS and GitHub OAuth tokens can enforce; a gap is raised as an open question, not dropped. State-changing editing actions MUST be protected against cross-site request forgery. Sign-out MUST also remove the content the Editing Interface cached on the device.
  - **Gap found by the step 8 re-run (T-47, T-48):** with Sveltia CMS and a GitHub OAuth App token, sign-out only deletes the token in that browser, the token does not expire, the CMS has no inactivity timeout, and cached content stays on the device after sign-out. Open question: SQ-16. Until it is decided, editors MUST NOT sign in to the Editing Interface on devices that other people use, and SEC-AUTHN-5 applies.
  - **Applies to:** Editing Interface (TB-2)
  - **Verification:** Automated tests: session invalid after sign-out and timeout; a forged cross-site request is rejected; URLs and logs contain no session values; after sign-out the browser holds no cached content
  - **References:** `REF-SESSION`
  - **Threats:** T-2, T-3, T-47, T-48 (strengthened: cached content)
- **SEC-AUTHN-4** The OAuth helper on `edit.nina-laaf.de` MUST release a token only to pages served from `https://edit.nina-laaf.de` (its allowed-domains setting set to exactly that host), MUST verify the OAuth `state` value of every sign-in, MUST request only the scopes SEC-AUTHZ-3 allows, and MUST never request the `workflow` scope. Its GitHub OAuth client secret is a secret under SEC-SECRET-1.
  - **Applies to:** Editing Interface OAuth helper (TB-2, TB-3)
  - **Verification:** A sign-in started from another origin receives no token; a callback with a wrong or missing `state` is rejected; the scopes shown on GitHub's authorization screen are reviewed
  - **References:** `REF-AUTH`, `REF-SVELTIA-AUTH`
  - **Threats:** T-43
- **SEC-AUTHN-5** Ives MUST be able to revoke all Editing Interface tokens of an editor at once (by revoking that editor's authorization of the OAuth app, or all tokens of the app), and MUST do so when an editor reports a lost device, when an editor is removed (SEC-AUTHZ-2), and after a suspected compromise.
  - **Applies to:** Editing Interface, Content Store (TB-3)
  - **Verification:** Revocation drill: after revocation, the Editing Interface on the old device can no longer read or write the Content Store
  - **References:** `REF-SESSION`, `REF-AUTH`
  - **Threats:** T-44, T-47
- **SEC-AUTHZ-1** The Content Store MUST accept changes only from authenticated editors on its allow-list (the GitHub collaborators of `dalini/nina-content`: Nina and Ives) and MUST deny everything else by default, leaving the site unchanged.
  - **Applies to:** Content Store, Editing Interface
  - **Verification:** Test with a non-listed account: change is rejected and the site is unchanged
  - **References:** `REF-ASVS-5`, `REF-PC-2024`
- **SEC-AUTHZ-2** Adding or removing editors MUST be decided only by the owners (Nina and Ives) and carried out only by Ives, the sole GitHub admin of `dalini/nina-content`. Every change MUST also be committed as a line in `EDITORS.md` in the Content Store.
  - **Applies to:** Content Store
  - **Verification:** Manual review: collaborator list matches `EDITORS.md`; Ives is the only admin; history shows each change
  - **References:** `REF-ASVS-5`
- **SEC-AUTHZ-3** Editors' write access, and the credential the Editing Interface uses to write to the Content Store, MUST be limited to content and media (plus adding and removing editors under SEC-AUTHZ-2). They MUST NOT be able to change templates, build configuration or pipeline definitions: those live in `dalini/nina-site`, where only the maintainer has write access (ARCHITECTURE.md DR-8). Accepted risk: Ives is both editor and maintainer, so his CMS OAuth token can also write `dalini/nina-site`; this is accepted for his account, which requires 2FA.
  - **Finding of the step 8 re-run (T-44):** the authenticator's default GitHub OAuth App scope `repo,user` reaches every repository the signed-in editor can access, not only `dalini/nina-content`, and for Ives also his admin rights on `dalini/nina-content` and all his other repositories. That is wider than the accepted risk above, which covers only `dalini/nina-site`. The Editing Interface credential MUST be narrowed to the smallest scope the editing system supports, or the wider reach MUST be accepted explicitly: SQ-17.
  - **Applies to:** Content Store, Editing Interface, Site Build (TB-3, TB-4)
  - **Verification:** Test with Nina's editor account and the Editing Interface credential: a push to `dalini/nina-site` is rejected; review that only Ives has write access there; review of the scopes or permissions granted to the Editing Interface credential
  - **References:** `REF-CICD`, `REF-ASVS-5`, `REF-SVELTIA-AUTH`
  - **Threats:** T-11, T-44
- **SEC-AUTHZ-4** The Content Store, including its history and media originals, MUST NOT be publicly readable; read access is limited to editors, the build identity and the VPS content check and backup (read-only credentials), and the maintainer (Ives).
  - **Applies to:** Content Store (TB-3)
  - **Verification:** Unauthenticated read request is rejected; review of the access list
  - **References:** `REF-ASVS-5`
  - **Threats:** T-13

### Change history and recovery

- **SEC-AUDIT-1** Every change in the Content Store MUST record which authenticated editor made it, taken from the Editing Interface sign-in rather than from self-declared author fields, and when. Editors, the Editing Interface and the build identity MUST NOT be able to rewrite or delete history (branch protection on `main` of `dalini/nina-content`). Exception: on a documented legal request (withdrawn image rights, personal data entered by mistake), only Ives may purge the content from history and backups, and MUST keep a record of the purge (date, reason, no content). `main` of `dalini/nina-content` MUST accept only commits that GitHub marks as verified, so that the author of a direct push cannot be self-declared (ASSUMPTION: commits the Editing Interface makes through GitHub's API are marked verified; checked at implementation). Branch protection and required verified commits on a private repository depend on the GitHub plan: SQ-18.
  - **Applies to:** Content Store, Editing Interface
  - **Verification:** Changes by two editors show each identity in the history; an attempt to rewrite history with editor, Editing Interface or build credentials is rejected; a direct push of an unsigned commit naming another editor is rejected
  - **References:** `REF-ASVS-5`, `REF-GH-PLANS`
  - **Threats:** T-9, T-15, T-52, T-53 (strengthened: verified commits)
- **SEC-AUDIT-2** Every production deploy MUST record the Content Store version it was built from and what triggered it, and the previously deployed version MUST be restorable without a new content change.
  - **Applies to:** Site Build, Static Hosting (TB-4)
  - **Verification:** Review of deploy records; restore drill to the previous version
  - **References:** `REF-SSDF`
  - **Threats:** T-23, T-27
- **SEC-AVAIL-1** Content and media originals MUST be backed up to a location that does not depend on the Content Store's account or provider, and restoring from the backup MUST be tested. A mirror of `dalini/nina-content` (history and originals) MUST be made weekly to the VPS and kept for 30 days; Ives tests a restore once a year. The backup MUST be stored outside every directory nginx serves and MUST be readable only by the maintainer, not by the web server, deploy, OAuth-helper or statistics accounts. Residual risk: the backup is independent of GitHub, but not of the VPS that also runs Static Hosting.
  - **Applies to:** Content Store, Static Hosting (VPS)
  - **Verification:** Yearly restore test into an empty store; the Site Build produces the same site from it; file permissions and nginx roots reviewed against the backup path
  - **References:** `REF-SSDF`
  - **Threats:** T-16, T-65 (strengthened: location and access on the VPS)

### Uploads and content validation

- **SEC-INPUT-1** The Editing Interface MUST accept only image files and PDFs for upload, check the actual file type (not only the extension), and reject images above 10 MB and PDFs above 20 MB. Because these checks run in the editor's browser and a direct write to the Content Store bypasses them, the Site Build MUST recheck the type and size of every file it publishes; a failed check fails the publish under SEC-INPUT-3.
  - **Applies to:** Editing Interface, Content Store, Site Build
  - **Verification:** Upload tests with a renamed executable, an SVG with script, and an oversized file; the same files committed directly to the Content Store: the publish fails
  - **References:** `REF-INPUT`
  - **Threats:** T-7, T-49 (strengthened: recheck in the Site Build)
- **SEC-INPUT-2** SVG uploads MUST be rejected unless sanitized; this applies to editor uploads, not to the site's own `logo.svg`. The Site Build MUST apply the same rule to every SVG from the Content Store it would publish.
  - **Applies to:** Editing Interface, Site Build
  - **Verification:** Upload test with an SVG containing script; the same SVG committed directly to the Content Store: the publish fails
  - **References:** `REF-INPUT`, `REF-XSS`
  - **Threats:** T-7, T-49 (strengthened: recheck in the Site Build)
- **SEC-INPUT-3** The Site Build MUST fail and publish nothing when content violates the content model (FR-10.6, ARCHITECTURE.md DR-3), leaving the previous site online.
  - **Applies to:** Site Build, Static Hosting
  - **Verification:** Build with an invalid work entry: build fails; live site unchanged
  - **References:** `REF-INPUT`
- **SEC-INPUT-4** Unpublished content MUST NOT appear in the production output (FR-11.2); only the preview build includes it (SEC-CICD-3).
  - **Applies to:** Site Build
  - **Verification:** Production build test with an unpublished work: no page, no image, no listing entry
  - **References:** `REF-ASVS-5`
- **SEC-INPUT-5** Image processing (at upload and in the Site Build, including Instagram media) MUST reject images larger than 40 megapixels before decoding them fully.
  - **Applies to:** Editing Interface uploads, Site Build image processing
  - **Verification:** Upload and build tests with a small file declaring extreme pixel dimensions: upload rejected; build fails under SEC-INPUT-3 without exhausting memory or time
  - **References:** `REF-INPUT`
  - **Threats:** T-8, T-38
- **SEC-INPUT-6** The Site Build MUST treat content fields as data: template, shortcode or other generator syntax inside content MUST NOT be evaluated.
  - **Applies to:** Site Build (TB-4)
  - **Verification:** Build test with generator template and shortcode syntax in a text field: the output shows it as encoded text
  - **References:** `REF-INPUT`
  - **Threats:** T-18
- **SEC-INPUT-7** The Site Build MUST fail and publish nothing when the Content Store checkout contains a symbolic link or any other entry that is not a regular file or directory.
  - **Applies to:** Site Build (TB-4)
  - **Verification:** Build test with a symbolic link in the content pointing outside the checkout: the build fails and the output contains nothing from the link target
  - **References:** `REF-INPUT`, `REF-HUGO-ADV`
  - **Threats:** T-50

### Secrets and pipeline (TB-4)

- **SEC-SECRET-1** Deploy credentials, editing-system credentials and any Instagram access token MUST be kept out of the repository, generated output, client code and logs, and stored as follows: deploy, preview-deploy and content-read secrets in GitHub Actions secrets of the `production` environment of `dalini/nina-site`; the build-trigger token (SEC-CICD-4), the Instagram token, the content-read credentials of the content check and the backup, and the GitHub OAuth client secret of the OAuth helper (SEC-AUTHN-4) in root-only files on the VPS. How processes that do not run as root (SEC-HOST-2) read these files: SQ-20. Ives rotates them at least yearly and after any suspected leak.
  - **Applies to:** Site Build, Static Hosting, Instagram Feed Integration, Editing Interface OAuth helper
  - **Verification:** Secret scanning on every change; inspection of build output; rotation dates reviewed yearly
  - **References:** `REF-SECRETS`
  - **Threats:** T-43, T-62 (strengthened: OAuth client secret and VPS credentials listed)
- **SEC-SECRET-2** The production deploy key and the Instagram token MUST be available only to the production build of `main`, not to builds of other branches or external contributions. The preview build MUST use its own preview deploy key, which can write only the preview site. Limiting secrets to `main` needs environments with deployment branches, which GitHub offers for private repositories only on paid plans: SQ-18.
  - **Applies to:** Site Build pipeline (TB-4)
  - **Verification:** CI configuration review; a test build of another branch cannot read the secrets
  - **References:** `REF-CICD`, `REF-SECRETS`, `REF-GH-PLANS`
  - **Threats:** T-20, T-52
- **SEC-SECRET-3** Instagram access MUST use the Instagram API with Instagram Login with scope `instagram_business_basic` only, and MUST NOT allow posting or account changes. The 60-day token MUST be refreshed monthly by a cron job on the VPS; a failed refresh MUST alert Ives.
  - **Applies to:** Instagram Feed Integration (TB-5), Static Hosting
  - **Verification:** Review of the granted scope; cron and alert configuration review
  - **References:** `REF-SECRETS`
  - **Threats:** T-37
- **SEC-CICD-1** The build/deploy identity MUST have only the permissions needed to publish the site: read-only access to `dalini/nina-content`, and an SSH deploy key restricted to writing the web root (plus reading the Instagram token and the deployed feed files, ARCHITECTURE.md). Only `main` of the Content Store MAY deploy to production. The deploy account on the VPS MUST NOT have root rights and MUST be limited to the paths listed here and the publish status file (DF-9). The workflow's own `GITHUB_TOKEN` MUST be limited to read access. Every connection from the Site Build to the VPS MUST verify the VPS's SSH host key against a pinned value.
  - **Applies to:** Site Build, Static Hosting
  - **Verification:** Review of CI permissions and branch protection; the deploy account cannot read or write outside the listed paths; a connection to a host with another key fails
  - **References:** `REF-CICD`, `REF-SSDF`
  - **Threats:** T-57, T-62 (strengthened: non-root deploy account, read-only workflow token, pinned host key)
- **SEC-CICD-2** Third-party CI actions/steps, and build tools installed outside the lockfile (generator, image processing), MUST be pinned to exact versions (commit SHA where the platform supports it); downloaded tools MUST be verified against a published checksum or signature. The same applies to the Editing Interface: the Sveltia CMS script bundle and the OAuth helper code MUST be served from `edit.nina-laaf.de` (never loaded from a CDN when the page opens), pinned to an exact release, and verified against a published checksum or the lockfile. Because Sveltia CMS supports only its latest version, Ives updates both promptly after a security release (DEP-3 to DEP-5).
  - **Applies to:** Site Build pipeline, Editing Interface
  - **Verification:** CI configuration review; the Editing Interface page loads its script only from its own origin (SEC-HTTP-4); the deployed bundle matches the pinned release
  - **References:** `REF-CICD`, `REF-SUPPLY`
  - **Threats:** T-19 (strengthened: build tools, integrity check), T-46 (strengthened: Editing Interface code)
- **SEC-CICD-3** Previews of unpublished content exist only at `preview.nina-laaf.de` and MUST be reachable only by authenticated editors (per-editor HTTP Basic Auth, SEC-AUTHN-1).
  - **Applies to:** Site Build pipeline, Static Hosting
  - **Verification:** Request a preview URL without signing in: denied
  - **References:** `REF-CICD`
  - **Threats:** T-21
- **SEC-CICD-4** Builds MUST start only through `workflow_dispatch` called by the content check on the VPS, or the nightly schedule. The content check MUST use a fine-grained token limited to Actions: write on `dalini/nina-site`, kept in a root-only file on the VPS (SEC-SECRET-1); no other build trigger (webhook, `repository_dispatch`, push from `dalini/nina-content`) exists, and nothing editors can write holds a trigger secret. The build workflow MUST NOT take `workflow_dispatch` inputs, or MUST accept only a validated commit ID and use it only as data, never inside a shell command. The content check MUST alert Ives when it cannot read the Content Store or cannot start a build (alert mechanism as for the token refresh, SEC-SECRET-3).
  - **Applies to:** Site Build pipeline, Static Hosting (VPS)
  - **Verification:** CI configuration review of the workflow triggers and inputs; token scope review; a dispatch attempt without the token is rejected; a dispatch with a crafted input runs no command; a content check with a revoked credential alerts Ives
  - **References:** `REF-CICD`
  - **Threats:** T-22, T-55 (strengthened: dispatch inputs), T-58 (strengthened: alert)
- **SEC-CICD-5** Only the Site Build's generated output MUST be deployed to Static Hosting; repository metadata, source content and configuration MUST NOT be reachable on the site.
  - **Applies to:** Site Build pipeline, Static Hosting
  - **Verification:** Post-deploy check that `/.git/`, source-content and configuration paths return not found
  - **References:** `REF-CICD`
  - **Threats:** T-29
- **SEC-CICD-6** Steps that read or process Content Store files (checkout, Hugo, image and PDF processing, content checks) MUST run without access to the production and preview deploy keys and the Instagram token. Those secrets are available only to the step that downloads Instagram posts and to the step that deploys the finished output (ARCHITECTURE.md Site Build).
  - **Applies to:** Site Build pipeline (TB-4)
  - **Verification:** CI configuration review; a build test in which a content-processing step lists its environment and readable files finds no secret
  - **References:** `REF-CICD`, `REF-SECRETS`
  - **Threats:** T-54, T-18, T-50
- **SEC-CICD-7** GitHub Actions MUST be disabled for `dalini/nina-content`, and that repository MUST hold no Actions secrets, so that workflow files committed there never run.
  - **Applies to:** Content Store (TB-3)
  - **Verification:** Repository settings review; a test workflow committed to a branch does not run
  - **References:** `REF-CICD`
  - **Threats:** T-51
- **SEC-CICD-8** Every build job MUST have a time limit (value TO BE DECIDED, well above a normal build). The account's Actions spending limit MUST stay at zero (no paid overage), and GitHub's included-usage alerts for Actions minutes MUST be on and reach Ives. An unexplained build run or usage alert is treated as a suspected leak of the build-trigger token (rotation, SEC-SECRET-1).
  - **Applies to:** Site Build pipeline, GitHub account settings
  - **Verification:** CI configuration review; billing settings review in the yearly SEC-HOST-1 review
  - **References:** `REF-CICD`
  - **Threats:** T-56
- **SEC-CICD-9** The publish status file (DF-9) MUST contain only the content version, the result and, for a failure, the failing item's identifier and field name. It MUST NOT contain content values, build log output or secrets. Whether it must be readable only by editors: SQ-19.
  - **Applies to:** Site Build, Editing Interface (DF-9)
  - **Verification:** Build test with a failing item whose fields contain marker text: the status file contains none of it
  - **References:** `REF-SECRETS`
  - **Threats:** T-59

## Requirement and Architecture Traceability

Status after the step 8 re-run (threat model v2): `DEFINED` = decided and specified, not yet implemented or verified; `WITHDRAWN` = no longer applies; "open: SQ-n" = the rule stands, and the named question decides how it is met.

| Rule | Requirements | Component / boundary | Status |
|---|---|---|---|
| SEC-HTTP-1, -3 | NFR-4 | Static Hosting (TB-1) | DEFINED |
| SEC-HTTP-2 | NFR-3, FR-8.3 | Static Hosting, Public Site | DEFINED |
| SEC-HTTP-4 | FR-10.5, FR-10.9 | Editing Interface, preview site (TB-2) | DEFINED (CSP directive list at implementation) |
| SEC-HTTP-5 | FR-10.1, NFR-4 | Static Hosting (TB-1, TB-2) | DEFINED |
| SEC-HOST-1 | FR-7.1, NFR-4 | Domain and DNS, Static Hosting (TB-1) | DEFINED |
| SEC-HOST-2 | FR-10.4, FR-10.5 | Static Hosting (VPS) | DEFINED (open: SQ-20) |
| SEC-PRIV-1 | NFR-3 | Public Site, Site Build | DEFINED |
| SEC-PRIV-2 | — | — | WITHDRAWN (build-time feed) |
| SEC-PRIV-3 | FR-8.2–8.5 | Instagram Feed Integration, Site Build (TB-5) | DEFINED |
| SEC-PRIV-4 | FR-3.10, FR-10.2 | Editing Interface, Site Build | DEFINED |
| SEC-PRIV-5 | NFR-3 | Public Site, Static Hosting | DEFINED |
| SEC-PRIV-6 | FR-9.1 | Static Hosting (TB-1) | DEFINED |
| SEC-OUT-1, -2 | FR-1.4, FR-4.3, FR-4.6, FR-5.1, FR-10.1 | Site Build | DEFINED |
| SEC-OUT-3 | FR-8.2 | Instagram Feed Integration, Site Build | DEFINED |
| SEC-OUT-4 | FR-10.1 | Editing Interface | DEFINED |
| SEC-OUT-5 | FR-1.3, FR-3.6 | Public Site client-side code | DEFINED |
| SEC-OUT-6 | — | — | WITHDRAWN (build-time feed) |
| SEC-AUTHN-1 | FR-10.5, FR-10.9 | Editing Interface, preview site (TB-2) | DEFINED |
| SEC-AUTHN-2 | FR-10.5, FR-10.9 | Editing Interface, preview site (TB-2) | DEFINED |
| SEC-AUTHN-3 | FR-10.5 | Editing Interface (TB-2) | DEFINED (not enforceable with the decided mechanism; open: SQ-16) |
| SEC-AUTHN-4 | FR-10.5 | Editing Interface OAuth helper (TB-2, TB-3) | DEFINED (open: SQ-21) |
| SEC-AUTHN-5 | FR-10.5 | Editing Interface, Content Store (TB-3) | DEFINED |
| SEC-AUTHZ-1, -2 | FR-10.5 | Content Store, Editing Interface (TB-3) | DEFINED |
| SEC-AUTHZ-3 | FR-10.5 | Content Store, Editing Interface, Site Build (TB-3, TB-4) | DEFINED (accepted risk for the maintainer's token on `dalini/nina-site`; wider reach open: SQ-17) |
| SEC-AUTHZ-4 | FR-10.5, FR-11.2 | Content Store (TB-3) | DEFINED |
| SEC-AUDIT-1 | FR-10.7 | Content Store, Editing Interface | DEFINED (open: SQ-18) |
| SEC-AUDIT-2 | FR-10.4 | Site Build, Static Hosting (TB-4) | DEFINED |
| SEC-AVAIL-1 | FR-10.1 | Content Store, Static Hosting | DEFINED |
| SEC-INPUT-1, -2 | FR-10.2, FR-10.6 | Editing Interface, Site Build | DEFINED |
| SEC-INPUT-3 | FR-10.6 | Site Build, Static Hosting | DEFINED |
| SEC-INPUT-4 | FR-10.1, FR-10.9, FR-11.2 | Site Build | DEFINED |
| SEC-INPUT-5 | FR-10.2, NFR-1 | Editing Interface, Site Build | DEFINED |
| SEC-INPUT-6, -7 | FR-10.1 | Site Build (TB-4) | DEFINED |
| SEC-SECRET-1 | FR-8.2, FR-10.4, FR-10.5 | Site Build, Static Hosting, Instagram Feed Integration, OAuth helper (TB-4) | DEFINED (open: SQ-20) |
| SEC-SECRET-2 | FR-10.4, FR-10.9 | Site Build pipeline (TB-4) | DEFINED (open: SQ-18) |
| SEC-SECRET-3 | FR-8.2 | Instagram Feed Integration (TB-5) | DEFINED |
| SEC-CICD-1 | FR-10.4 | Site Build pipeline (TB-4) | DEFINED (open: SQ-20) |
| SEC-CICD-2 | FR-10.1, FR-10.4 | Site Build pipeline, Editing Interface (TB-2, TB-4) | DEFINED |
| SEC-CICD-3, -4, -5 | FR-10.4, FR-10.8, FR-10.9, FR-11.2 | Site Build pipeline, Static Hosting (TB-4) | DEFINED |
| SEC-CICD-6 | FR-10.4 | Site Build pipeline (TB-4) | DEFINED |
| SEC-CICD-7 | FR-10.5 | Content Store (TB-3) | DEFINED |
| SEC-CICD-8 | FR-10.4 | Site Build pipeline, GitHub account | DEFINED |
| SEC-CICD-9 | FR-10.8 | Site Build, Editing Interface (DF-9) | DEFINED (open: SQ-19) |

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

STRIDE per element (`REF-TM`) of the documented system; no implementation exists. Threats that depend on an open decision say so and point to it.

- **Version 1**, 2026-10-31: the system as specified after step 7 (T-1 to T-42).
- **Version 2**, 2026-10-31: re-run of step 8 against the architecture decided in step 10 and the VPS content check that replaced the Actions poll. Every element below, including those step 10 added (OAuth helper, preview site, VPS jobs, publish status file), was checked again against all six categories. T-1 to T-42 keep their IDs; a row that version 2 changed carries a "v2:" note. T-43 to T-68 are new. Six findings need a decision: SQ-16 to SQ-21 (Open Security Questions). Research for version 2 used the Sveltia CMS and Sveltia CMS Authenticator source code (October 2026), GitHub documentation on plan availability, and the Hugo and GoAccess advisories cited in the rows.

### Analysis surface

- **Actors:** Visitor (anonymous); Editor (at v1: Nina and people she authorizes; since step 10: Nina and Ives); Owner (SEC-AUTHZ-2); build/deploy identity; Instagram (external); maintainer (at v1 an ASSUMPTION; since step 10: Ives); GitHub (platform for the Content Store, sign-in and Actions); the VPS provider.
- **Components:** Editing Interface (Sveltia CMS running in the editor's browser) and its OAuth helper on `edit.nina-laaf.de`, Content Store, Site Build, Static Hosting (the VPS with nginx) including the preview site and the jobs running on the VPS (content check, Instagram token refresh, Content Store backup, statistics), Public Site, Instagram Feed Integration (ARCHITECTURE.md Architecture).
- **Trust boundaries:** TB-1 to TB-5 (Required Security Inputs). TB-1 includes the domain and DNS of `nina-laaf.de`. Inside the VPS, processes facing the internet (nginx, the OAuth helper, SSH/rsync) are separated from root and from the stored secrets, backup and logs (SEC-HOST-2).
- **Data flows** (labels for this analysis):
  - DF-1 editor browser → Editing Interface: sign-in, edits, uploads.
  - DF-2 Editing Interface → Content Store: content and media writes.
  - DF-3 legacy import → Content Store (one-time, FR-11.1).
  - DF-4 publish trigger → Site Build: at v1 from the Editing Interface (FR-10.4) and on a schedule (then an ASSUMPTION); since step 10 a content check on the VPS that starts the build through `workflow_dispatch`, plus a nightly build (ARCHITECTURE.md Site Build).
  - DF-5 Content Store, templates and design tokens → Site Build.
  - DF-6 Site Build → Static Hosting: deploy; the build also reads the Instagram token and the deployed feed files over the same SSH channel.
  - DF-7 Static Hosting → visitor browser.
  - DF-8 Instagram → visitor browser after consent (model (a)) or → Site Build (model (b)); step 10 chose (b).
  - DF-9 publish status read by the Editing Interface (FR-10.8, added for T-24); the Site Build writes it on `edit.nina-laaf.de`.
  - DF-10 editor browser → OAuth helper → GitHub OAuth → token back to the Editing Interface (added in v2).
  - DF-11 Content Store → VPS: weekly backup mirror (added in v2).
  - DF-12 nginx access logs → statistics tool → reports (added in v2).
  - DF-13 VPS ↔ Instagram: monthly token refresh (added in v2).
- **Browser-facing surfaces** (DESIGN.md Components): links and language switch, images with caption and credit, downloads, the Instagram posts on the home page (at v1: a consent placeholder and its button); in the Editing Interface, inputs and form feedback and the publish status banner; the preview site's pages.
- **Sensitive data:** see Required Security Inputs.

### STRIDE coverage

Every element was checked against all six categories (version 2). A cell lists the threats found or states that none is credible.

| Element | Spoofing | Tampering | Repudiation | Information Disclosure | Denial of Service | Elevation of Privilege |
|---|---|---|---|---|---|---|
| Editing Interface in the editor's browser (TB-2, DF-1) | T-1, T-2, T-47 | T-3, T-7, T-46 | T-9 | T-4, T-45, T-48 | T-8, T-10 | T-5, T-6 |
| OAuth helper on `edit.nina-laaf.de` (TB-2, DF-10) | T-43 | T-46 | No credible threat identified | T-43 | T-31 | T-61 |
| Content Store (TB-3, DF-2) | No credible threat identified beyond T-1 and T-12 | T-12, T-49 | T-15, T-52, T-53 | T-13, T-14 | T-16, T-17, T-51 | T-11, T-44 |
| Publish trigger: VPS content check (DF-4) | T-22 | T-55 | T-23 | T-62 | T-56, T-58 | No credible threat identified |
| Site Build and pipeline (TB-4, DF-5, DF-6) | T-57 | T-19 | T-23 | T-20, T-21, T-25, T-50 | T-24, T-56 | T-18, T-52, T-54 |
| Publish status file (DF-9) | No credible threat identified | T-60 | No credible threat identified | T-59 | T-24 | No credible threat identified |
| Preview site `preview.nina-laaf.de` (TB-2) | T-67 | T-27 | No credible threat identified | T-21, T-66 | T-31 | No credible threat identified |
| Static Hosting (VPS, nginx), domain and DNS (TB-1, DF-7) | T-26 | T-27, T-28 | No credible threat identified | T-29, T-30 | T-31, T-64 | T-61 |
| VPS jobs and stored data: secrets, backup, logs, statistics (DF-11 to DF-13) | No credible threat identified | T-27 | No credible threat identified | T-63, T-65 | T-39, T-58 | T-61, T-62 |
| Public Site (visitor browser) | No credible threat identified | T-32 | No credible threat identified | T-34 | No credible threat identified | T-33 |
| Instagram Feed Integration (TB-5, DF-8) | T-36 | T-38 | No credible threat identified | No credible threat identified (T-40 does not apply) | T-39, T-68 | T-37 (T-35 does not apply) |
| Legacy import (DF-3) | No credible threat identified | T-41 | No credible threat identified | T-41 | No credible threat identified | No credible threat identified |
| Contact by email (FR-7.1) | No credible threat identified | No credible threat identified | No credible threat identified | T-42 | No credible threat identified | No credible threat identified |

Why no credible threat was identified:

- **OAuth helper, Repudiation:** the helper only relays the GitHub sign-in; GitHub records each authorization in the editor's own account, and changes are attributed in the Content Store (SEC-AUDIT-1).
- **Content Store, Spoofing:** the store has no sign-in of its own in the documents; impersonating an editor is T-1, writing as a non-listed party is T-12.
- **Publish trigger, Elevation of Privilege:** the trigger token can only start the build workflow of `dalini/nina-site`; it cannot change content or code (T-22); command injection through the call is T-55.
- **Publish status file, Spoofing / Repudiation / EoP:** only the Site Build writes it, over the deploy channel (writing it as someone else is T-60); it records no user action; it grants no rights (DR-1).
- **Preview site, Repudiation / EoP:** visitors of the preview only read static files; no server-side code serves them.
- **Static Hosting, Repudiation:** visitors perform no actions; Ives is the only operator, and unattributed changes to served files are covered by T-23 and T-27.
- **VPS jobs, Spoofing / Repudiation:** the jobs only connect out (to GitHub and Instagram, over verified HTTPS) and take no requests; their runs are visible in the build records (SEC-AUDIT-2) and their alerts (SEC-CICD-4, SEC-SECRET-3).
- **Public Site, Spoofing:** visitors have no identity; impersonating the site itself is T-26.
- **Public Site, Repudiation:** visitors are anonymous and change nothing.
- **Public Site, Denial of Service:** pages are static and do no processing a visitor can exhaust; flooding the host is T-31.
- **Instagram, Repudiation:** the integration is read-only and performs no user actions.
- **Instagram, Information Disclosure:** with the build-time model visitors never contact Instagram (T-40 no longer applies); the token is T-37.
- **Legacy import, Spoofing / Repudiation / DoS / EoP:** a one-time import of known files; the result passes the same build checks (SEC-INPUT-3, SEC-INPUT-6, SEC-INPUT-7) and change history (SEC-AUDIT-1) as editor content.
- **Contact by email, all but Information Disclosure:** the site publishes only a `mailto:` link; the mail system is outside this system; tampering with the page is T-27; spam volume is part of T-42.

Version 1 found no credible Elevation of Privilege on Static Hosting because no server-side code serves visitors. Version 2 changes that cell: the VPS now also runs the OAuth helper and jobs that parse outside data (T-61).

### Threats

| Threat ID | STRIDE category | Element (component / boundary / flow) | Threat description | Existing mitigation (FR-* / SEC-* or NONE) | Gap and proposed change |
|---|---|---|---|---|---|
| T-1 | Spoofing | Editing Interface sign-in (TB-2, DF-1) | An external attacker obtains or guesses an editor's credentials (phishing, reused password, repeated guessing) and signs in as Nina; they can publish, deface, unpublish or delete any content. | SEC-AUTHN-1, SEC-AUTHZ-1 | Mechanism undecided; nothing limits guessing or forbids shared accounts. Added SEC-AUTHN-2. Step 10: individual GitHub accounts with 2FA required (SEC-AUTHN-2). |
| T-2 | Spoofing | Editing session (TB-2) | An attacker who obtains a live editing session (left signed in on a shared computer, token leaked in a URL or log, fixed session) continues as the editor without credentials. | SEC-AUTHN-1 (timeouts undecided at v1) | No session rules. Added SEC-AUTHN-3. Step 10: 30 minutes inactivity, 8 hours maximum (SEC-AUTHN-3). v2: the decided mechanism cannot enforce these limits (T-47); open question SQ-16. |
| T-3 | Tampering | Editing actions (TB-2, DF-1) | While Nina is signed in, another website makes her browser send an editing action (cross-site request forgery) or tricks her into clicking one inside a hidden frame (clickjacking), e.g. deleting a work. | NONE (SEC-HTTP-3 covers Static Hosting only) | Added SEC-AUTHN-3 (forgery protection) and SEC-HTTP-4 (no framing). v2: the Editing Interface sends the GitHub token in a request header, not a cookie, so other sites cannot make the browser send it; the sign-in flow is protected by SEC-AUTHN-4 (T-43). |
| T-4 | Information Disclosure | Editor browser ↔ Editing Interface (TB-2) | A network attacker (e.g. public Wi-Fi) reads credentials, session tokens or unpublished content if the Editing Interface is reachable without HTTPS. | NONE (SEC-HTTP-1 covers Static Hosting only) | Added SEC-HTTP-4. |
| T-5 | Elevation of Privilege | Editing Interface views and previews | Script stored in content (by an editor, a stolen editor account, or imported legacy HTML) runs in the Editing Interface when another editor opens the item, with that editor's session; if the viewer is the owner, it can add editors (SEC-AUTHZ-2). | SEC-OUT-1 (Site Build output only) | Added SEC-OUT-4: the same encoding in the Editing Interface. v2: the token sits in browser storage, so such a script can also steal it (T-45); SEC-HTTP-4 adds a CSP for the Editing Interface. |
| T-6 | Elevation of Privilege | Editor management (TB-2, TB-3) | An editor who is not the owner adds or removes editor accounts to keep access or lock Nina out. | SEC-AUTHZ-2 | No gap. |
| T-7 | Tampering | Uploads (DF-1, DF-2) | An editor account uploads a file that is not what it claims (an executable renamed `.jpg` or `.pdf`, an SVG with script, a PDF with active content), and the site distributes it to visitors. | SEC-INPUT-1, SEC-INPUT-2, SEC-HTTP-3 | PDFs are published as uploaded; residual risk accepted while every upload needs an editor account (T-1). No change. v2: the upload checks run in the browser and are now rechecked by the Site Build (T-49). |
| T-8 | Denial of Service | Uploads → Site Build image processing (DF-1, DF-5) | An image within the file-size limit but with extreme pixel dimensions (decompression bomb) exhausts memory or time in image processing, so every publish fails until it is removed. | SEC-INPUT-1 (file size only), SEC-INPUT-3 (previous site stays online) | Added SEC-INPUT-5. Step 10: 40 megapixels, 10 MB images, 20 MB PDFs (SEC-INPUT-1, SEC-INPUT-5). |
| T-9 | Repudiation | Content changes (DF-2, Content Store history) | A change or deletion (by an editor, or by an attacker using an editor account) cannot be attributed: history authorship may be self-declared, or the editing system may write every change under its own service identity. Nina cannot tell who changed what, or undo it. | SEC-AUTHZ-2 (editor-list changes only); change history (ARCHITECTURE.md Content Store) | Added SEC-AUDIT-1 and REQUIREMENTS.md FR-10.7 (Nina sees history and can restore). v2: direct pushes can carry a self-declared author (T-53). |
| T-10 | Denial of Service | Editing Interface availability | The editing system or its sign-in provider is down; Nina cannot edit or publish. | ARCHITECTURE.md DR-1 (the public site does not depend on it) | Accepted: the public site stays online. No change. |
| T-11 | Elevation of Privilege | Content Store write scope (TB-3 → TB-4) | If templates, build configuration or pipeline definitions sit where editors write (at v1 assumed: one repository), an editor account or the Editing Interface's own store credential can change build code and run it in the Site Build with the deploy and Instagram secrets. | NONE | Added SEC-AUTHZ-3 and ARCHITECTURE.md DR-8. Step 10: templates and pipeline moved to `dalini/nina-site` (maintainer Ives only); accepted risk for Ives's own CMS token (SEC-AUTHZ-3). v2: the token reaches more than `dalini/nina-site` (T-44, SQ-17). |
| T-12 | Tampering | Direct writes to the Content Store (TB-3) | A person or token not on the editor allow-list writes to the store directly, bypassing the Editing Interface's checks (FR-10.6). | SEC-AUTHZ-1, SEC-INPUT-3 | No gap. |
| T-13 | Information Disclosure | Content Store read access (TB-3) | If the store is readable by others (e.g. a public repository), anyone sees unpublished content, deleted items in history, image originals with location metadata, and editor identities. | SEC-AUTHN-1 (Editing Interface only); SEC-AUTHZ-1 covers writes only | Added SEC-AUTHZ-4 (no public read access). |
| T-14 | Information Disclosure | Content Store history and backups | Content Nina deletes (FR-10.1), e.g. an image whose rights were withdrawn or personal data entered by mistake, remains in history and backups and can be recovered by anyone with read access. | NONE | Step 10: only Ives may purge history and backups on a documented legal request, with a purge record (SEC-AUDIT-1 exception). |
| T-15 | Repudiation | Content Store history | Someone with administrative rights on the store rewrites or deletes history, hiding tampering and destroying the record. | NONE | Added SEC-AUDIT-1 (history not rewritable by editors, the Editing Interface or the build identity). v2: branch protection on a private repository depends on the GitHub plan (T-52, SQ-18). |
| T-16 | Denial of Service | Content Store as the single source of truth | The store is lost: account locked or deleted, provider failure, or mass deletion through a compromised editor account. All content and originals are gone. | Change history (protects against edits, not against losing the account) | Added SEC-AVAIL-1 (independent backup, tested restore). Step 10: weekly mirror to the VPS, 30 days, yearly restore test by Ives. v2: the backup is independent of GitHub but shares the VPS with Static Hosting (T-65). |
| T-17 | Denial of Service | Storage of large originals (Content Store) | ~4 MB originals exhaust a storage or transfer quota (at v1 assumed: large-file storage), blocking uploads and builds. | NONE | Step 10: originals committed directly to `dalini/nina-content` without Git LFS (no LFS quota); GitHub's repository size limits remain the constraint (ARCHITECTURE.md Content Store). No rule added. |
| T-18 | Elevation of Privilege | Content Store → Site Build (TB-4, DF-5) | The generator evaluates template or shortcode syntax inside content fields, so text an editor enters runs at build time with access to the build environment and its secrets. | SEC-OUT-1 (encodes output, does not forbid evaluation) | Added SEC-INPUT-6. v2: content processing also runs without secrets (SEC-CICD-6, T-54). |
| T-19 | Tampering | Site Build toolchain (TB-4) | A compromised generator release, image tool, dependency or CI step injects script into every page or steals secrets. | SEC-CICD-2, DEP-1 to DEP-8 | Tools installed outside a lockfile were not covered: SEC-CICD-2 strengthened (build tools pinned, checksum or signature checked). |
| T-20 | Information Disclosure | Pipeline secrets (TB-4) | Builds of non-production branches (editing-system drafts, external pull requests) run with deploy or Instagram secrets, so unreviewed changes can read them. | SEC-SECRET-1, SEC-CICD-1 (only main deploys) | Added SEC-SECRET-2. v2: environments limited to `main` are a paid feature for private repositories (T-52, SQ-18). |
| T-21 | Information Disclosure | Preview deployments (TB-4 → TB-1) | The hosting or CI publishes previews of drafts at public URLs, exposing unpublished content. | SEC-INPUT-4 (production output only) | Added SEC-CICD-3. Step 10: previews exist only at `preview.nina-laaf.de` behind per-editor HTTP Basic Auth, deployed with their own key (SEC-SECRET-2). |
| T-22 | Spoofing | Publish trigger (DF-4) | Anyone who learns the build trigger (e.g. a webhook URL) poses as the Editing Interface and starts builds repeatedly, using up build capacity and delaying real publishes. | SEC-CICD-1 (only main deploys, so content cannot be changed this way) | Added SEC-CICD-4. Step 10: the only trigger is `workflow_dispatch` from the VPS content check with a token limited to Actions: write on `dalini/nina-site`, kept root-only on the VPS (SEC-CICD-4); a leaked token can start builds but cannot change content. v2: a leaked token can still use up the Actions minutes (T-56) or, with dispatch inputs, inject commands (T-55). |
| T-23 | Repudiation | Deploys (DF-6) | After a defacement or a bad publish, nobody can tell which Content Store version is live or what triggered the deploy, and the previous site cannot be restored quickly. | NONE | Added SEC-AUDIT-2. |
| T-24 | Denial of Service | Publishing (DF-4 to DF-6) | Publishing fails without anyone noticing (invalid content under SEC-INPUT-3, a failed scheduled build, an Instagram fetch error): Nina's changes never appear, and upcoming/current/past grouping (FR-4.1) goes stale. | SEC-INPUT-3 (previous site stays online) | Added REQUIREMENTS.md FR-10.8 (publish status shown to Nina) and the DF-9 status read in ARCHITECTURE.md; feed failures: T-39. v2: a publish that never starts is not a failure: T-58. |
| T-25 | Information Disclosure | Build output (DF-6) | Full-resolution originals or files with metadata end up in the static output (generators can copy page resources), exposing GPS and device data and unprotected originals; PDFs carry author, software or embedded-image metadata. | SEC-PRIV-4 (images), NFR-1 | SEC-PRIV-4 strengthened (PDFs; every file in the output checked). Step 10: originals never published, images at most 2400 px (FR-3.10); copyright and creator metadata kept (SEC-PRIV-4). |
| T-26 | Spoofing | Domain and DNS of `nina-laaf.de` (TB-1) | An attacker controls the domain or a DNS record (registrar or DNS account takeover, lapsed registration, a record still pointing to a retired host) and serves a fake nina-laaf.de with a valid certificate, e.g. with a false contact address to approach collectors. | NONE (SEC-HTTP-1 does not help once DNS is controlled) | Added SEC-HOST-1. Step 10: Ives holds registrar, DNS and VPS accounts. |
| T-27 | Tampering | Hosting account (TB-1) | Someone with a hosting login or a leaked deploy credential replaces served files directly, bypassing the pipeline (defacement, injected script). | SEC-CICD-1, SEC-SECRET-1 (deploy identity only) | Human hosting accounts were not covered: SEC-HOST-1; recovery: SEC-AUDIT-2. v2: the server itself: SEC-HOST-2 (T-61). |
| T-28 | Tampering | Visitor browser ↔ Static Hosting (TB-1) | A network attacker alters pages in transit or downgrades to HTTP; another site frames the pages to mislead visitors. | SEC-HTTP-1, SEC-HTTP-3 | No gap. |
| T-29 | Information Disclosure | Static Hosting content (TB-1) | The deploy uploads more than the generated output (repository metadata such as `.git`, source content including unpublished items, configuration), all reachable by URL. | SEC-INPUT-4 (generated content only) | Added SEC-CICD-5. |
| T-30 | Information Disclosure | Hosting access logs | The host logs visitor IP addresses (personal data under GDPR) with unknown retention and access. | NONE | Step 10: full IPs kept 14 days, readable only by Ives, described in the Datenschutzerklärung: added SEC-PRIV-6. |
| T-31 | Denial of Service | Static Hosting (TB-1) | A traffic flood makes the site unavailable or, with usage-based pricing, causes costs. | NONE | Step 10: VPS at a fixed price (no usage-based cost); nginx per-IP request and connection limits; a large flood taking the site down is accepted. v2: the same limits cover `edit.nina-laaf.de` (OAuth helper) and `preview.nina-laaf.de`. |
| T-32 | Tampering | Client-side code in the Public Site | If client-side code is added (consent placeholder, language preference, feed rendering) and writes values from the URL, browser storage or the feed into the page as HTML, a crafted link runs script in the site's origin (DOM-based XSS). | SEC-HTTP-2 (CSP, partial) | Added SEC-OUT-5. Step 10: small first-party vanilla JavaScript is allowed (ARCHITECTURE.md), so SEC-OUT-5 applies. |
| T-33 | Elevation of Privilege | Public Site origin ↔ Editing Interface (TB-1, TB-2) | If the Editing Interface is served from the public origin (e.g. a path under nina-laaf.de) and keeps editor credentials in the browser, any script that runs in the Public Site (T-32, T-35, a gap in SEC-OUT-1) acts as the editor. | SEC-OUT-1, SEC-HTTP-2 | Added ARCHITECTURE.md DR-9 (separate origin). Step 10: Editing Interface on `edit.nina-laaf.de`. |
| T-34 | Information Disclosure | Public Site before consent | Editor content or a template references a third-party resource (an external image URL, an embed), sending visitors' IP addresses to third parties without consent. | SEC-PRIV-1, SEC-OUT-1, SEC-HTTP-2 | No gap. |
| T-35 | Elevation of Privilege | Instagram content in the browser (model (a), TB-5) | After consent, script from Instagram/Meta runs in the site's own origin and can read or change any page, and the editor's session if T-33 applies. | SEC-PRIV-2 (only after consent), SEC-HTTP-2 (allowed origins) | Step 10: does not apply; the build-time model was chosen and no Instagram/Meta script reaches visitors. SEC-OUT-6 withdrawn. |
| T-36 | Spoofing | @ninalaaf account → site (TB-5) | An attacker who takes over the Instagram account posts as Nina, and the posts appear on nina-laaf.de (live in (a), at the next build in (b)) without her review. | SEC-OUT-3 (encoding only) | Step 10: posts appear without review; Nina can hide a post or switch the feed off in the CMS (REQUIREMENTS.md FR-8.6). Residual risk accepted until the next publish after a takeover. |
| T-37 | Elevation of Privilege | Instagram access token (model (b)) | A token with more than read access, or a long-lived one, leaks and lets an attacker post as Nina or read account data. | SEC-SECRET-1 | Added SEC-SECRET-3 (read-only). Step 10: scope `instagram_business_basic`; 60-day token in a root-only file on the VPS, refreshed monthly by cron, alert to Ives (SEC-SECRET-1, SEC-SECRET-3). v2: the build reads the root-only token over the deploy channel, which conflicts with a non-root deploy account (T-62, SQ-20). |
| T-38 | Tampering | Instagram → Site Build (model (b), DF-8) | Fetched captions, links or media are malicious (markup in a caption, a crafted image aimed at the image processor, a non-HTTPS link) or altered in transit. | SEC-OUT-2, SEC-OUT-3, SEC-PRIV-3 | SEC-OUT-3 strengthened (verified HTTPS; media pass SEC-INPUT-1 and SEC-INPUT-5). Step 10: build-time model chosen, so this threat applies. v2: what happens to a post that fails these checks: T-68. |
| T-39 | Denial of Service | Instagram availability (TB-5) | Instagram is unavailable or the token has expired: in (a) the feed does not load; in (b) the whole build can fail and block all publishing. | FR-8.4 (the page still renders) | Build-time case not covered: added REQUIREMENTS.md FR-8.5. Step 10: the last successfully fetched posts stay on the site (FR-8.5). v2: a single failing post must not fail the build either (T-68). |
| T-40 | Information Disclosure | Instagram after consent (model (a)) | Once the visitor consents, Meta receives the visitor's IP address and browser data. | SEC-PRIV-1, SEC-PRIV-2 | Step 10: does not apply; visitors never contact Instagram (build-time model, SEC-PRIV-3). |
| T-41 | Tampering | Legacy import (DF-3) | Legacy text contains raw HTML (the legacy CV and contact pages in `docs/legacy-content/pages/` contain `<a>`, `<p>` and `<div>`), and legacy images keep EXIF data; the one-time import bypasses the Editing Interface. | SEC-OUT-1, SEC-INPUT-3, SEC-INPUT-4, SEC-PRIV-4, FR-11.2 | No gap. |
| T-42 | Information Disclosure | Contact by email (FR-7.1) | Nina's published email address is harvested for spam and phishing. | NONE | Accepted: published on purpose (FR-7.1). No change. |
| T-43 | Spoofing | OAuth helper on `edit.nina-laaf.de` (TB-2, DF-10) | Another website opens the helper's sign-in window and receives the editor's GitHub token from the message the helper sends back, or starts a sign-in without a matching `state` so that the editor's browser completes a flow the attacker began. The Sveltia CMS Authenticator restricts the receiving page only when its allowed-domains setting is filled in (`REF-SVELTIA-AUTH`). The helper also holds the GitHub OAuth client secret, which no rule lists. | SEC-AUTHN-1 (requires a sign-in, not how the token is released) | Added SEC-AUTHN-4 (token released only to `https://edit.nina-laaf.de`, `state` verified, no `workflow` scope); client secret added to SEC-SECRET-1. Runtime of the helper on the VPS: SQ-21. |
| T-44 | Elevation of Privilege | Editor token scope (TB-3) | The authenticator's default GitHub OAuth App scope `repo,user` (`REF-SVELTIA-AUTH`) gives each editor's token read and write access to every repository that editor can reach, plus profile changes. Ives's token also carries his admin rights on `dalini/nina-content` (branch protection, collaborators) and on all his other repositories. A token stolen from the browser (T-45) or a device (T-47) acts with all of it and does not expire. | SEC-AUTHZ-3 (accepted risk for `dalini/nina-site` only); SEC-AUTHN-2 (a second factor does not limit a token) | SEC-AUTHZ-3 strengthened: the credential MUST be narrowed to the smallest scope the editing system supports, or the wider reach accepted explicitly (SQ-17). Added SEC-AUTHN-5 (revocation). |
| T-45 | Information Disclosure | Editor token in the browser (Editing Interface, TB-2) | Sveltia CMS keeps the GitHub token in the browser's local storage of `edit.nina-laaf.de` (key `sveltia-cms.user`). Any script that runs in that origin (stored markup that slips past SEC-OUT-4, a manipulated CMS bundle, T-46) can read the token and send it out. No Content Security Policy is defined for that host. | SEC-OUT-4; ARCHITECTURE.md DR-9; SEC-HTTP-4 (no CSP) | SEC-HTTP-4 strengthened: CSP for the Editing Interface (script only from its own origin, connections only to its own origin and GitHub). |
| T-46 | Tampering | Editing Interface code supply (TB-2) | Sveltia CMS is usually loaded from a CDN at its latest version, and the project supports only its newest release. A compromised or faulty release, or a script served by the CDN, runs in the editors' browsers with their tokens. The OAuth helper and its runtime are also third-party code on the VPS. | SEC-CICD-2 (CI only); DEP-1 to DEP-8 | SEC-CICD-2 strengthened: the CMS bundle and the OAuth helper are served from the VPS, pinned to an exact release and verified; Ives updates them after each security release. |
| T-47 | Spoofing | Editing session lifetime (TB-2) | Signing out of Sveltia CMS only deletes the token in that browser. The GitHub OAuth App token stays valid at GitHub and does not expire, and the CMS has no inactivity timeout. Anyone who copies the token, or uses a lost or shared device before sign-out, acts as the editor indefinitely. SEC-AUTHN-3 (session ends on sign-out, 30 minutes inactivity, 8 hours maximum) cannot be met with this mechanism. | SEC-AUTHN-3 (not enforceable as decided) | Gap raised as SQ-16, as SEC-AUTHN-3 requires. Added SEC-AUTHN-5 (revocation). Until SQ-16 is decided, editors do not sign in on devices others use (SEC-AUTHN-3). |
| T-48 | Information Disclosure | Locally cached content (editor's device) | Sveltia CMS caches repository files, including unpublished items and asset thumbnails, in the browser's IndexedDB. Sign-out does not clear that cache, so the next user of a shared device can read unpublished content. | NONE | SEC-AUTHN-3 strengthened: sign-out MUST remove cached content, or SQ-16 decides otherwise; no shared devices meanwhile. |
| T-49 | Tampering | Direct writes that bypass the Editing Interface's checks (DF-2) | The required-field, file-type, size, SVG and pixel checks at upload run in the editor's browser. Anyone with an editor token or account can commit files directly through the GitHub API or Git. The Site Build rechecks content rules (DR-3) and pixel size (SEC-INPUT-5), but not file type, file size or SVG content. | ARCHITECTURE.md DR-3; SEC-INPUT-3; SEC-INPUT-5 | SEC-INPUT-1 and SEC-INPUT-2 strengthened: the Site Build rechecks every file it publishes. DR-3 references them. |
| T-50 | Information Disclosure | Content Store checkout → Site Build (DF-5) | A symbolic link committed to the Content Store (possible by a direct write, T-49) points outside the checkout. A generator lookup that follows it reads files on the build machine, including credentials, and publishes them. Hugo had such confinement bypasses in direct lookups until v0.165.0 (`REF-HUGO-ADV`). | SEC-CICD-2; DEP-5 (known vulnerabilities) | Added SEC-INPUT-7 (no symbolic links or special files in the checkout) and SEC-CICD-6 (no secrets during content processing). |
| T-51 | Denial of Service | GitHub Actions in `dalini/nina-content` (TB-3) | An editor account (through GitHub's web interface, or with a token that has the `workflow` scope) adds a workflow to the Content Store. It runs on the same account's Actions minutes (T-56) and is a trigger path that SEC-CICD-4 says does not exist. | SEC-CICD-4 (no push trigger into `dalini/nina-site`); SEC-AUTHN-4 (no `workflow` scope) | Added SEC-CICD-7 (Actions disabled in `dalini/nina-content`, no secrets there). |
| T-52 | Repudiation | Platform controls of the private repositories (Content Store, pipeline) | Branch protection on `main` of the private `dalini/nina-content` (SEC-AUDIT-1) and a `production` environment limited to `main` (SEC-SECRET-1, SEC-SECRET-2) are available for private repositories only on paid GitHub plans (`REF-GH-PLANS`). Whether `dalini` is a personal account or an organization, and its plan, are UNKNOWN. On GitHub Free an editor token can force-push `main` and rewrite the history, and the deploy secrets are not limited to `main`. | SEC-AUDIT-1, SEC-SECRET-2 (both assume these features) | Decision needed: SQ-18. The rules stay as they are; their traceability status records the dependency. |
| T-53 | Repudiation | Commit authorship (Content Store history) | Git author names are self-declared. A direct push (T-49) with an editor token can carry another editor's name, so the history shows a false author. | SEC-AUDIT-1 (identity from the sign-in, for Editing Interface commits) | SEC-AUDIT-1 strengthened: only commits that GitHub marks as verified are accepted on `main` (depends on SQ-18). |
| T-54 | Elevation of Privilege | Site Build steps that hold secrets (TB-4) | The steps that parse editor-controlled files (Markdown, images, PDFs) run in the same job as the deploy keys and the Instagram token. A parser flaw, a generator bug (T-50) or template evaluation (T-18) then reaches those secrets. | SEC-SECRET-2; SEC-INPUT-6 | Added SEC-CICD-6 (content processing without secrets) and the stage split in ARCHITECTURE.md Site Build. |
| T-55 | Tampering | `workflow_dispatch` call (DF-4) | If the build workflow accepts dispatch inputs (e.g. a content version) and uses them inside shell commands, anyone with the trigger token can inject commands into a run that later holds the deploy key. | SEC-CICD-4 (token scope) | SEC-CICD-4 strengthened: no inputs, or only a validated commit ID used as data. |
| T-56 | Denial of Service | Actions minutes of the account (Site Build pipeline) | A leaked trigger token (T-22), a workflow in the Content Store (T-51) or a stuck build uses up the 2,000 included minutes. Builds of private repositories then stop until the next month; with paid overage enabled they cause costs instead. | One concurrency group (ARCHITECTURE.md Site Build); FR-10.8 (failure email) | Added SEC-CICD-8 (job time limit, no paid overage, included-usage alerts to Ives, token rotation after unexplained runs). |
| T-57 | Spoofing | Identity of the VPS towards the Site Build (DF-6) | Without a pinned SSH host key, the runner can be made to connect to another host that poses as the VPS. The deploy then goes nowhere, and the build reads forged feed files and a forged Instagram token from it. | SEC-CICD-1 (deploy key limits) | SEC-CICD-1 strengthened: the runner verifies a pinned host key. |
| T-58 | Denial of Service | VPS content check (DF-4) | The content check stops silently (cron not running, VPS down, its read credential or the trigger token expired or revoked). No build starts, so nothing fails and FR-10.8 never fires. Nina's changes wait for the nightly build, or longer if the VPS is down, breaking FR-10.4 unnoticed. | FR-10.8 (failed publishes only); nightly build | SEC-CICD-4 strengthened: the content check alerts Ives when it fails. REQUIREMENTS.md FR-10.8 extended: a change not live 15 minutes after saving is shown as not live. |
| T-59 | Information Disclosure | Publish status file (DF-9) | The status file lives on `edit.nina-laaf.de`, which serves the CMS without a server-side sign-in, so anyone can request it. The failing item and field, or error text, can reveal unpublished items. | ARCHITECTURE.md DR-1 (the status carries no content) | Added SEC-CICD-9 (only the listed fields; no content values or log output). Whether the file must be readable only by editors: SQ-19. |
| T-60 | Tampering | Publish status file (DF-9) | Someone who can write the file (deploy key, VPS access) shows Nina a false "live" state, or places markup in it that runs in the Editing Interface. | SEC-CICD-1; SEC-HOST-1 | SEC-OUT-4 strengthened: the status is shown as text. Write access: SEC-CICD-1, SEC-HOST-2. |
| T-61 | Elevation of Privilege | VPS (Static Hosting) | The VPS runs every internet-facing process of the system (nginx for three hosts, the OAuth helper, SSH/rsync) and jobs that parse outside data: the statistics tool parses visitor-controlled log lines (GoAccess 1.10.2 had a heap overflow triggered by a crafted User-Agent, CVE-2026-54715, fixed in 1.11). Unpatched software, a weak administrative login or a process running as root lets one flaw take over all three sites, every secret on the VPS, the backup and the access logs. | SEC-HOST-1 (provider accounts); SEC-SECRET-1 (root-only files) | Added SEC-HOST-2 (security updates, SSH keys only, only the needed ports open, no internet-facing process as root). |
| T-62 | Elevation of Privilege | Deploy account and root-only secrets on the VPS (TB-4) | SEC-SECRET-1 keeps the Instagram token in a root-only file, but the build reads it over the deploy SSH channel; the OAuth helper's client secret and the content check's token raise the same issue. Either the deploy account and those processes can read root-only files, which makes a leaked deploy key or a helper flaw a full VPS takeover, or they cannot read their secrets. | SEC-CICD-1 (deploy key limited to the web root); SEC-SECRET-1 | SEC-CICD-1 strengthened (the deploy account never has root rights); SEC-HOST-2 (no consumer runs as root). How they read their secrets: SQ-20. |
| T-63 | Information Disclosure | Statistics reports (VPS, DF-12) | Reports generated from the access logs can list visitor IP addresses, paths and user agents. Written into a served directory they are public; kept as files they outlive the 14-day log retention; log lines crafted by visitors can carry markup into an HTML report that Ives opens. | SEC-PRIV-5; SEC-PRIV-6 (logs only) | SEC-PRIV-5 strengthened: reports readable only by the maintainer, not served, no full IP addresses beyond 14 days, tool kept current. |
| T-64 | Denial of Service | TLS certificates (TB-1, TB-2) | A certificate renewal fails unnoticed. Because HSTS is set, browsers refuse the expired site without a way to continue, and editors cannot sign in. | NONE (certificate source UNKNOWN) | Added SEC-HTTP-5 (automatic renewal, alert on failure). |
| T-65 | Information Disclosure | Content Store backup on the VPS (DF-11) | The weekly mirror (history, originals with metadata, unpublished content) sits on the internet-facing VPS. If it lands under a served directory, or is readable by the web server or deploy account, it can be downloaded. | SEC-AVAIL-1; SEC-AUTHZ-4 (the GitHub copy only); SEC-CICD-5 (deploy output only) | SEC-AVAIL-1 strengthened: outside served directories, readable only by the maintainer. Residual risk stated: the backup is not independent of the VPS. |
| T-66 | Information Disclosure | Preview site (TB-2) | Preview pages render unpublished editor content, but the CSP of SEC-HTTP-2 applies only to public responses. A script that slips past SEC-OUT-1 runs on `preview.nina-laaf.de` with the editor's Basic Auth credentials and can read every preview page and send it out. | SEC-OUT-1; SEC-CICD-3 | SEC-HTTP-4 strengthened: the preview site sends the SEC-HTTP-2 CSP. |
| T-67 | Spoofing | Preview sign-in (TB-2) | nginx accepts password files with plain, SHA-1 and MD5-based entries; whoever reads the file (backup, VPS access) can recover weak entries, and a password reused from GitHub would open the CMS as well. Basic Auth also has no sign-out or expiry: the browser keeps the credentials until it is closed. | SEC-AUTHN-1; SEC-AUTHN-2 (individual credentials, limited guessing) | SEC-AUTHN-2 strengthened (unique passwords, strongest available hash). No sign-out or expiry: a property of Basic Auth, chosen in step 10 (Q-4f) for a read-only preview; no change. |
| T-68 | Denial of Service | Instagram media in the build (TB-5, DF-8) | What happens to a recent post whose media fails the checks of SEC-OUT-3 (a video, a file over the limits, a crafted image, possibly posted on purpose after a takeover, T-36) is undefined. If it is treated like content under SEC-INPUT-3, it fails every build and blocks all publishing until the post is hidden. | FR-8.5 (refresh failure only); SEC-OUT-3 | SEC-OUT-3 strengthened and REQUIREMENTS.md FR-8.5 extended: a failing post is left out and the build continues. |

## Prompt Placeholders To Resolve

- `{{CODE_QUALITY_PROMPT}}` — RESOLVED (no local prompt found): low cyclomatic and cognitive complexity; small cohesive functions and modules; separated presentation, business-rule, persistence and integration concerns; explicit error handling and trust-boundary transitions; no duplicated security-sensitive logic; testability without hidden global state.
- `{{API_SECURITY_PROMPT}}` — RESOLVED (no local prompt found): the public site has no API. APIs used: GitHub API (Sveltia CMS, editor OAuth tokens; SEC-AUTHN-1 to -5, SEC-AUTHZ-1 to -3) and Instagram API with Instagram Login (build time, read-only; SEC-OUT-3, SEC-SECRET-3). Baseline: `REF-AUTH`, `REF-SECRETS`, `REF-INPUT`.
- `{{BACKEND_FRAMEWORK_PROMPT}}` — RESOLVED (no local prompt found): Hugo static site generator; rules SEC-OUT-1, SEC-INPUT-3, SEC-INPUT-5, SEC-INPUT-6, SEC-INPUT-7, SEC-CICD-2, SEC-CICD-6. Baseline: `REF-XSS`, `REF-INPUT`.
- `{{FRONTEND_FRAMEWORK_PROMPT}}` — RESOLVED (no local prompt found): no client framework; small first-party vanilla JavaScript only (SEC-OUT-5, SEC-HTTP-2). Baseline: `REF-XSS`.
- `{{AUTH_PROMPT}}` — RESOLVED (no local prompt found): GitHub OAuth sign-in with 2FA for the CMS, per-editor HTTP Basic Auth for the preview site (SEC-AUTHN-1 to -5). Baseline: `REF-AUTH`, `REF-SESSION`.
- `{{DEPLOYMENT_PROMPT}}` — RESOLVED (no local prompt found): GitHub Actions in `dalini/nina-site`, rsync/SSH to a VPS with nginx (SEC-HTTP-1 to -5, SEC-HOST-1, -2, SEC-SECRET-1, -2, SEC-CICD-1 to -9). Baseline: `REF-SSDF`, `REF-CICD`, `REF-SUPPLY`.

## Open Security Questions

SQ-1 to SQ-15 were resolved in step 10 (`.planning/OPEN_QUESTIONS.md`); the decisions are written into the rules above. The step 8 re-run (threat model v2) raised SQ-16 to SQ-21. Each rule they touch stays in force until the question is answered.

- **SQ-16** (T-2, T-47, T-48; SEC-AUTHN-3) How is SEC-AUTHN-3 met for the Editing Interface? Sveltia CMS signs out only in the browser and leaves its cached content there, it has no inactivity timeout, and GitHub OAuth App tokens do not expire. TO BE DECIDED. Possible directions: a GitHub App whose user tokens expire after 8 hours, which meets the maximum but not the inactivity limit (whether Sveltia CMS and its authenticator support it is UNKNOWN; the authenticator currently passes on only the access token), together with clearing local data on sign-out; or a documented exception that relies on SEC-AUTHN-5 revocation.
- **SQ-17** (T-11, T-44; SEC-AUTHZ-3) Which scope may the Editing Interface credential have? The default `repo,user` reaches every repository of each editor and, for Ives, his admin rights. TO BE DECIDED. Possible directions: a GitHub App installed only on `dalini/nina-content` with content read and write (support UNKNOWN, as in SQ-16); a separate GitHub account without admin rights for Ives's editing; or explicit acceptance of the wider reach.
- **SQ-18** (T-20, T-52, T-53; SEC-AUDIT-1, SEC-SECRET-1, SEC-SECRET-2) Which account type and plan hold `dalini/nina-content` and `dalini/nina-site` (UNKNOWN)? Branch protection or rulesets, required verified commits, and environments with deployment branches need GitHub Pro (personal account) or GitHub Team (organization) for private repositories (`REF-GH-PLANS`). TO BE DECIDED: a paid plan, or another way to meet these rules. Making `dalini/nina-site` public would provide environments but would not protect `dalini/nina-content`, which must stay private (SEC-AUTHZ-4); and in a public repository, scheduled workflows are disabled after 60 days without repository activity, which would stop the nightly build (FR-10.4).
- **SQ-19** (T-59; SEC-CICD-9) Must the publish status file be readable only by editors, or is a public file with only the SEC-CICD-9 fields acceptable? TO BE DECIDED. An editor-only file needs an access check on `edit.nina-laaf.de`, which the GitHub sign-in in the browser does not provide by itself.
- **SQ-20** (T-37, T-43, T-62; SEC-SECRET-1, SEC-CICD-1, SEC-HOST-2) How do processes that must not run as root (the deploy account, the OAuth helper, the content check, the token refresh, the backup) read their secrets, which SEC-SECRET-1 keeps in root-only files? TO BE DECIDED. Possible directions: each secret owned by the one unprivileged account that uses it, with SEC-SECRET-1 changed to match; or a root job that passes on only what each process needs. Until decided, SEC-CICD-1 and SEC-HOST-2 hold: no consumer runs as root.
- **SQ-21** (T-43, T-46, T-61; SEC-AUTHN-4, SEC-CICD-2, SEC-HOST-2) Which runtime runs the OAuth helper on the VPS? The Sveltia CMS Authenticator is written for Cloudflare Workers (`REF-SVELTIA-AUTH`); running it on the VPS needs a compatible runtime or a port. TO BE DECIDED; the choice falls under DEP-1 to DEP-8, SEC-CICD-2 and SEC-HOST-2.
