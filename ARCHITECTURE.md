# ARCHITECTURE

`ARCHITECTURE.md` owns HOW nina-laaf.de is built: components, interfaces, data flow, trust boundaries, technology choices and rationale. Behavior: `REQUIREMENTS.md`. Design: `DESIGN.md`. Security controls: `SECURITY.md`.

## Required Architecture Inputs

- Requirements source: REQUIREMENTS.md
- Design source: DESIGN.md
- System purpose: See REQUIREMENTS.md
- Primary use cases: See REQUIREMENTS.md
- Target users / actors: See REQUIREMENTS.md
- Runtime environment: web application: statically rendered public site plus a web-based editing interface (`PROJECT_NOTES.md` §2)
- Server framework: none for the public site; pages are pre-built by a static site generator. Generator: TO BE DECIDED (Hugo is a candidate; AQ-1)
- Client framework: TO BE DECIDED (none required by any requirement; AQ-2)
- API style and integration model: TO BE DECIDED (editing interface ↔ Content Store; Instagram feed, see OQ-6)
- Authentication and session model: TO BE DECIDED (editing interface only; public site has no login)
- Data model expectations: content items (Work, Image, Exhibition/News entry, Text, CV entry, Download, Page) stored as versioned files with media assets; each text field holds DE and EN values (FR-1.4). Storage format TO BE DECIDED.
- Deployment model: TO BE DECIDED (AQ-4)
- Scale expectations: UNKNOWN (single editor; public read-only traffic)
- Security expectations: TO BE DECIDED — addressed in a later security specification, SECURITY.md

## Initial Architecture (Provisional)

Content flows one way: Nina edits in the **Editing Interface**, which writes to the **Content Store**; the **Site Build** turns the store into static files; **Static Hosting** serves them to the **Public Site** in the visitor's browser. Visitors never reach the editing side. Business rules (validation of required fields, publish/unpublish, language fields) are enforced where content is written (Editing Interface, rechecked by Site Build), never in the visitor's browser.

- **Editing Interface**
  - **Responsibility:** Lets Nina and authorized editors create, edit, unpublish and delete content and upload images/PDFs without technical skills (FR-10.1–10.3); validates required fields before saving (FR-10.6); triggers publishing (FR-10.4).
  - **Inputs:** Editor actions and uploads; editor identity.
  - **Outputs:** Content changes and media written to the Content Store.
  - **Data owned or accessed:** Reads and writes all content items and media in the Content Store; owns none itself.
  - **Open decisions:** Which editing system (AQ-3); how editors authenticate (SQ in `SECURITY.md`); where it is hosted.

- **Content Store**
  - **Responsibility:** Single source of truth for all content and media, with change history.
  - **Inputs:** Writes from the Editing Interface; one-time import of selected legacy works (FR-11.1).
  - **Outputs:** Content and media read by the Site Build.
  - **Data owned or accessed:** Owns Work, Image, Exhibition/News entry, Text, CV entry, Download, Page, featured and public-space flags, image order, alt text and credits.
  - **Open decisions:** Storage form and where large originals (~4 MB) are kept (ASSUMPTION: a version-controlled repository with large-file storage, as in the earlier attempt).

- **Site Build**
  - **Responsibility:** Generates the complete static site in DE and EN from the Content Store: all pages, language switch targets (FR-1.3), upcoming/current/past grouping of exhibitions (FR-4.1), responsive image sizes from originals (NFR-1), and only published content (FR-11.2). Fails the publish when content is invalid instead of producing broken pages.
  - **Inputs:** Content Store; templates and design tokens (`DESIGN.md`, `style-guide.html`); build date.
  - **Outputs:** Static files (HTML, CSS, images, PDFs) delivered to Static Hosting.
  - **Data owned or accessed:** Reads all content; owns generated output only.
  - **Open decisions:** Generator (AQ-1); when builds run (on every publish, and on a schedule so "upcoming/current/past" stays correct; ASSUMPTION); build environment.

- **Static Hosting**
  - **Responsibility:** Serves the generated files for `nina-laaf.de` over HTTPS.
  - **Inputs:** Output of the Site Build.
  - **Outputs:** Responses to visitor browsers.
  - **Data owned or accessed:** Generated files only; no visitor data stored.
  - **Open decisions:** Provider and deployment (AQ-4).

- **Public Site (visitor's browser)**
  - **Responsibility:** Displays pages, the language switch and images; works without login (NFR-2); shows the Instagram consent placeholder and loads the feed only after consent if that model is chosen (FR-8.3, FR-8.4).
  - **Inputs:** Static files from Static Hosting; visitor actions.
  - **Outputs:** Rendered pages; on consent only, requests to Instagram.
  - **Data owned or accessed:** None persistent.
  - **Open decisions:** Whether any client-side code is needed (AQ-2).

- **Instagram Feed Integration**
  - **Responsibility:** Provides recent posts of @ninalaaf (FR-8.2) without third-party loading before consent (FR-8.3).
  - **Inputs/Outputs:** Either (a) the browser loads the feed from Instagram after consent, or (b) the Site Build fetches recent posts and copies them into the static output.
  - **Data owned or accessed:** Recent post images, captions, links.
  - **Open decisions:** Model (a) or (b) (`REQUIREMENTS.md` OQ-6); for (b), the Instagram access method and credentials.

## Requirement Traceability

| Component / boundary | Requirement groups | Status |
|---|---|---|
| Editing Interface | FR-10.1–10.6 | PARTIALLY DEFINED (system AQ-3, auth TO BE DECIDED) |
| Content Store | FR-1.4, FR-3.2–3.8, FR-4.2–4.5, FR-5, FR-6, FR-7.1–7.2, FR-10.3, FR-11.1 | SUPPORTED |
| Site Build | FR-1.1–1.7, FR-2, FR-3.1, FR-3.9, FR-4.1, FR-4.4, FR-5, FR-6.2, FR-9.1, FR-11.2, NFR-1 | SUPPORTED |
| Static Hosting | NFR-1, NFR-4 | PARTIALLY DEFINED (AQ-4) |
| Public Site | FR-1.3, FR-7.1, FR-8.1, FR-8.4, NFR-2, NFR-3 | SUPPORTED |
| Instagram Feed Integration | FR-8.2, FR-8.3 | TO BE DECIDED (OQ-6) |
| — | FR-7.3 (contact form, not in this release) | TO BE DECIDED |

## Dependency Rules

- **DR-1** Content flows one way: Editing Interface → Content Store → Site Build → Static Hosting → Public Site. No component writes back upstream, and the Public Site never reaches the Editing Interface or Content Store.
- **DR-2** The Content Store is the only owner of content and media. Templates and the Public Site MUST NOT hold content that editors are expected to change (FR-10.1).
- **DR-3** Content rules (required fields, publish state, language fields) are defined once for the content model and enforced by the Editing Interface and rechecked by the Site Build; templates only render.
- **DR-4** The content model MUST NOT depend on a specific editing system or generator: content is stored in a documented, portable format so either can be replaced.
- **DR-5** Instagram-specific data and access stay inside the Instagram Feed Integration; other components consume only a neutral list of posts (image, caption, link, date).
- **DR-6** The Public Site loads no third-party resources except through the consent-gated Instagram Feed Integration (NFR-3).
- **DR-7** Components interact only through the interfaces above, with no circular dependencies.

## Open Questions

- **AQ-1** Which static site generator: Hugo or another?
- **AQ-2** Does the Public Site need any client-side code beyond the consent-gated feed?
- **AQ-3** Which editing system gives Nina a non-technical, web-based interface that writes to the Content Store (for example a Git-based headless CMS)?
- **AQ-4** Where is the site hosted and deployed, and where is `nina-laaf.de` hosted today?
- **AQ-5** How are large image originals stored and how are responsive sizes produced (at build time or by the host)?
