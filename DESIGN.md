# DESIGN

`DESIGN.md` owns the visual and frontend design language of nina-laaf.de. `style-guide.html` renders it as working code; `logo.svg` is the mark. Product, users and pages: see `REQUIREMENTS.md`. Decisions from step 10 and step 10 again: `.planning/OPEN_QUESTIONS.md`.

## Required Design Inputs

- Brand personality: quiet, neutral, image-first; no further direction
- Primary audience: See `REQUIREMENTS.md` (Required Requirement Inputs)
- Platform targets (web / mobile / both): both (`REQUIREMENTS.md` NFR-1, NFR-4)
- Light / dark mode: both, following the visitor's system setting (`prefers-color-scheme`)
- Existing brand assets: none

## Brand and Logo

- Brand direction: quiet and neutral, so that the artwork images carry the site.
- Logo concept (provisional until Nina decides on a mark; deferred): an abstract mark, one solid round body resting on a flat base line: a form placed on a plinth. It uses no letters, so it works for both languages.
- Wordmark: "Nina Laaf" set in the primary font (Typography), weight 600. The header shows the wordmark only; the mark is used as the favicon.
- Usage: keep clear space of at least 25% of the mark's width on every side. Minimum display size: 24 × 24 px. Show it in `primary` on `background` or `surface`, or in `background` on `primary`, in the current color mode.
- Incorrect usage: do not stretch or rotate it, add effects or shadows, recolor it outside the palette, or place it on top of artwork images.

## Color Palette

Light mode is the default; dark mode applies when the visitor's system prefers dark (`prefers-color-scheme: dark`). Both use the same token roles.

| Token | Light | Dark | Use |
|---|---|---|---|
| `primary` | `#1f1f1f` | `#ededed` | logo, headings, buttons, links |
| `secondary` | `#5c5c5c` | `#a3a3a3` | top edge of the logo base, metadata (year, materials, credits), secondary text |
| `background` | `#ffffff` | `#141414` | page background |
| `surface` | `#f4f3f1` | `#222120` | panels, cards, image placeholders |
| `text` | `#1f1f1f` | `#ededed` | body text |
| `error` | `#b3261e` | `#f2b8b5` | form errors |
| `success` | `#1d6b38` | `#8fd19e` | confirmations |

Logo colors: `primary` (body and base) and `secondary` (top edge of the base).

Contrast (WCAG 2.2 AA). Light: `text`/`primary` on `background` 16.5:1, on `surface` 14.9:1; `secondary` on `background` 6.7:1, on `surface` 6.0:1; `error` and `success` on `background` 6.5:1, on `surface` 5.9:1; `background` on `primary` 16.5:1. Dark: `text`/`primary` on `background` 15.7:1, on `surface` 13.7:1; `secondary` on `background` 7.3:1, on `surface` 6.4:1; `error` on `background` 10.8:1, on `surface` 9.4:1; `success` on `background` 10.3:1, on `surface` 9.0:1; `background` on `primary` 15.7:1. All pairs in both modes pass 4.5:1 for body text.

## Typography

- Primary: system UI stack `system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`. It loads no font files, so it meets the self-hosting rule (`SECURITY.md` SEC-PRIV-1), and it reads well in German and English.
- Fallback: `sans-serif`.
- Brand typography: none; the system stack is the brand typeface (no font files).
- Weights: 400 (regular), 600 (semibold, for headings and titles).
- Scale (rem, base 16 px): `xs` 0.8125 · `sm` 0.875 · `base` 1 · `lg` 1.25 · `xl` 1.5 · `2xl` 2 · `3xl` 2.75. Body line height 1.6, headings 1.2.
- Work titles keep their original casing (often lowercase).

## Layout and Spacing

- Spacing scale (px): `1` 4 · `2` 8 · `3` 12 · `4` 16 · `5` 24 · `6` 32 · `7` 48 · `8` 64 · `9` 96.
- Content width: text at most 68 characters per line; image areas up to 1200 px wide; page margin `4` on mobile, `6` from `md` up.
- Grid: works lists use a flexible grid, 1 column on small screens, 2 from `md`, 3 from `lg`. Images keep their own aspect ratio and are never cropped on work pages.
- Breakpoints: `sm` 480 px · `md` 768 px · `lg` 1024 px · `xl` 1280 px.

## Components

- **Links**: `primary`, underlined in text; the underline thickens on hover. Navigation links show the current page with a 2 px bottom border.
- **Buttons**: one style, solid `primary` with `background` text, 4 px radius, at least 44 × 44 px; hover lightens the fill; disabled uses `secondary` and is not clickable.
- **Language switch** (`REQUIREMENTS.md` FR-1.3): shown in the header as "DE | EN", with the current language marked and announced to screen readers.
- **Work metadata**: title (semibold), then year, materials, dimensions, edition in `secondary`, `sm` size.
- **Image with credit**: image, then caption (optional, `REQUIREMENTS.md` FR-3.7) and credit ("Foto: …" / "Photo: …") in `secondary`, `xs` size.
- **Image viewer** (work pages, `REQUIREMENTS.md` FR-3.11): opens the selected image full-screen on `background`, with caption and credit below it; previous, next and close are Buttons, also reachable with the arrow keys and Esc; it stops at the ends (Previous is disabled on the first image, Next on the last, using the disabled Button style) and shows the position ("3 / 7") in `secondary`, `xs` size; focus moves into the viewer and returns to the image when it closes; no auto-advance, no animated transition when reduced motion is preferred. Without JavaScript each image is a plain link to its largest size.
- **Exhibition entry page** (`REQUIREMENTS.md` FR-4.7): title (semibold), then type, venue, city and dates in `secondary`; co-exhibitors and external link when entered; the description in body text; installation images as Image with credit; linked works as thumbnails in the works grid.
- **Consent banner** (`REQUIREMENTS.md` FR-9.2): a bar fixed to the bottom of the viewport on `surface`, with a short text and a link to the Datenschutzerklärung, and two Buttons of equal size and style (Accept, Decline); it never covers the page's main content on load and does not block scrolling. A footer link reopens it. Text and labels are fixed in the templates (not editable content), written by Ives with the Datenschutzerklärung at implementation and approved by Nina (`SECURITY.md` Required Security Inputs); the text must not claim that nothing reaches Google before consent. Not shown on the preview site.
- **Instagram posts** (home page, `REQUIREMENTS.md` FR-8.2): up to 6 post images laid out in the works grid (Layout and Spacing), each linking to its post, followed by the @ninalaaf link; images are self-hosted (`SECURITY.md` SEC-PRIV-3). When no posts exist only the link is shown (FR-8.4).
- **Publish status banner** (Editing Interface, `REQUIREMENTS.md` FR-10.8): the one element added to the otherwise unstyled Sveltia CMS page: a full-width strip above the CMS on `surface` with a 4 px `error` left border, body text in `text`. German, informal: "Deine Änderungen sind noch nicht online." and, when content caused it, "Bitte prüfe: <Eintrag>, Feld <Feld>." It is hidden once the changes are live.
- **Inputs and form feedback** (public site only: the consent banner and any later contact form; the Editing Interface uses Sveltia CMS's own UI without restyling): label above the field, 1 px `secondary` border, 4 px radius; errors show in `error` below the field, with text that names the problem (not color alone).
- **Interface labels** (`REQUIREMENTS.md` FR-1.1, FR-4.1, FR-4.2, FR-5.2, FR-6.2):
  - Navigation: EN Works, Exhibitions/News, Texts, CV, Contact; DE Arbeiten, Ausstellungen/News, Texte, Vita, Kontakt.
  - CV sections: EN Short bio, Education, Solo exhibitions, Works in public space, Group exhibitions, Awards and grants; DE Kurzbiografie, Ausbildung, Einzelausstellungen, Arbeiten im öffentlichen Raum, Gruppenausstellungen, Preise und Stipendien.
  - Exhibition groups: EN Upcoming, Current, Past; DE Demnächst, Aktuell, Vergangen.
  - Exhibition types: EN Solo, Two-person, Group, Fair; DE Einzelausstellung, Doppelausstellung, Gruppenausstellung, Messe.
  - Download label: "PDF" in both languages.
  - Dates (`REQUIREMENTS.md` FR-1.8): DE `DD.MM.YYYY`, EN `YYYY-MM-DD`; ranges "12.03.2026 – 03.05.2026" / "2026-03-12 – 2026-05-03".
  - CV line from a work in public space (`REQUIREMENTS.md` FR-5.3): year, title.
- **Focus state**: every interactive element shows a 2 px `primary` outline with 2 px offset on keyboard focus.

## Accessibility

- Target conformance: WCAG 2.2 AA.
- Contrast: text pairs as listed under Color Palette; non-text indicators (focus outline, borders on inputs) meet 3:1.
- Visible focus on every interactive element; never remove outlines without a replacement.
- Everything works with the keyboard alone, including the language switch, image sequences and the image viewer.
- Alt text: `REQUIREMENTS.md` FR-3.7. Decorative images are hidden from screen readers.
- The `lang` attribute matches the page language, and German and English pages are announced correctly.
- Reduced motion: when the visitor prefers reduced motion, the site uses no animated transitions or auto-advancing images.

## Open Questions

None open. DQ-1 to DQ-4 were resolved in step 10; PQ-38, PQ-46 and PQ-52 in step 10 again; PQ-60, PQ-63, PQ-65 and PQ-66 in step 10 a third time (`.planning/OPEN_QUESTIONS.md`). Deferred: the final mark; `logo.svg` stays a provisional placeholder until Nina decides.
