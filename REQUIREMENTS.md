# REQUIREMENTS

`REQUIREMENTS.md` owns WHAT the nina-laaf.de website must do. Security and privacy requirements are authored in `SECURITY.md`. Derived from section 1 of `docs/PROJECT_NOTES.md` (archived); this file is now the source of truth.

## Required Requirement Inputs

- Project purpose: Public website for artist and sculptor Nina Laaf at `nina-laaf.de` that presents her work to curators, galleries, collectors, press and the public, and makes it easy to contact her.
- Primary users / actors: Visitor (public, anonymous: curators, galleries, collectors, press, students, the general public); Nina, the site owner (non-technical content editor); people Nina authorizes to edit.
- Core workflows: Visitor browses works, exhibitions/news, texts and CV, switches language, downloads PDFs, views the Instagram feed after consent, contacts Nina by email. Nina adds and updates works, exhibitions, news, texts, CV and downloads through an editing interface without technical skills.
- Business objects / data entities: Work, Image, Exhibition/News entry, Text, CV entry, Download (PDF), Page (Home, Contact, Impressum, Datenschutzerklärung), Instagram post.
- External integrations: Instagram (feed of @ninalaaf); galerie burster (link only).
- Authentication / roles: Visitors are anonymous; editors are Nina and people she authorizes. Authentication and authorization: `SECURITY.md`.
- Regulatory or privacy constraints: Operated from Germany; Impressum and Datenschutzerklärung required (FR-9.1). GDPR and consent rules: `SECURITY.md`.

## Functional Requirements

### Navigation and languages

- **FR-1.1** The site MUST offer navigation to Works, Exhibitions/News, Texts, CV and Contact on every page.
- **FR-1.2** Every page MUST be available in German and in English.
- **FR-1.3** Every page MUST offer a language switch that opens the same page in the other language.
- **FR-1.4** Every text content field (descriptions, texts, CV entries, news, captions, page content) MUST hold a German and an English version.
- **FR-1.5** Work titles MUST be shown as entered and MUST NOT be translated.
- **FR-1.6** When a visitor arrives without choosing a language, the site MUST show the default language: TO BE DECIDED (OQ-2).
- **FR-1.7** When the English or German version of a content field is empty, the page MUST still render without errors and without showing placeholder text. Fallback behavior: TO BE DECIDED (OQ-10).

### Home

- **FR-2.1** The home page MUST show the artist's name and featured work, as selected by Nina. Layout of the selection (one image or several): see OQ-3.
- **FR-2.2** Each featured work on the home page MUST link to its work page.

### Works

- **FR-3.1** The Works page MUST list all published works.
- **FR-3.2** Each work MUST have its own page showing title, year, materials and dimensions.
- **FR-3.3** A work page MUST show dimensions with either two or three values in centimeters, as entered.
- **FR-3.4** A work page MUST show an edition (e.g. "ed. 10") when one is entered, and show none when it is empty.
- **FR-3.5** A work page MUST show the series and a short text when they are entered, and omit them when empty.
- **FR-3.6** A work page MUST show all images of the work in the order Nina sets, including object views and installation views.
- **FR-3.7** Every image MUST have alt text; an image MUST show its photo credit when one is entered.
- **FR-3.8** Works marked as "in public space" MUST be identifiable on the Works page and MUST be viewable together as a group.
- **FR-3.9** A work page MUST link to the exhibitions it is linked to (FR-4.5).

### Exhibitions and News

- **FR-4.1** The Exhibitions/News page MUST list entries grouped as upcoming, current and past, based on each entry's dates and the current date.
- **FR-4.2** Each entry MUST show title, type (solo, two-person, group or fair), venue, city and dates.
- **FR-4.3** An entry MUST show co-exhibitors and an external link when they are entered, and omit them when empty.
- **FR-4.4** Past entries MUST remain visible as an archive.
- **FR-4.5** An entry MAY be linked to works; linked works MUST appear on the entry and link to their work pages.

### Texts and CV

- **FR-5.1** The Texts page MUST list all published essays and catalogue texts, each opening on its own page.
- **FR-5.2** The CV page MUST show a short bio, education, solo exhibitions, works in public space, group exhibitions, and awards and grants, each as its own section.

### Downloads

- **FR-6.1** The site MUST offer the portfolio PDFs that Nina publishes for download. Which PDFs: OQ-4.
- **FR-6.2** Each download MUST show its title and that it is a PDF before the visitor opens it.

### Contact

- **FR-7.1** The Contact page MUST show Nina's email address as a link that opens the visitor's email program.
- **FR-7.2** The Contact page MUST link to galerie burster. Whether the gallery is named as the contact for sales: OQ-5.
- **FR-7.3** The site MAY offer a contact form later; it is not part of this release.

### Instagram

- **FR-8.1** The site MUST link to Instagram account @ninalaaf.
- **FR-8.2** The site MUST show a feed of Nina's recent Instagram posts. How the feed is provided: OQ-6.
- **FR-8.3** Consent gating of Instagram/Meta content: `SECURITY.md` SEC-PRIV-1 to SEC-PRIV-3.
- **FR-8.4** If the feed cannot be shown (no consent, or Instagram unavailable), the page MUST still render and MUST show the link from FR-8.1.
- **FR-8.5** If the feed is copied into the site when it is built (OQ-6) and cannot be refreshed during a build, the build MUST still publish all other content, and the feed area follows FR-8.4. Whether the last copied posts or none are shown then: TO BE DECIDED. (Threat: `SECURITY.md` T-39.)

### Legal pages

- **FR-9.1** Every page MUST link to the Impressum and the Datenschutzerklärung.

### Content editing

- **FR-10.1** Nina MUST be able to create, edit, unpublish and delete works, images, exhibitions/news entries, texts, CV entries, downloads and page content without using Markdown, Git, HTML or a command line.
- **FR-10.2** Nina MUST be able to upload images and PDFs and set image order, alt text and photo credit.
- **FR-10.3** Nina MUST be able to mark works as featured (FR-2.1) and as in public space (FR-3.8).
- **FR-10.4** Changes Nina saves MUST appear on the public site after publishing; the maximum delay is TO BE DECIDED.
- **FR-10.5** Restricting content changes to Nina and the people she authorizes: `SECURITY.md` SEC-AUTHN-1, SEC-AUTHZ-1, SEC-AUTHZ-2.
- **FR-10.6** When Nina saves a work without a title, year or at least one image, or an image without alt text, the editor MUST reject the save and show which field is missing.
- **FR-10.7** Nina MUST be able to see, for each content item, who changed it and when, and MUST be able to restore an earlier version. Attribution rule: `SECURITY.md` SEC-AUDIT-1. (Threat: `SECURITY.md` T-9.)
- **FR-10.8** When a publish fails, the editing interface MUST show Nina that her changes are not live yet and, when content caused the failure, which item and field. Who else is notified: TO BE DECIDED. (Threat: `SECURITY.md` T-24.)

### Migration

- **FR-11.1** Every work Nina selects from the 53 legacy work series (`docs/legacy-content/works/`) MUST be published with its legacy title, year, materials, dimensions and images. Selection: OQ-1.
- **FR-11.2** Legacy works not selected MUST NOT be published.

## Non-Functional Requirements

- **NFR-1** Pages and images MUST be fast on mobile: images MUST be delivered in sizes suited to the visitor's screen rather than as the ~4 MB originals. Performance budget: TO BE DECIDED.
- **NFR-2** The public site MUST be usable without logging in and MUST NOT require visitor accounts.
- **NFR-3** No third-party content before visitor consent: `SECURITY.md` SEC-PRIV-1.
- **NFR-4** All pages MUST work on current desktop and mobile browsers.

## Open Questions

- **OQ-1** Which of the 53 legacy work series are migrated?
- **OQ-2** Which language is the default for visitors who have not chosen one?
- **OQ-3** Does the home page show one large featured image or a small selection?
- **OQ-4** Which portfolio PDFs are offered (old: *Portfolio Nina Laaf 2023*, *Außenskulpturen 2023*)?
- **OQ-5** Is galerie burster named as the contact for sales?
- **OQ-6** How is the Instagram feed provided: loaded after a click/consent, or recent posts copied into the site when it is built?
- **OQ-7** Is a newsletter part of the site? (The old site had one; it needs a mailing provider and double opt-in.)
- **OQ-8** Are analytics used: none, or cookieless only?
- **OQ-9** Are images protected (watermarks, limited download size)?
- **OQ-10** What does the visitor see when one language version of a content field is empty (FR-1.7)?
