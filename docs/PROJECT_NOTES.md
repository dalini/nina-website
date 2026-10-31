# Project Notes: nina-laaf.de

> Input for steps 01, 03 and 04. The prompts treat this file as the *only* source of truth.
> Lines starting with **Open:** are undecided on purpose; they become open questions, not features.
> Decisions from Ives, 2026-10-31: bilingual DE/EN, email contact (form optional), Instagram feed wanted,
> fully new tech, static site with an easy editing backend, take over a selection of the old works.
> Archived after step 04: the spec files in the repo root are now the source of truth. Record new decisions there, not here.

---

## 1. Project notes (used by step 01 → REQUIREMENTS.md)

### Purpose
- New website for **Nina Laaf**, artist and sculptor, at `nina-laaf.de`. It presents her work to curators, galleries, collectors, press and the public, and makes it easy to get in touch.
- The live nina-laaf.de is currently an empty placeholder ("Hello World").
- The site is built from scratch. Earlier versions (Koken CMS, then an Eleventy attempt in `dalini/nina-laaf-site`) are input only; their text content is in `docs/legacy-content/`, and their images are in that repo's Git LFS.
- **A selection of the 53 old work series (2012–2023) is taken over**, not all of them. Nina chooses which. Each migrated work keeps its title, year, materials, dimensions and images.
- **Open:** which works are migrated (Nina's selection list).

### Who uses it
- **Visitors** (public, no login): curators, gallerists, collectors, press, students, the general public.
- **Nina (site owner, non-technical)**: adds and updates works, exhibitions, news and texts **herself**, through an easy editing interface. She must not need to know Markdown, Git, HTML or the command line.

### About the artist (facts that shape the site; details in `docs/CONTENT_SEED.md`)
- Sculptor, lives and works in Karlsruhe. Represented by galerie burster (Berlin / Karlsruhe).
- Large site-specific sculptures and room-sized installations, permanent works in public space, wall pieces (plaster, steel, copper, lacquer), small ceramics, silk-screen prints in editions.
- Several exhibitions a year, so exhibitions and news must be quick to add.
- Installation photos are credited to photographers.

### Content and pages
- **Navigation**: Works, Exhibitions/News, Texts, CV, Contact (the old site used Works, Essays, News, CV, Contact).
- **Home**: name and a selection of current or featured work. **Open:** one large image, or a small selection?
- **Works**: list of works; each work page has title, year, materials, dimensions (2 or 3 values, cm), optional edition (for prints), optional series, optional short text, and several images (object views and installation views), each image with alt text and an optional photo credit. Works in public space are marked as such and can be found as a group.
- **Exhibitions / News**: upcoming, current and past exhibitions and fairs; each entry has title, type (solo / two-person / group / fair), venue, city, dates, optional co-exhibitors, optional link, and optional linked works. Past entries remain visible as an archive.
- **Texts**: essays and catalogue texts (the old site had one: *What you see is not always what it seems to be*).
- **CV**: short bio, education, solo exhibitions, works in public space, group exhibitions, awards and grants.
- **Downloads**: portfolio PDFs (the old site offered *Portfolio Nina Laaf 2023* and *Außenskulpturen 2023*). **Open:** which PDFs are offered.
- **Contact**: Nina's email address and a link to the gallery. A contact form MAY be added later; it is not required. **Open:** whether the gallery is named as the contact for sales.
- **Instagram**: a link to @ninalaaf, plus an **Instagram feed** showing her recent posts on the site. It must not load anything from Instagram/Meta in the visitor's browser before the visitor consents. **Open:** how the feed is provided (for example: a click-to-load feed after consent, or recent posts copied into the site when it is built).
- **Impressum** and **Datenschutzerklärung**: required, since the site is operated from Germany.

### Languages
- All pages and all content fields that contain text exist in **German and English**. Visitors can switch language on every page; titles of works are not translated.
- **Open:** which language is the default.

### Not in scope
- No shop, no user accounts for visitors, no comments.
- **Open:** newsletter (the old site had one; it would need a mailing provider and double opt-in).

---

## 2. Architecture notes (used by step 03)

- Tech is **fully new**. The Eleventy setup of the earlier attempt does not need to be kept.
- The public site is **statically rendered**: pre-built pages, no server-side code or database serving visitors.
- **Hugo** is a candidate static site generator. **Open:** Hugo or another generator.
- Nina edits through an **easy web-based editing backend** suitable for a non-technical artist, that publishes to the static site (for example a Git-based headless CMS). **Open:** which editing system.
- Images must stay fast on mobile and be served in responsive sizes; originals are large (~4 MB JPEGs).
- **Open:** hosting and deployment (where `nina-laaf.de` is hosted today is unknown).

---

## 3. Security notes (used by step 04)

- Operated from Germany for an international audience: GDPR applies; Impressum and Datenschutzerklärung are required.
- No third-party content (fonts, Instagram/Meta, video, maps) may load in the visitor's browser before consent; fonts are self-hosted.
- **Open:** analytics (none, or cookieless only).
- Only Nina (and people she authorizes) may change content through the editing backend.
- If a contact form is added later: spam protection without a third-party CAPTCHA, and messages are not stored on the website.
- **Open:** image protection (watermarks, limiting download size).
