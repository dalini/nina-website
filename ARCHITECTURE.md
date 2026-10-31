# ARCHITECTURE

`ARCHITECTURE.md` owns HOW nina-laaf.de is built: components, interfaces, data flow, trust boundaries, technology choices and rationale. Behavior: `REQUIREMENTS.md`. Design: `DESIGN.md`. Security controls: `SECURITY.md`. Decisions from step 10 and step 10 again: `.planning/OPEN_QUESTIONS.md`.

## Required Architecture Inputs

- Requirements source: REQUIREMENTS.md
- Design source: DESIGN.md
- System purpose: See REQUIREMENTS.md
- Primary use cases: See REQUIREMENTS.md
- Target users / actors: See REQUIREMENTS.md
- Runtime environment: web application: statically rendered public site plus a web-based editing interface, both served by nginx from one rented VPS in Germany
- Server framework: none for the public site; pages are pre-built by the static site generator Hugo
- Client framework: none. Small first-party vanilla JavaScript is allowed where a page needs it: the image viewer on work pages (FR-3.11) and the consent banner with the Consent Mode defaults (FR-9.2); `SECURITY.md` SEC-OUT-5 applies. The only third-party script is the Google Analytics tag on the public site (`SECURITY.md` SEC-PRIV-5)
- API style and integration model: the Editing Interface (Sveltia CMS) commits content and media to the Content Store through the GitHub API, using the signed-in editor's own GitHub OAuth token. A cron job on the VPS detects new commits on the Content Store and starts the Site Build through the GitHub API (`workflow_dispatch`); the Site Build reports a publish status file (DF-9). Instagram posts are fetched by the Site Build through the Instagram API with Instagram Login
- Authentication and session model: See SECURITY.md (SEC-AUTHN-1 to SEC-AUTHN-5)
- Data model expectations: content items (Work, Image, Exhibition entry, News item, Text, CV entry, Download, Page, plus the Instagram settings: hidden posts and feed on/off) stored as Markdown files with YAML front matter, media files next to them, in a Git repository; each text field holds DE and EN values (FR-1.4)
- Deployment model: GitHub Actions in `dalini/nina-site` build with Hugo and deploy only the generated output to the VPS over rsync/SSH with a restricted deploy key; nginx serves it
- Scale expectations: small: 1–3 editors, up to a few hundred works, low thousands of visits per month with occasional peaks (exhibition openings, press)
- Security expectations: See SECURITY.md

## Repositories

- `dalini/nina-website` — the specification (this repository), plus operational runbooks and review records in `docs/ops/` (no secrets, credentials or personal data).
- `dalini/nina-content` — private; the Content Store. Editors write to it through the Editing Interface.
- `dalini/nina-site` — templates, Hugo configuration, design tokens, Editing Interface configuration, the OAuth helper (Deno) and CI workflows. Only the maintainer (Ives) writes to it (DR-8). Tests run with `deno task test`.

## Architecture

Nina edits in the **Editing Interface**, which writes to the **Content Store**; the **Site Build** turns the store into static files; **Static Hosting** serves them to the **Public Site** in the visitor's browser. Flow and enforcement rules: Dependency Rules below.

- **Editing Interface**
  - **Technology:** Sveltia CMS, self-hosted at `edit.nina-laaf.de` together with its GitHub OAuth helper (DR-9); configuration kept in `dalini/nina-site`. The CMS bundle and the helper are served from the VPS at pinned releases, never loaded from a CDN (`SECURITY.md` SEC-CICD-2). The helper is a Deno port of the Sveltia CMS Authenticator (written upstream for Cloudflare Workers), kept in `dalini/nina-site` and maintained by Ives, run on the VPS under its own account with a pinned Deno version (`SECURITY.md` SEC-AUTHN-4). The CMS UI is used as is, without restyling.
  - **Responsibility:** Lets editors create, edit, unpublish and delete content and upload images/PDFs without technical skills (FR-10.1–10.3); validates required fields before saving (FR-10.6); shows change history and restore through the Git history (FR-10.7). Where Sveltia CMS cannot enforce a check (actual file type, pixel size, a single featured work) or offers no history view or restore, the Site Build enforces the check by failing the publish, and Ives restores from the Git history on request (FR-10.3, FR-10.6, FR-10.7); shows the publish status banner (FR-10.8); manages the Instagram settings (FR-8.6).
  - **Inputs:** Editor actions and uploads; editor identity (GitHub sign-in); the publish status file written by the Site Build (DF-9).
  - **Outputs:** Commits of content and media to `main` of the Content Store. A save on `main` is the publish trigger (DF-4).
  - **Data owned or accessed:** Reads and writes all content items and media in the Content Store, within DR-8; owns none itself.

- **Content Store**
  - **Technology:** private GitHub repository `dalini/nina-content`; content as Markdown with YAML front matter; image originals and PDFs committed directly (no Git LFS). The content model and its rules are documented in `dalini/nina-site` (DR-3, DR-4).
  - **Responsibility:** Single source of truth for all content and media, with change history (Git).
  - **Inputs:** Commits from the Editing Interface; one-time import of the legacy works (FR-11.1–11.4).
  - **Outputs:** Content and media read by the Site Build.
  - **Data owned or accessed:** Owns Work, Image, Exhibition entry, News item, Text, CV entry, Download, Page, featured and public-space flags, manual orders (works, texts, downloads), CV overrides, image order, alt text, credits and captions, Exhibition descriptions and installation images, Instagram settings (hidden posts by link, feed on/off).
  - **Backup:** weekly mirror to the VPS (`SECURITY.md` SEC-AVAIL-1).
  - **Capacity note:** originals are committed directly, so GitHub's repository size limits apply (`SECURITY.md` T-17).

- **Site Build**
  - **Technology:** Hugo, run by GitHub Actions in `dalini/nina-site`, with the Hugo version pinned (`SECURITY.md` SEC-CICD-2).
  - **Responsibility:** Generates the complete static site in DE (`/de/`) and EN (`/`) from the Content Store: all pages, language switch targets (FR-1.3), upcoming/current/past grouping in Europe/Berlin (FR-4.1), the home page entry (FR-2.3), derived CV sections (FR-5.3), a page per Exhibition entry (FR-4.7), responsive image sizes from the originals with a 2400 px maximum (NFR-1, FR-3.10), metadata stripping from images and PDFs (`SECURITY.md` SEC-PRIV-4), the Instagram posts (FR-8.2) and only published content. Rechecks content rules (DR-3); on invalid content it fails the publish (`SECURITY.md` SEC-INPUT-3). Every build job has a 30-minute time limit (`SECURITY.md` SEC-CICD-8).
  - **When it runs:** only when content changed, plus nightly. A cron job on the VPS checks `main` of the Content Store every minute with `git ls-remote` and a read-only credential (no GitHub Actions minutes) and, when the commit differs from the last one it started a build for, starts the build workflow in `dalini/nina-site` through `workflow_dispatch`; this meets the 15-minute limit of FR-10.4. A nightly scheduled build (Europe/Berlin) keeps FR-4.1 and FR-2.3 correct (FR-10.4). The workflow uses one concurrency group, so a new run replaces a queued one. Cost: Actions minutes are used only by real builds, well inside the Actions minutes included in the account's paid plan (GitHub Pro or Team, `SECURITY.md` SEC-SECRET-2).
  - **Stages:** checking out and processing the Content Store (Hugo, image and PDF processing, content checks) runs without the deploy keys and the Instagram token. Only the step that downloads Instagram posts and the step that deploys the finished output receive them (`SECURITY.md` SEC-CICD-6, T-54).
  - **Production and preview:** the production build of `main` publishes only published content to `nina-laaf.de`. A preview build of the same content, including unpublished items, is deployed to `preview.nina-laaf.de` with a separate preview deploy key (`SECURITY.md` SEC-CICD-3, SEC-SECRET-2).
  - **Inputs:** Content Store (read-only credential); templates and design tokens (`DESIGN.md`, `style-guide.html`) from `dalini/nina-site`, outside editors' write access (DR-8); build date; Instagram posts (Instagram Feed Integration).
  - **Outputs:** Static files (HTML, CSS, JavaScript, images, PDFs) deployed to Static Hosting; a publish status file on `edit.nina-laaf.de` (content version, success or failure, failing item and field; public, `SECURITY.md` SEC-CICD-9) that the Editing Interface shows (FR-10.8); a GitHub Actions failure email to the maintainer (FR-10.8).
  - **Data owned or accessed:** Reads all content; owns generated output only.

- **Static Hosting**
  - **Technology:** a rented VPS (root server) in Germany running nginx, administered by Ives; serves `nina-laaf.de`, `preview.nina-laaf.de` and `edit.nina-laaf.de`. Registrar, DNS and VPS accounts are held by Ives (`SECURITY.md` SEC-HOST-1).
  - **Responsibility:** Serves the generated files over HTTPS with the headers of `SECURITY.md` SEC-HTTP-1 to SEC-HTTP-4; limits requests and connections per IP (20 requests/s with a burst of 40, and 20 connections, per IP; T-31); protects `preview.nina-laaf.de` with per-editor HTTP Basic Auth (5 failed sign-ins per IP per minute, then HTTP 429); keeps access logs for abuse handling as in `SECURITY.md` SEC-PRIV-6 (no statistics are made from them); holds the weekly Content Store backup; runs the content check that starts builds (Site Build, When it runs); runs the monthly Instagram token refresh (under the deploy account); renews the Let's Encrypt certificates and emails Ives about failed renewals, refreshes and content checks (SEC-HTTP-5); installs security updates daily; runs the Editing Interface's OAuth helper. Server hardening and process accounts: `SECURITY.md` SEC-HOST-2; each secret file belongs to the one account that uses it (SEC-SECRET-1).
  - **Inputs:** Output of the Site Build (rsync over SSH).
  - **Outputs:** Responses to visitor browsers.
  - **Data owned or accessed:** Generated files; access logs; content backup; Instagram token, build-trigger token, the content check's and backup's read credentials, and the OAuth helper's client secret (`SECURITY.md` SEC-SECRET-1).

- **Public Site (visitor's browser)**
  - **Responsibility:** Displays pages, the language switch and images; works without login (NFR-2); shows the consent banner (FR-9.2) and the image viewer (FR-3.11); loads nothing from third parties except the Google Analytics tag with Consent Mode (`SECURITY.md` SEC-PRIV-1, SEC-PRIV-5).
  - **Inputs:** Static files from Static Hosting; visitor actions.
  - **Outputs:** Rendered pages; Google Analytics measurements to Google (cookieless before consent).
  - **Data owned or accessed:** The visitor's consent choice in browser storage; Google Analytics cookies after consent.

- **Instagram Feed Integration**
  - **Responsibility:** Provides the recent posts of @ninalaaf (FR-8.2) as part of the static output (model (b), build time).
  - **Method:** the Site Build calls the Instagram API with Instagram Login (scope `instagram_business_basic` only; @ninalaaf must be a Business or Creator account), downloads post images into the output, and applies the hidden-post list and feed switch from the Content Store (FR-8.6). The 60-day long-lived token lives in a file on the VPS owned by the deploy account, is refreshed monthly by a cron job running under that account, and is read by the build over the deploy SSH channel; a failed refresh is emailed to Ives. Post images get alt text from the caption (FR-8.7); Nina hides a post by its link (FR-8.6). If a fetch fails, the build reuses the feed files already deployed on the VPS (FR-8.5).
  - **Data owned or accessed:** Recent post images, captions, links, dates.

## Requirement Traceability

| Component / boundary | Requirement groups | Status |
|---|---|---|
| Editing Interface | FR-8.6, FR-10.1–10.9 | DEFINED (Sveltia CMS, GitHub sign-in) |
| Content Store | FR-1.4, FR-3.2–3.8, FR-4.2–4.7, FR-5, FR-6, FR-7.1–7.2, FR-8.6, FR-10.3, FR-10.7, FR-11 | SUPPORTED |
| Site Build | FR-1.1–1.7, FR-2, FR-3.1, FR-3.9, FR-3.10, FR-4.1, FR-4.4, FR-4.7, FR-5, FR-6.2, FR-8.2, FR-8.5, FR-8.7, FR-9.1, FR-9.2, FR-10.4, FR-10.6, FR-10.8, FR-10.9, FR-11, NFR-1 | SUPPORTED |
| Static Hosting | FR-10.9, NFR-1, NFR-4 | DEFINED (VPS with nginx) |
| Public Site | FR-1.3, FR-3.11, FR-7.1, FR-8.1, FR-8.4, FR-9.2, NFR-2, NFR-3 | SUPPORTED |
| Instagram Feed Integration | FR-8.2, FR-8.3, FR-8.5, FR-8.6 | DEFINED (build time) |
| — | FR-7.3 (contact form, not in this release) | NOT IN THIS RELEASE |

## Dependency Rules

- **DR-1** Content flows one way: Editing Interface → Content Store → Site Build → Static Hosting → Public Site. No component writes back upstream, and the Public Site never reaches the Editing Interface or Content Store. The Editing Interface may read the Site Build's publish status file (FR-10.8); the status carries no content and grants no write access (`SECURITY.md` T-24). The Site Build may read its own earlier output from Static Hosting to reuse the last Instagram posts (FR-8.5).
- **DR-2** The Content Store is the only owner of content and media. Templates and the Public Site MUST NOT hold content that editors are expected to change (FR-10.1).
- **DR-3** Content rules (required fields, publish state, language fields) are defined once for the content model and enforced by the Editing Interface and rechecked by the Site Build, never in the visitor's browser; templates only render. The Site Build also rechecks the upload rules for every file it publishes (`SECURITY.md` SEC-INPUT-1, SEC-INPUT-2, T-49).
- **DR-4** The content model MUST NOT depend on a specific editing system or generator: content is stored in a documented, portable format (Markdown with YAML front matter) so either can be replaced.
- **DR-5** Instagram-specific data and access stay inside the Instagram Feed Integration; other components consume only a neutral list of posts (image, caption, link, date).
- **DR-6** Third-party resources in the Public Site: `SECURITY.md` SEC-PRIV-1, SEC-PRIV-3 and SEC-PRIV-5 (Google Analytics only).
- **DR-7** Components interact only through the interfaces above, with no circular dependencies.
- **DR-8** Editors and the Editing Interface write only content and media in the Content Store (`dalini/nina-content`). Templates, design tokens, build configuration and pipeline definitions live in `dalini/nina-site`, where only the maintainer writes. Enforcement: `SECURITY.md` SEC-AUTHZ-3 (T-11).
- **DR-9** The Editing Interface is served from its own origin (`edit.nina-laaf.de`), not from the Public Site's origin, so script running in public pages cannot act with an editor's session (`SECURITY.md` T-33).

## Open Questions

None. AQ-1 to AQ-5 were resolved in step 10, and the security questions that could change the architecture (SQ-17, SQ-18, SQ-20, SQ-21) in step 10 again (`.planning/OPEN_QUESTIONS.md`).
