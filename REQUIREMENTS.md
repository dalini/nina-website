# REQUIREMENTS

`REQUIREMENTS.md` owns WHAT the nina-laaf.de website must do. Security and privacy requirements are authored in `SECURITY.md`. Derived from section 1 of `docs/PROJECT_NOTES.md` (archived); this file is now the source of truth. Open questions were resolved in step 10 (record: `.planning/OPEN_QUESTIONS.md`).

## Required Requirement Inputs

- Project purpose: Public website for artist and sculptor Nina Laaf at `nina-laaf.de` that presents her work to curators, galleries, collectors, press and the public, and makes it easy to contact her.
- Primary users / actors: Visitor (public, anonymous: curators, galleries, collectors, press, students, the general public); editors Nina (site owner, non-technical) and Ives (also the maintainer, `SECURITY.md`); both may add or remove editors.
- Core workflows: Visitor browses works, exhibitions, news, texts and CV, switches language, downloads PDFs, sees Nina's recent Instagram posts on the home page, contacts Nina by email. Nina adds and updates works, exhibitions, news, texts, CV entries, downloads and page content through a web-based editing interface without technical skills.
- Business objects / data entities: Work, Image, Exhibition entry, News item, Text, CV entry, Download (PDF), Page (Home, Contact, Impressum, Datenschutzerklärung), Instagram post.
- External integrations: Instagram (recent posts of @ninalaaf, copied into the site when it is built); galerie burster (link only).
- Authentication / roles: Visitors are anonymous; editors are Nina and Ives. Authentication and authorization: `SECURITY.md`.
- Regulatory or privacy constraints: Operated from Germany; Impressum and Datenschutzerklärung required (FR-9.1). GDPR and privacy rules: `SECURITY.md`.

## Functional Requirements

### Navigation and languages

- **FR-1.1** The site MUST offer navigation to Works, Exhibitions/News, Texts, CV and Contact on every page.
- **FR-1.2** Every page MUST be available in German and in English.
- **FR-1.3** Every page MUST offer a language switch that opens the same page in the other language.
- **FR-1.4** Every text content field (descriptions, texts, CV entries, news, captions, page content) MUST hold a German and an English version.
- **FR-1.5** Work titles MUST be shown as entered and MUST NOT be translated.
- **FR-1.6** English is the default language: the site root (`/`) MUST show the English home page, and German pages MUST be served under `/de/`. The site MUST NOT redirect visitors based on their browser language.
- **FR-1.7** When the English or German version of a content field is empty, the page MUST render without errors and without placeholder text, and MUST show the version in the other language, marked with that language (`lang` attribute), without an extra note.

### Home

- **FR-2.1** The home page MUST show the artist's name and one large image of the featured work (FR-10.3).
- **FR-2.2** The featured work on the home page MUST link to its work page.
- **FR-2.3** The home page MUST show one Exhibition entry: the current one; if none is current, the next upcoming one; if none is upcoming, the most recent past one (FR-4.1). When several qualify, the order of FR-4.1 decides.
- **FR-2.4** The home page MUST show the Instagram posts of FR-8.2 below the featured image and the Exhibition entry.

### Works

- **FR-3.1** The Works page MUST list all published works, newest year first; Nina MUST be able to set a manual order that replaces it.
- **FR-3.2** Each work MUST have its own page showing its title and year. The year is a single four-digit year.
- **FR-3.3** A work page MUST show materials and dimensions when they are entered, and omit them when empty. Dimensions are shown with two or three values in centimeters, as entered.
- **FR-3.4** A work page MUST show an edition (e.g. "ed. 10") when one is entered, and show none when it is empty.
- **FR-3.5** A work page MUST show the series and a short text when they are entered, and omit them when empty.
- **FR-3.6** A work page MUST show all images of the work in the order Nina sets, including object views and installation views.
- **FR-3.7** Every image MUST have alt text; an image MUST show its photo credit when one is entered.
- **FR-3.8** Works marked as "in public space" MUST be identifiable on the Works page and MUST be viewable together as a group.
- **FR-3.9** A work page MUST link to the exhibitions it is linked to (FR-4.5).
- **FR-3.10** Published images MUST be at most 2400 px on their long side, and uploaded original files MUST NOT be published. The site MUST NOT add watermarks or block saving images.

### Exhibitions and News

- **FR-4.1** The Exhibitions/News page MUST list Exhibition entries grouped as upcoming, current and past. Every entry has a start date and an end date; both days count as part of the exhibition, and "today" is the current date in Europe/Berlin. An entry is upcoming when it starts after today, current when today is between its start and end date, and past when it ended before today. Upcoming and current entries are listed earliest start first; past entries most recent end date first.
- **FR-4.2** Each Exhibition entry MUST show title, type (solo, two-person, group or fair), venue, city and dates.
- **FR-4.3** An Exhibition entry MUST show co-exhibitors and an external link when they are entered, and omit them when empty.
- **FR-4.4** Past entries MUST remain visible as an archive.
- **FR-4.5** An Exhibition entry MAY be linked to works; linked works MUST appear on the entry and link to their work pages.
- **FR-4.6** News items MUST be listed in their own section of the Exhibitions/News page, newest date first. Each News item MUST show its title and date, and MUST show text, an external link and an image when they are entered.

### Texts and CV

- **FR-5.1** The Texts page MUST list all published essays and catalogue texts in the order Nina sets, each opening on its own page. A Text MAY have a year, shown when entered. Text bodies MAY contain images (FR-3.7 applies).
- **FR-5.2** The CV page MUST show these sections in this order: short bio, education, solo exhibitions, works in public space, group exhibitions, awards and grants. Sections without entries MUST be hidden. Within each section, entries are listed newest year first.
- **FR-5.3** The CV sections for exhibitions and works in public space MUST be filled from the content: past and current Exhibition entries of type solo or two-person appear under solo exhibitions, those of type group or fair under group exhibitions, and published works marked "in public space" under works in public space. Upcoming Exhibition entries appear once they open.
- **FR-5.4** Nina MUST be able to hide a derived line from the CV, replace its generated wording with her own text, and add CV-only lines to any section (e.g. older exhibitions not entered as Exhibition entries); CV-only lines are merged with derived lines by year. The short bio and the education and awards and grants sections are entered as CV entries.

### Downloads

- **FR-6.1** The CV page MUST offer the PDFs that Nina publishes for download; there is no fixed list.
- **FR-6.2** Each download MUST show its title and that it is a PDF before the visitor opens it.

### Contact

- **FR-7.1** The Contact page MUST show Nina's email address as a link that opens the visitor's email program.
- **FR-7.2** The Contact page MUST link to galerie burster as her gallery; the gallery is not named as the contact for sales.
- **FR-7.3** The site MAY offer a contact form later; it is not part of this release.
- **FR-7.4** The site MUST NOT offer a newsletter.

### Instagram

- **FR-8.1** The footer of every page MUST link to Instagram account @ninalaaf.
- **FR-8.2** The home page MUST show the 6 most recent posts of @ninalaaf that are not hidden (FR-8.6). Posts are copied into the site when it is built, so visitors' browsers never load anything from Instagram (`SECURITY.md` SEC-PRIV-1, SEC-PRIV-3).
- **FR-8.3** Privacy rules for Instagram content: `SECURITY.md` SEC-PRIV-1 and SEC-PRIV-3; no consent step is needed because visitors make no request to Instagram.
- **FR-8.4** If no posts can be shown (feed switched off, or none ever fetched), the home page MUST still render and MUST show the link from FR-8.1.
- **FR-8.5** If the posts cannot be refreshed during a build, the build MUST still publish all other content, and the home page MUST show the posts from the last successful refresh. A fetched post whose media fails the checks of `SECURITY.md` SEC-OUT-3 MUST be left out like a hidden post (FR-8.6), without failing the build. (Threats: `SECURITY.md` T-39, T-68.)
- **FR-8.6** Posts appear without Nina reviewing them. Nina MUST be able to hide a single post and to switch the whole feed off in the editing interface; the change is published like any other content change (FR-10.4). (Threat: `SECURITY.md` T-36.)

### Legal pages

- **FR-9.1** Every page MUST link to the Impressum and the Datenschutzerklärung.

### Content editing

- **FR-10.1** Nina MUST be able to create, edit, unpublish and delete works, images, Exhibition entries, News items, texts, CV entries, downloads and page content without using Markdown, Git, HTML or a command line.
- **FR-10.2** Nina MUST be able to upload images and PDFs and set image order, alt text and photo credit.
- **FR-10.3** Nina MUST be able to mark exactly one work as featured (FR-2.1); marking a work removes the mark from the previous one. She MUST be able to mark works as in public space (FR-3.8), set the manual order of works and texts (FR-3.1, FR-5.1), and edit the CV as in FR-5.4.
- **FR-10.4** Changes Nina saves MUST appear on the public site within 15 minutes after publishing. The grouping of FR-4.1 and the home page entry of FR-2.3 MUST update at least once a day without an editor action.
- **FR-10.5** Restricting content changes to the editors: `SECURITY.md` SEC-AUTHN-1, SEC-AUTHZ-1, SEC-AUTHZ-2.
- **FR-10.6** The editor MUST reject a save and show which field is missing when: a work has no title, year or image; an image has no alt text; a Text has no title or body; a CV entry has no section or text, or no year in any section other than the short bio; a Download has no title or PDF file; a News item has no title or date; an Exhibition entry has no start or end date. A publish MUST fail (FR-10.8) when no published work is featured, or when the Contact email address, the Impressum or the Datenschutzerklärung is missing.
- **FR-10.7** Nina MUST be able to see, for each content item, who changed it and when, and MUST be able to restore an earlier version. Attribution rule: `SECURITY.md` SEC-AUDIT-1. (Threat: `SECURITY.md` T-9.)
- **FR-10.8** When a publish fails, or a saved change is still not live 15 minutes after it was saved (FR-10.4), the editing interface MUST show Nina that her changes are not live yet and, when content caused the failure, which item and field. Ives (the maintainer) MUST be notified by email. (Threats: `SECURITY.md` T-24, T-58.)
- **FR-10.9** Editors MUST be able to preview unpublished content before it is published; previews are reachable only by editors (`SECURITY.md` SEC-CICD-3).

### Migration

- **FR-11.1** All 53 legacy work series (`docs/legacy-content/works/`) MUST be imported with their legacy title, year, materials, dimensions and images. Legacy values are German; their English fields stay empty and FR-1.7 applies.
- **FR-11.2** Legacy works without a year (12 of 53) MUST be imported unpublished until a year is added; all other legacy works MUST be published.
- **FR-11.3** Migrated images MUST receive interim alt text made of the work title and the view (e.g. "tiptoe, installation view 1"), which counts as alt text under FR-3.7 and FR-10.6 until it is improved.
- **FR-11.4** The legacy "featured" flag MUST be ignored. At launch the newest migrated work ("vom Flüstern und Formen", 2023) is the featured work until Nina changes it.

## Non-Functional Requirements

- **NFR-1** Pages and images MUST be fast on mobile: images MUST be delivered in sizes suited to the visitor's screen rather than as the ~4 MB originals. Every page type MUST meet Core Web Vitals "good" in a Lighthouse mobile run (mid-range phone, 4G): Largest Contentful Paint ≤ 2.5 s, Cumulative Layout Shift ≤ 0.1, Interaction to Next Paint ≤ 200 ms, and an initial page weight of at most 1.5 MB.
- **NFR-2** The public site MUST be usable without logging in and MUST NOT require visitor accounts.
- **NFR-3** No third-party content in the visitor's browser: `SECURITY.md` SEC-PRIV-1.
- **NFR-4** All pages MUST work on the last two major versions of Chrome, Edge, Firefox, Safari (macOS and iOS) and Samsung Internet, on desktop and mobile.

## Open Questions

None. OQ-1 to OQ-10 were resolved in step 10 (`.planning/OPEN_QUESTIONS.md`).
