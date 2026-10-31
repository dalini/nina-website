# DESIGN

`DESIGN.md` owns the visual and frontend design language of nina-laaf.de. `style-guide.html` renders it as working code; `logo.svg` is the mark. Product, users and pages: see `REQUIREMENTS.md`.

## Required Design Inputs

- Brand personality: TO BE DECIDED (DQ-1)
- Primary audience: See `REQUIREMENTS.md` (Required Requirement Inputs)
- Platform targets (web / mobile / both): both (`REQUIREMENTS.md` NFR-1, NFR-4)
- Light / dark mode: TO BE DECIDED (DQ-4)
- Existing brand assets: UNKNOWN

## Brand and Logo

- Brand direction: TO BE DECIDED (DQ-1). The design stays quiet and neutral so that the artwork images carry the site.
- Logo concept (provisional): an abstract mark, one solid round body resting on a flat base line: a form placed on a plinth. It uses no letters, so it works for both languages.
- Wordmark: "Nina Laaf" set in the primary font (Typography), next to or below the mark. TO BE DECIDED whether the wordmark replaces the mark in the header (DQ-2).
- Usage: keep clear space of at least 25% of the mark's width on every side. Minimum display size: 24 × 24 px. Show it in `primary` on `background` or `surface`, or in `background` on `primary`.
- Incorrect usage: do not stretch or rotate it, add effects or shadows, recolor it outside the palette, or place it on top of artwork images.

## Color Palette

| Token | Hex | Use |
|---|---|---|
| `primary` | `#1f1f1f` | logo, headings, buttons, links |
| `secondary` | `#5c5c5c` | top edge of the logo base, metadata (year, materials, credits), secondary text |
| `background` | `#ffffff` | page background |
| `surface` | `#f4f3f1` | panels, cards, consent placeholder, image placeholders |
| `text` | `#1f1f1f` | body text |
| `error` | `#b3261e` | form errors |
| `success` | `#1d6b38` | confirmations |

Logo colors: `primary` (body and base) and `secondary` (top edge of the base).

Contrast (WCAG 2.2 AA): `text`/`primary` on `background` 16.5:1, on `surface` 14.9:1; `secondary` on `background` 6.7:1, on `surface` 6.0:1; `error` and `success` on `background` 6.5:1, on `surface` 5.9:1; `background` on `primary` 16.5:1. All pass 4.5:1 for body text.

## Typography

- Primary: system UI stack `system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`. It loads no font files, so it meets the self-hosting rule (`SECURITY.md` SEC-PRIV-1), and it reads well in German and English.
- Fallback: `sans-serif`.
- Brand typography: TO BE DECIDED (DQ-3).
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
- **Image with credit**: image, then caption and credit ("Foto: …" / "Photo: …") in `secondary`, `xs` size.
- **Consent placeholder** (Instagram feed): a `surface` box explaining what loads and from where, with one button to load the content (loading rules: `SECURITY.md` SEC-PRIV-2).
- **Inputs and form feedback** (editing interface and any later contact form): label above the field, 1 px `secondary` border, 4 px radius; errors show in `error` below the field, with text that names the problem (not color alone).
- **Focus state**: every interactive element shows a 2 px `primary` outline with 2 px offset on keyboard focus.

## Accessibility

- Target conformance: WCAG 2.2 AA.
- Contrast: text pairs as listed under Color Palette; non-text indicators (focus outline, borders on inputs) meet 3:1.
- Visible focus on every interactive element; never remove outlines without a replacement.
- Everything works with the keyboard alone, including the language switch, image sequences and the consent button.
- Alt text: `REQUIREMENTS.md` FR-3.7. Decorative images are hidden from screen readers.
- The `lang` attribute matches the page language, and German and English pages are announced correctly.
- Reduced motion: when the visitor prefers reduced motion, the site uses no animated transitions or auto-advancing images.

## Open Questions

- **DQ-1** What is the brand personality and direction, if any beyond "quiet, image-first"?
- **DQ-2** Is the header the mark, the wordmark "Nina Laaf", or both?
- **DQ-3** Is a custom (self-hosted, open-licensed) typeface wanted instead of the system stack?
- **DQ-4** Light mode only, or also dark mode?
