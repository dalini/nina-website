# ARCHITECTURE

`ARCHITECTURE.md` owns HOW nina-laaf.de is built: components, interfaces, data flow, trust boundaries, technology choices and rationale. Behavior: `REQUIREMENTS.md`. Design: `DESIGN.md`. Security controls: `SECURITY.md`. Decisions from step 10: `.planning/OPEN_QUESTIONS.md`.

## Required Architecture Inputs

- Requirements source: REQUIREMENTS.md
- Design source: DESIGN.md
- System purpose: See REQUIREMENTS.md
- Primary use cases: See REQUIREMENTS.md
- Target users / actors: See REQUIREMENTS.md
- Runtime environment: web application: statically rendered public site plus a web-based editing interface, both served by nginx from one rented VPS in Germany
- Server framework: none for the public site; pages are pre-built by the static site generator Hugo
- Client framework: none. Small first-party vanilla JavaScript is allowed where a page needs it (e.g. an image viewer on work pages); `SECURITY.md` SEC-OUT-5 applies
- API style and integration model: the Editing Interface (Sveltia CMS) commits content and media to the Content Store through the GitHub API, using the signed-in editor's own GitHub OAuth token. A cron job on the VPS detects new commits on the Content Store and starts the Site Build through the GitHub API (`workflow_dispatch`); the Site Build reports a publish status file (DF-9). Instagram posts are fetched by the Site Build through the Instagram API with Instagram Login
- Authentication and session model: See SECURITY.md (SEC-AUTHN-1 to SEC-AUTHN-3)
- Data model expectations: content items (Work, Image, Exhibition entry, News item, Text, CV entry, Download, Page, plus the Instagram settings: hidden posts and feed on/off) stored as Markdown files with YAML front matter, media files next to them, in a Git repository; each text field holds DE and EN values (FR-1.4)
- Deployment model: GitHub Actions in `dalini/nina-site` build with Hugo and deploy only the generated output to the VPS over rsync/SSH with a restricted deploy key; nginx serves it
- Scale expectations: small: 1–3 editors, up to a few hundred works, low thousands of visits per month with occasional peaks (exhibition openings, press)
- Security expectations: See SECURITY.md

## Repositories

- `dalini/nina-website` — the specification (this repository).
- `dalini/nina-content` — private; the Content Store. Editors write to it through the Editing Interface.
- `dalini/nina-site` — templates, Hugo configuration, design tokens, Editing Interface configuration and CI workflows. Only the maintainer (Ives) writes to it (DR-8).

## Architecture

Nina edits in the **Editing Interface**, which writes to the **Content Store**; the **Site Build** turns the store into static files; **Static Hosting** serves them to the **Public Site** in the visitor's browser. Flow and enforcement rules: Dependency Rules below.

- **Editing Interface**
  - **Technology:** Sveltia CMS, self-hosted at `edit.nina-laaf.de` together with its GitHub OAuth helper (DR-9); configuration kept in `dalini/nina-site`.
  - **Responsibility:** Lets editors create, edit, unpublish and delete content and upload images/PDFs without technical skills (FR-10.1–10.3); validates required fields before saving (FR-10.6); shows change history and restore through the Git history (FR-10.7); shows the publish status banner (FR-10.8); manages the Instagram settings (FR-8.6).
  - **Inputs:** Editor actions and uploads; editor identity (GitHub sign-in); the publish status file written by the Site Build (DF-9).
  - **Outputs:** Commits of content and media to `main` of the Content Store. A save on `main` is the publish trigger (DF-4).
  - **Data owned or accessed:** Reads and writes all content items and media in the Content Store, within DR-8; owns none itself.

- **Content Store**
  - **Technology:** private GitHub repository `dalini/nina-content`; content as Markdown with YAML front matter; image originals and PDFs committed directly (no Git LFS). The content model and its rules are documented in `dalini/nina-site` (DR-3, DR-4).
  - **Responsibility:** Single source of truth for all content and media, with change history (Git).
  - **Inputs:** Commits from the Editing Interface; one-time import of the legacy works (FR-11.1–11.4).
  - **Outputs:** Content and media read by the Site Build.
  - **Data owned or accessed:** Owns Work, Image, Exhibition entry, News item, Text, CV entry, Download, Page, featured and public-space flags, manual orders, CV overrides, image order, alt text and credits, Instagram settings.
  - **Backup:** weekly mirror to the VPS (`SECURITY.md` SEC-AVAIL-1).
  - **Capacity note:** originals are committed directly, so GitHub's repository size limits apply (`SECURITY.md` T-17).

- **Site Build**
  - **Technology:** Hugo, run by GitHub Actions in `dalini/nina-site`, with the Hugo version pinned (`SECURITY.md` SEC-CICD-2).
  - **Responsibility:** Generates the complete static site in DE (`/de/`) and EN (`/`) from the Content Store: all pages, language switch targets (FR-1.3), upcoming/current/past grouping in Europe/Berlin (FR-4.1), the home page entry (FR-2.3), derived CV sections (FR-5.3), responsive image sizes from the originals with a 2400 px maximum (NFR-1, FR-3.10), metadata stripping (`SECURITY.md` SEC-PRIV-4), the Instagram posts (FR-8.2) and only published content. Rechecks content rules (DR-3); on invalid content it fails the publish (`SECURITY.md` SEC-INPUT-3).
  - **When it runs:** only when content changed, plus nightly. A cron job on the VPS checks `main` of the Content Store every minute with `git ls-remote` and a read-only credential (no GitHub Actions minutes) and, when the commit differs from the last one it started a build for, starts the build workflow in `dalini/nina-site` through `workflow_dispatch`; this meets the 15-minute limit of FR-10.4. A nightly scheduled build (Europe/Berlin) keeps FR-4.1 and FR-2.3 correct (FR-10.4). The workflow uses one concurrency group, so a new run replaces a queued one. Cost: Actions minutes are used only by real builds, well inside the 2,000 included minutes per month of a free private repository.
  - **Production and preview:** the production build of `main` publishes only published content to `nina-laaf.de`. A preview build of the same content, including unpublished items, is deployed to `preview.nina-laaf.de` with a separate preview deploy key (`SECURITY.md` SEC-CICD-3, SEC-SECRET-2).
  - **Inputs:** Content Store (read-only credential); templates and design tokens (`DESIGN.md`, `style-guide.html`) from `dalini/nina-site`, outside editors' write access (DR-8); build date; Instagram posts (Instagram Feed Integration).
  - **Outputs:** Static files (HTML, CSS, JavaScript, images, PDFs) deployed to Static Hosting; a publish status file on `edit.nina-laaf.de` (content version, success or failure, failing item and field) that the Editing Interface shows (FR-10.8); a GitHub Actions failure email to the maintainer (FR-10.8).
  - **Data owned or accessed:** Reads all content; owns generated output only.

- **Static Hosting**
  - **Technology:** a rented VPS (root server) in Germany running nginx, administered by Ives; serves `nina-laaf.de`, `preview.nina-laaf.de` and `edit.nina-laaf.de`. Registrar, DNS and VPS accounts are held by Ives (`SECURITY.md` SEC-HOST-1).
  - **Responsibility:** Serves the generated files over HTTPS with the headers of `SECURITY.md` SEC-HTTP-1 to SEC-HTTP-4; limits requests and connections per IP (T-31); protects `preview.nina-laaf.de` with per-editor HTTP Basic Auth; keeps access logs as in `SECURITY.md` SEC-PRIV-6; produces cookieless statistics from those logs (SEC-PRIV-5); holds the weekly Content Store backup; runs the content check that starts builds (Site Build, When it runs); runs the monthly Instagram token refresh.
  - **Inputs:** Output of the Site Build (rsync over SSH).
  - **Outputs:** Responses to visitor browsers.
  - **Data owned or accessed:** Generated files; access logs; content backup; Instagram token.

- **Public Site (visitor's browser)**
  - **Responsibility:** Displays pages, the language switch and images; works without login (NFR-2); loads nothing from third parties (FR-8.2, `SECURITY.md` SEC-PRIV-1).
  - **Inputs:** Static files from Static Hosting; visitor actions.
  - **Outputs:** Rendered pages.
  - **Data owned or accessed:** None persistent.

- **Instagram Feed Integration**
  - **Responsibility:** Provides the recent posts of @ninalaaf (FR-8.2) as part of the static output (model (b), build time).
  - **Method:** the Site Build calls the Instagram API with Instagram Login (scope `instagram_business_basic` only; @ninalaaf must be a Business or Creator account), downloads post images into the output, and applies the hidden-post list and feed switch from the Content Store (FR-8.6). The 60-day long-lived token lives in a root-only file on the VPS, is refreshed monthly by a cron job there, and is read by the build over the deploy SSH channel; a failed refresh alerts Ives. If a fetch fails, the build reuses the feed files already deployed on the VPS (FR-8.5).
  - **Data owned or accessed:** Recent post images, captions, links, dates.

## Requirement Traceability

| Component / boundary | Requirement groups | Status |
|---|---|---|
| Editing Interface | FR-8.6, FR-10.1–10.9 | DEFINED (Sveltia CMS, GitHub sign-in) |
| Content Store | FR-1.4, FR-3.2–3.8, FR-4.2–4.6, FR-5, FR-6, FR-7.1–7.2, FR-8.6, FR-10.3, FR-10.7, FR-11 | SUPPORTED |
| Site Build | FR-1.1–1.7, FR-2, FR-3.1, FR-3.9, FR-3.10, FR-4.1, FR-4.4, FR-5, FR-6.2, FR-8.2, FR-8.5, FR-9.1, FR-10.4, FR-10.6, FR-10.8, FR-10.9, FR-11, NFR-1 | SUPPORTED |
| Static Hosting | FR-10.9, NFR-1, NFR-4 | DEFINED (VPS with nginx) |
| Public Site | FR-1.3, FR-7.1, FR-8.1, FR-8.4, NFR-2, NFR-3 | SUPPORTED |
| Instagram Feed Integration | FR-8.2, FR-8.3, FR-8.5, FR-8.6 | DEFINED (build time) |
| — | FR-7.3 (contact form, not in this release) | NOT IN THIS RELEASE |

## Dependency Rules

- **DR-1** Content flows one way: Editing Interface → Content Store → Site Build → Static Hosting → Public Site. No component writes back upstream, and the Public Site never reaches the Editing Interface or Content Store. The Editing Interface may read the Site Build's publish status file (FR-10.8); the status carries no content and grants no write access (`SECURITY.md` T-24). The Site Build may read its own earlier output from Static Hosting to reuse the last Instagram posts (FR-8.5).
- **DR-2** The Content Store is the only owner of content and media. Templates and the Public Site MUST NOT hold content that editors are expected to change (FR-10.1).
- **DR-3** Content rules (required fields, publish state, language fields) are defined once for the content model and enforced by the Editing Interface and rechecked by the Site Build, never in the visitor's browser; templates only render.
- **DR-4** The content model MUST NOT depend on a specific editing system or generator: content is stored in a documented, portable format (Markdown with YAML front matter) so either can be replaced.
- **DR-5** Instagram-specific data and access stay inside the Instagram Feed Integration; other components consume only a neutral list of posts (image, caption, link, date).
- **DR-6** Third-party resources in the Public Site: `SECURITY.md` SEC-PRIV-1 and SEC-PRIV-3.
- **DR-7** Components interact only through the interfaces above, with no circular dependencies.
- **DR-8** Editors and the Editing Interface write only content and media in the Content Store (`dalini/nina-content`). Templates, design tokens, build configuration and pipeline definitions live in `dalini/nina-site`, where only the maintainer writes. Enforcement: `SECURITY.md` SEC-AUTHZ-3 (T-11).
- **DR-9** The Editing Interface is served from its own origin (`edit.nina-laaf.de`), not from the Public Site's origin, so script running in public pages cannot act with an editor's session (`SECURITY.md` T-33).

## Open Questions

None. AQ-1 to AQ-5 were resolved in step 10 (`.planning/OPEN_QUESTIONS.md`).
