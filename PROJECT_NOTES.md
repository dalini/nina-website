# Project Notes: nina-laaf.de

> **Fill this in before step 1.** The prompts treat this file as the *only* source of truth.
> Anything written here becomes a requirement, and anything left out won't be built.
> Lines marked `[CONFIRM]` are suggestions: keep, change or delete each one.
> Delete a whole line rather than leaving it vague. Write `NONE` under a heading if nothing applies.

---

## 1. Project notes (used by step 01 → REQUIREMENTS.md)

### Purpose
- Rebuild the personal website of **Nina Laaf**, artist and sculptor, at `nina-laaf.de`.
- The live nina-laaf.de is currently an empty placeholder ("Hello World").
- The previous site ran on **Koken CMS**. A first rebuild attempt (`dalini/nina-laaf-site`, Eleventy) already migrated its content: **53 work series (2012–2023)**, CV, news, one essay, contact page. Their text is copied into `docs/legacy-content/`; images (~4 MB JPEGs each) stay in that repo's Git LFS. All of it MUST be carried over to the new site with no loss of works, images, titles, materials, dimensions or years. [CONFIRM]
- The old site's navigation was: **Works, Essays, News, CV, Contact**. [CONFIRM] keep it.
- Goal of the site: [CONFIRM] present her work to curators, galleries, collectors and press, and make it easy to get in touch.

### About the artist (from public gallery/press sources, 2018–2021: verify and update)
- Sculptor, lives and works in Karlsruhe. Studied at the Staatliche Akademie der Bildenden Künste Karlsruhe; Meisterschülerin of Prof. Harald Klingelhöller.
- Works with: large site-specific sculptures and room-sized installations built from many small elements; wall pieces combining plaster/gypsum, steel, copper and lacquer (series *Circles*); small ceramics with gold glaze; silk-screen prints in editions (e.g. "ed. 10").
- Gallery: galerie burster (Berlin / Karlsruhe), still current in 2026 (solo show *MUTUAL*, Nov 2025; group show *HEAT*, Aug–Sep 2026). The site should link to the gallery and name it as the contact for sales. [CONFIRM]
- Active exhibition practice: several shows a year, so the exhibitions list must be easy to keep current.
- Her full exhibition history and works are collected in `docs/CONTENT_SEED.md`. That file is content, not specification.
- Exhibitions and press exist in German and English; installation photos are credited to photographers (e.g. Silke Briel).
- The old nina-laaf.de hosted PDF downloads (e.g. diploma catalogue *betwixt*, 2016).

### What this means for the site
- A work record needs: title (often lowercase, sometimes "ohne titel (…)"), year, materials, dimensions (H × W × D, cm; sometimes 2 values), edition (for prints), series, photo credit per image, and optionally a short text.
- Works need **installation views** as well as single-object photos, and an exhibition should link to the works shown in it.
- Exhibitions list: solo/group, title, venue, city, dates, optional link, optional PDF/catalogue/audio guide.
- Gallery representation shown on Contact and/or About.

### Who uses it
- **Visitors** (public, no login): curators, gallerists, collectors, press, students, the general public. [CONFIRM]
- **Nina (site owner)**: adds and updates works, exhibitions and texts. [CONFIRM: does she edit content herself, or does someone else do it?]

### Content and pages
- **Home**: [CONFIRM] one strong image or a small selection of current work, name, short tagline.
- **Works / Arbeiten**: [CONFIRM] grouped by series or year; each work has images, title, year, material, dimensions, and optionally a short text. Sculpture often needs several views per work, and maybe video.
- **Exhibitions / Ausstellungen**: [CONFIRM] upcoming, current, past; title, venue, city, dates, solo/group, link.
- **About / Vita (CV)**: [CONFIRM] short bio, education, awards and grants, collections, bibliography.
- **Essays / Texts**: catalogue texts and essays (the old site had one essay, *What you see is not always what it seems to be*).
- **News**: current and upcoming exhibitions and fairs (e.g. art karlsruhe, art antwerp) with dates, venue, co-exhibitors and link; past entries move to an archive.
- **CV** (old site): solo exhibitions, works in public space (permanent outdoor installations, e.g. Skulpturenradweg Seckach, Art Loop Strohgäu, Botanischer Garten Karlsruhe), group exhibitions, awards. Bilingual labels side by side ("Einzelausstellungen | Solo Exhibitions").
- **Portfolio downloads**: PDFs (*Portfolio Nina Laaf 2023*, *Außenskulpturen 2023*, older catalogues). [CONFIRM] which to keep / update.
- **Contact / Kontakt**: the old site had a contact form (email, name, message) and a newsletter sign-up. [CONFIRM] keep the form, or email link only? Keep the newsletter (it needs a mailing provider and double opt-in under GDPR)?
- **Works in public space** as their own category or filter. [CONFIRM]
- **Impressum** and **Datenschutzerklärung**: required for a site operated from Germany.

### Languages
- [CONFIRM] German and English (press and gallery texts already exist in both). Which one is the default?
- [CONFIRM] Link to Instagram (@ninalaaf). Link only; no embedded feed, since embeds load Meta scripts before consent.

### Things it must NOT do (keep it small)
- [CONFIRM] No shop, no user accounts, no comments. (Newsletter: see Contact.)

### Content to supply
- Images of works (high resolution), image credits / photographers.
- Bio, CV, exhibition list, texts.
- Existing logo or signature: [CONFIRM] none, or describe it.

---

## 2. Architecture notes (used by step 03, replaces <<ARCHITECTURE_NOTES_OR_NONE>>)

- The first rebuild attempt used **Eleventy** (static site generator), content as Markdown with YAML front matter, images in **Git LFS**, deployed via GitHub Actions to GitHub Pages (a Netlify config also existed). [CONFIRM] keep this direction, or let step 03 decide fresh.
- [CONFIRM] Prefer a **static website** (pre-built pages, no server-side code, no database) unless a requirement needs more. A contact form or newsletter on a static site needs an external service: name it or leave it TO BE DECIDED.
- [CONFIRM] Content editing: Nina edits Markdown files herself / uses a simple CMS / someone else edits for her.
- Hosting: [CONFIRM] keep the current host of `nina-laaf.de` (which one?) or choose a new one.
- Images: must stay fast on mobile; served in responsive sizes.
- Write `NONE` here if you'd rather leave all of this open for step 03.

---

## 3. Security notes (used by step 04, replaces <<SECURITY_NOTES_OR_NONE>>)

- The site is operated from Germany and addresses visitors in the EU: GDPR applies; Impressum and Datenschutzerklärung required.
- [CONFIRM] No tracking or analytics, or only cookieless analytics, so no cookie banner is needed.
- [CONFIRM] No third-party embeds that load before consent (Google Fonts, YouTube, Instagram, Maps).
- [CONFIRM] If there's a contact form: spam protection without third-party CAPTCHA; messages go to Nina's email; nothing stored on the server.
- Protect image rights: [CONFIRM] show watermarks? Disable high-res download? (Note: any image a browser shows can be saved.)
