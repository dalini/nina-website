# Open questions — step 10 interview record

Step 10 (`prompts/10-resolve-open-questions.md`), 2026-10-31. One ordered list of every open item found in `REQUIREMENTS.md`, `DESIGN.md`, `ARCHITECTURE.md`, `SECURITY.md`, `CLAUDE.md`, `style-guide.html` and `.planning/github-issues/` (epic, workstreams, leaf issues, ISSUE_PLAN PQ-1 to PQ-28), most foundational first. Duplicates across files are merged into one entry; the Sources column lists every marker the entry clears. `{{ISSUE_URL:...}}` markers are left for the CREATE run.

Status: `ANSWERED` or `DEFERRED` (by Ives). Every question is answered; rows Q-2b and later that are out of numeric order were raised by earlier answers or by conflicts found while writing the specs.

## Deferred by Ives (kept visible)

- **Final logo mark** (Q-45): `logo.svg` stays a provisional placeholder until Nina decides; `DESIGN.md` and `style-guide.html` mark it provisional.
- **Test command** (Q-50): `CLAUDE.md` keeps `Test: TO BE DECIDED`; the first coding PR (step 11) sets it.

## Follow-ups for implementation (from the answers, not open questions)

- Session timeouts (Q-9): verify what Sveltia CMS and GitHub OAuth tokens can enforce; raise any gap as a new question (`SECURITY.md` SEC-AUTHN-3).
- @ninalaaf must be an Instagram Business or Creator account for the Instagram API with Instagram Login (Q-26).
- Re-run the threat model (step 8) against the decided architecture before the first release (`SECURITY.md` Threat model status).

## Where the answers were written

`REQUIREMENTS.md` (OQ-1 to OQ-10, PQ-8 to PQ-11, PQ-13, PQ-15, PQ-19, PQ-21 to PQ-24, PQ-26), `ARCHITECTURE.md` (AQ-1 to AQ-5, PQ-1, PQ-16 to PQ-18, PQ-27), `SECURITY.md` (SQ-1 to SQ-15, PQ-2 to PQ-7, PQ-12, PQ-14, PQ-20, PQ-28), `DESIGN.md` and `style-guide.html` (DQ-1 to DQ-4, PQ-25), `CLAUDE.md` (Commands), `.planning/github-issues/` (epic, workstreams, leaf issues, ISSUE_PLAN answers column).

| # | Question | Sources | Status | Answer |
|---|---|---|---|---|
| Q-1 | Static site generator | AQ-1, PQ-16, SECURITY `{{BACKEND_FRAMEWORK_PROMPT}}`, issue Constraints/Test Environment | ANSWERED | Hugo. |
| Q-2 | Editing system; Editing Interface ↔ Content Store write contract, publish trigger, publish-status format | AQ-3, PQ-1, API style (Required Architecture Inputs), SECURITY `{{API_SECURITY_PROMPT}}` | ANSWERED | Sveltia CMS (Git-based, Decap-compatible config), admin on its own origin (DR-9). |
| Q-3 | Form of the Content Store, content storage format, where large originals are kept | AQ-5, PQ-18, Data model expectations, ASSUMPTION (repo + large-file storage), T-17 | ANSWERED | Private GitHub repository; content as Markdown with YAML front matter; image originals committed directly, no Git LFS. |
| Q-4 | Hosting provider and CI/CD platform; where `nina-laaf.de` is hosted today; previews | AQ-4, PQ-17, Deployment model, SECURITY Deployment and CI/CD model, `{{DEPLOYMENT_PROMPT}}`, SEC-CICD-3, T-21 | ANSWERED | Own server: rented VPS/root server in Germany, administered by us; nginx serves the static files; GitHub Actions builds on main and deploys only the Hugo output over rsync/SSH with a restricted deploy key. |
| Q-5 | Responsive image sizes: produced by the Site Build or by the host | AQ-5, PQ-5 | ANSWERED | Produced by the Hugo build; originals are never published. |
| Q-6 | Client-side code in the Public Site | AQ-2, PQ-27, Client framework, `{{FRONTEND_FRAMEWORK_PROMPT}}`, SEC-OUT-5 | ANSWERED | Small first-party vanilla JS allowed (e.g. an image viewer on work pages); no framework. SEC-OUT-5 applies. |
| Q-7 | When builds run (scheduled builds) and the maximum publish delay | ARCHITECTURE Site Build ASSUMPTION, DF-4 ASSUMPTION, FR-10.4 | ANSWERED | Build on every publish plus a nightly scheduled build (Europe/Berlin); maximum publish delay 15 minutes. |
| Q-8 | Scale expectations | Required Architecture Inputs (UNKNOWN) | ANSWERED | Small: 1–3 editors, up to a few hundred works, low thousands of visits per month with occasional peaks. |
| Q-9 | Editor authentication, second factor, session timeouts | SQ-1, PQ-2, Session model, `{{AUTH_PROMPT}}`, SEC-AUTHN-1 to -3 | ANSWERED | Individual GitHub accounts via GitHub OAuth (helper self-hosted on the own server); 2FA required; failed sign-ins limited by GitHub. Sessions: 30 min inactivity, 8 h maximum (implementation verifies what Sveltia and GitHub tokens enforce). |
| Q-10 | Editors besides Nina; owner who may add/remove editors | SQ-2, PQ-3 | ANSWERED | Editors: Nina and Ives. Owners (may add/remove editors): Nina and Ives. |
| Q-11 | Maintainer role and its access | SQ-10, PQ-4, ASSUMPTION (Required Security Inputs, Analysis surface) | ANSWERED | Maintainer: Ives (repository admin, CI, server). |
| Q-12 | Holders of registrar, DNS and hosting accounts, today and later | SQ-13, PQ-12, ARCHITECTURE Static Hosting (UNKNOWN), TB-1 provider UNKNOWN | ANSWERED | Ives holds the registrar/domain, DNS and VPS accounts, today and in future. |
| Q-13 | Default language and what the site root shows | OQ-2, PQ-8, FR-1.6 | ANSWERED | English at the root (/), German under /de/; no automatic redirect. |
| Q-14 | What a visitor sees when one language version is empty | OQ-10, PQ-9, FR-1.7 | ANSWERED | Show the version in the other language, marked with its own lang attribute; no extra note. |
| Q-15 | Home page: one featured image or a selection; other home content | OQ-3, PQ-10, FR-2.1 | ANSWERED | One large featured image (linked to its work page) plus the current exhibition entry. |
| Q-16 | Exhibition/News dates, boundary days, time zone, news type | PQ-11, FR-4.1, FR-4.2 | ANSWERED | Exhibition dates: start and end date both required; both days inclusive; today is taken in Europe/Berlin. |
| Q-17 | Work year: single year or range | PQ-24 | ANSWERED | Single four-digit year only. |
| Q-18 | Required fields (materials/dimensions, Texts, CV, Downloads, Contact email, legal Pages) | PQ-13, FR-3.2 vs FR-10.6 | ANSWERED | Materials and dimensions optional on save; shown when entered, omitted when empty (FR-3.2 changes). |
| Q-19 | CV sections: own entries or derived; short bio; empty sections | PQ-23 | ANSWERED | Solo exhibitions, group exhibitions and works in public space are derived from Exhibition entries and public-space works, but editable (see Q-19b); education and awards/grants are entered as CV entries; empty sections hidden. |
| Q-20 | Allowed rich-text elements and which fields are rich text | PQ-20, SEC-OUT-1 | ANSWERED | Paragraphs, headings h2–h3, bold, italic, links, lists, block quotes; plus inline images from the Content Store inside Text bodies only. Rich text only in Text bodies, Page content, News text, work short text and CV bio; all other fields plain text. |
| Q-21 | List orders; pages that carry the downloads and the Instagram link | PQ-19 | ANSWERED | Works: newest year first, Nina can reorder manually; Texts: newest first; CV: newest first within each section; Downloads on the CV page; Instagram link in the footer of every page. |
| Q-22 | Legacy migration: selection, English values, alt text | OQ-1, PQ-15, FR-11.1 | ANSWERED | All 53 legacy work series are migrated and published (FR-11.2 no longer applies to the import). |
| Q-23 | Instagram feed model | OQ-6, PQ-7, FR-8.2, TB-5, DF-8 | ANSWERED | Copied at build time (OQ-6 (b)): the Site Build fetches recent posts with a read-only token and self-hosts the images; visitors never request Instagram/Meta. |
| Q-24 | Feed when a build-time refresh fails | FR-8.5, T-39 | ANSWERED | Keep the posts from the last successful fetch; the rest of the build publishes normally. |
| Q-25 | Posts shown without review; quick removal | SQ-15, T-36 | ANSWERED | Recent posts appear automatically; Nina can hide a single post or switch the feed off in the CMS, which triggers a rebuild. |
| Q-26 | Consent storage and isolation, or access method and token lifetime | SQ-4, SEC-PRIV-2, SEC-OUT-6, T-35, T-37 | ANSWERED | Instagram API with Instagram Login, scope instagram_business_basic only (@ninalaaf must be a Business or Creator account); 60-day long-lived token refreshed monthly (see Q-26b for where); Ives is alerted if the refresh fails. Consent storage and origin isolation do not apply (no browser-loaded feed). |
| Q-27 | Page(s) where the feed appears | PQ-7 | ANSWERED | Home page, 6 most recent posts, below the featured image and exhibition. |
| Q-28 | Image protection (watermarks, limited size) | OQ-9, T-25 | ANSWERED | Published images at most 2400 px on the long side; originals never published; no watermarks, no right-click blocking. |
| Q-29 | Performance budget | NFR-1 | ANSWERED | Core Web Vitals 'good' on a mid-range phone over 4G (Lighthouse mobile): LCP ≤ 2.5 s, CLS ≤ 0.1, INP ≤ 200 ms; initial page weight ≤ 1.5 MB. |
| Q-30 | Upload size limits and pixel limit | SQ-8, SEC-INPUT-1, SEC-INPUT-5 | ANSWERED | Images up to 10 MB and 40 megapixels; PDFs up to 20 MB. |
| Q-31 | Image and PDF metadata to keep | SQ-7, PQ-6, SEC-PRIV-4 | ANSWERED | Keep only the copyright notice and the creator (photographer/author); strip all other metadata (GPS, device, software, dates). |
| Q-32 | Who besides Nina is notified of a failed publish | FR-10.8, REQ-CONTENT-030 Alerting | ANSWERED | Ives (maintainer) by email via GitHub Actions failure notifications; Nina sees the status in the CMS. |
| Q-33 | Secret store and who rotates secrets | SQ-3, SEC-SECRET-1 | ANSWERED | Deploy and preview secrets in GitHub Actions secrets of a 'production' environment in dalini/nina-site, limited to main (Instagram token: on the VPS, Q-26b); Ives rotates them at least yearly and after any suspected leak. |
| Q-34 | Backups: frequency, location, retention, restore tests | SQ-12, PQ-14, SEC-AVAIL-1 | ANSWERED | Weekly mirror of the content repository (including originals) to the VPS; 30 days retention; Ives tests a restore once a year. |
| Q-35 | Legal removal from history and backups | SQ-11, PQ-14, SEC-AUDIT-1, T-14 | ANSWERED | Yes: only Ives (maintainer) purges history and backups, only on a documented legal request; a record of the purge (date, reason, no content) is kept. Exception to SEC-AUDIT-1. |
| Q-36 | HSTS max-age; exact CSP directives | SEC-HTTP-1, SEC-HTTP-2, PQ-17 | ANSWERED | HSTS max-age=31536000; includeSubDomains (no preload). CSP: default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; font-src 'self'; base-uri 'self'; form-action 'none'; frame-ancestors 'none'. |
| Q-37 | What the host logs about visitors | SQ-6, T-30, hosting access logs UNKNOWN | ANSWERED | nginx access logs with full IP addresses, kept 14 days for abuse handling, readable only by Ives; described in the Datenschutzerklärung. |
| Q-38 | Traffic floods and spending limit | SQ-14, T-31 | ANSWERED | Per-IP request and connection limits in nginx only; a large flood taking the site down is accepted. |
| Q-39 | Security assurance target; NIST SP 800-53 and Zero Trust mappings in issues | SQ-9, PQ-28, Standards Alignment | ANSWERED | OWASP ASVS 5.0.0 Level 1 for the whole system; NIST SP 800-53 and Zero Trust fields are not applicable to this project. |
| Q-40 | Portfolio PDFs offered | OQ-4, PQ-22, FR-6.1 | ANSWERED | No fixed list: Nina uploads and publishes whichever PDFs she wants in the CMS. |
| Q-41 | galerie burster named as sales contact | OQ-5, PQ-26, FR-7.2 | ANSWERED | galerie burster is linked as representation, not named as the sales contact. |
| Q-42 | Newsletter | OQ-7 | ANSWERED | No newsletter. |
| Q-43 | Analytics | OQ-8, SQ-5, SEC-PRIV-5 | ANSWERED | Cookieless statistics computed from the nginx access logs on the VPS (e.g. GoAccess); no script or tracking in pages. |
| Q-44 | Legal wording (Impressum, Datenschutzerklärung), GDPR articles, reviewer | SECURITY obligations, REQ-LEGAL-010, REQ-PRIV-010 | ANSWERED | Drafted with a German legal-text generator (e.g. eRecht24), completed and approved by Nina before release; no GDPR article mapping in the issues. |
| Q-45 | Brand direction; existing brand assets | DQ-1, Existing brand assets (UNKNOWN), style-guide provisional logo | ANSWERED | No direction beyond 'quiet, image-first'; no existing brand assets. The placeholder mark stays provisional until Nina decides. |
| Q-46 | Header: mark, wordmark or both | DQ-2, style-guide provisional logo | ANSWERED | Header shows the wordmark 'Nina Laaf' only; the mark is used as favicon. |
| Q-47 | Custom typeface | DQ-3, style-guide provisional typeface | ANSWERED | Keep the system UI stack; no font files. |
| Q-48 | Dark mode | DQ-4 | ANSWERED | Light and dark mode, following the visitor's system setting (prefers-color-scheme). |
| Q-49 | Browsers counting as "current" | PQ-21, NFR-4 | ANSWERED | The last two major versions of Chrome, Edge, Firefox, Safari (macOS and iOS) and Samsung Internet. |
| Q-50 | Build/test commands; coverage target | CLAUDE.md Commands, issue Coverage Target | ANSWERED | Record Hugo basics now: build 'hugo --minify', run 'hugo server'; test command TO BE DECIDED in step 11 (deferred). |
| Q-4d | Previews of unpublished content | SEC-CICD-3, T-21, AQ-4 | ANSWERED | Editor-only previews wanted (SEC-CICD-3 applies). |
| Q-4e | Preview mechanism | SEC-CICD-3, SEC-SECRET-2 | ANSWERED | Protected preview site on the same server (e.g. preview.nina-laaf.de) behind a login, built from draft content with its own preview deploy key, never the production key. |
| Q-4f | Preview sign-in | SEC-CICD-3, SEC-AUTHN-2 | ANSWERED | HTTP Basic Auth per editor (individual nginx credentials, HTTPS only). |
| Q-11b | Keeping templates and pipeline outside editors' write access | DR-8, SEC-AUTHZ-3, T-11, PQ-4 | ANSWERED | Two repositories: a private content repository (Content Store; editors write through Sveltia) and a site repository (templates, Hugo config, design tokens, CI workflows; only the maintainer writes) that pulls content at build time. |
| Q-11c | Which repository holds what | DR-8, Q-11b | ANSWERED | dalini/nina-website stays the specification repository; site code (templates, Hugo config, CI) and content go to two new repositories. |
| Q-11d | Names of the new repositories | Q-11c | ANSWERED | dalini/nina-site (code; maintainer only) and dalini/nina-content (private Content Store). |
| Q-15b | Home page when no exhibition is current | FR-2.1, Q-15 | ANSWERED | Current exhibition; if none, the next upcoming; if none, the latest past entry. |
| Q-16b | How news fits FR-4.2 | PQ-11, FR-4.2 | ANSWERED | News is a separate content object, listed apart from exhibitions; the FR-4.2 exhibition types stay solo, two-person, group, fair. |
| Q-16c | News fields and listing | Q-16b | ANSWERED | News: title and date required; text, external link and image optional; own section on the Exhibitions/News page, newest first. |
| Q-18b | Required fields of other items; legal pages | PQ-13 | ANSWERED | Required: Text title + body; CV entry section + year + text; Download title + PDF; News title + date. The build fails if the Contact email, the Impressum or the Datenschutzerklärung is missing. |
| Q-19b | What Nina can edit in derived CV sections | Q-19, FR-5.2 | ANSWERED | All three: hide a derived line (flag on the Exhibition entry or work); add CV-only lines, merged by year; override the generated wording with a CV text field on the Exhibition entry. |
| Q-19c | Source of the short bio | PQ-23, FR-5.2 | ANSWERED | The short bio is a separate CV entry in section 'Bio'. |
| Q-22b | English values of migrated works | PQ-15, FR-1.4 | ANSWERED | Import German values only; English fields stay empty and English pages show German through the Q-14 fallback until filled in. |
| Q-22c | Alt text of migrated images | PQ-15, FR-3.7, FR-10.6 | ANSWERED | Interim alt text 'title, view n' (e.g. 'tiptoe, installation view 1') is accepted as valid alt text and improved later. |
| Q-26b | Credential that stores the refreshed Instagram token | SQ-3, SQ-4, T-37 | ANSWERED | The refresh runs as a monthly cron job on the VPS; the token lives in a root-only file there; the CI build reads it over the deploy SSH channel. |
| Q-24b | Where the last fetched posts are kept | FR-8.5, DR-1 | ANSWERED | On the server: when the fetch fails, the build re-uses the feed files already deployed on the VPS, pulled over the deploy channel. |
| Q-48b | Dark-mode colors | DQ-4, Q-48 | ANSWERED | Derive a dark token set now (same roles, each text pair checked against WCAG 2.2 AA) in DESIGN.md and style-guide.html; Ives reviews the diff. |
| Q-50b | Test coverage target | Issue Coverage Target | ANSWERED | 80% line coverage of first-party code, plus the per-issue rule (every AC tested; positive and negative tests for security decisions and error paths). |
| Q-51 | Order of the Texts page | PQ-19, Q-21 | ANSWERED | Texts get an optional year; Nina can reorder manually (supersedes 'newest first' in Q-21 for Texts). |
| Q-52 | Year of the Bio CV entry | Q-18b, Q-19c | ANSWERED | Year is required for every CV section except 'Bio'; the Bio entry is shown first. |
| Q-53 | Number of featured works | FR-2.1, FR-10.3, Q-15 | ANSWERED | Exactly one work is featured: marking a work featured removes the mark from the previous one; the build fails if no published work is featured. |
| Q-54 | Exhibition order within groups | FR-4.1, Q-16 | ANSWERED | Upcoming and current: earliest start first; past: most recent end date first. |
| Q-55 | Legacy works without a year | Q-22, FR-10.6, FR-11.1 | ANSWERED | The 12 legacy works without a year are imported unpublished until a year is added; the other 41 are published (refines Q-22). |
| Q-56 | Legacy featured flag | Q-53, FR-2.1 | ANSWERED | The legacy featured flag is ignored; the newest migrated work is featured at launch until Nina changes it. |
| Q-57 | Exhibition types → CV sections | Q-19, FR-5.2 | ANSWERED | solo and two-person → Solo exhibitions; group and fair → Group exhibitions. |
| Q-58 | Which derived entries appear in the CV | Q-19, FR-5.2 | ANSWERED | Past and current Exhibition entries appear in the CV; upcoming ones appear once they open. |
| Q-2b | Publish trigger | PQ-1, SEC-CICD-4, T-22 | ANSWERED | Changed by Ives after step 10 to avoid Actions cost (a 5-minute Actions poll would use about 8,600 of the 2,000 free minutes per month): a cron job on the VPS checks nina-content main every minute with git ls-remote and starts the nina-site build via workflow_dispatch, using a token limited to Actions: write on nina-site, root-only on the VPS. Builds run only on change, plus nightly. |
| Q-2c | Publish status shown to Nina | PQ-1, FR-10.8, DF-9 | ANSWERED | Each build writes a small status JSON (content version, success or failure, failing item and field) on the server; the self-hosted CMS page shows it as a banner. |
| Q-2d | CMS hostname | DR-9 | ANSWERED | CMS and GitHub OAuth helper on edit.nina-laaf.de; previews on preview.nina-laaf.de. |
| Q-10b | How owners manage editors | SEC-AUTHZ-2, SEC-AUDIT-1, Q-10 | ANSWERED | Only Ives is GitHub admin of dalini/nina-content: Nina stays an owner by decision, Ives makes editor changes on GitHub; only Ives can bypass branch protection. |
| Q-10c | Record of editor changes | SEC-AUTHZ-2 | ANSWERED | Every editor add/remove is also committed as a line in EDITORS.md in dalini/nina-content. |
| Q-11e | CMS token scope for the maintainer | SEC-AUTHZ-3, T-11 | ANSWERED | Accepted risk: Ives's CMS OAuth token can also write dalini/nina-site; documented as accepted for his account (2FA required). |
