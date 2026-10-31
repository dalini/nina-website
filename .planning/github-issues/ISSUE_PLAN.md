# ISSUE_PLAN — nina-laaf.de

Step 9, third run (`prompts/09-github-epic.md`), 2026-10-31, after step 10 again (answers to PQ-29 to PQ-53 in `.planning/OPEN_QUESTIONS.md`) and the step 8 re-run that produced SECURITY.md threat model v3. The specs changed after the second run's drafts, so the scope was decomposed again: the blocked scope of the second run is now drafted, the scope that step 10 again and threat model v3 added has issues, and the existing drafts were revised against the current specs. Nothing on GitHub was changed.

## Run Inputs

- `Scope: ALL`
- `Execution mode: DRAFT_ONLY` — local files only; no mutating `gh` command was run.
- Sources read: `REQUIREMENTS.md`, `ARCHITECTURE.md`, `SECURITY.md`, `DESIGN.md`, `REQUIREMENT_TEMPLATE.md`; `CLAUDE.md` followed; `style-guide.html` cited for UI issues. `.planning/OPEN_QUESTIONS.md` read as the record of the step 10 decisions. `docs/legacy-content/works/` read only to check the shape of the legacy works that FR-11.1 points to (it found PQ-58); it is not specification.
- Result: 116 draft issue bodies: 1 epic, 15 workstreams, 100 leaf issues.

## Planning Rules Applied

- **Blocking** means the answer changes observable behavior, roles or permissions, auth, an API contract, data semantics, a trust boundary, a security control, failure behavior, or what an acceptance test can assert (prompt definition). Scope that depends on a blocking question is listed under Blocked Scope and is **not** decomposed. Where only part of a requirement depends on one, the rest is decomposed and the issue names the excluded part in Out of Scope.
- **Non-blocking** questions are selections, values, wordings and orderings that leave every acceptance criterion pass/fail as written. They appear as a PQ reference in the issues' Open Decisions and still have to be answered before work on those issues starts, because an implementer must not make them alone.
- **Question numbering** continues after PQ-1 to PQ-53, which step 10 and step 10 again answered (`.planning/OPEN_QUESTIONS.md`); this run's questions start at PQ-54 so that no ID means two things. PQ-54 and PQ-55 are SECURITY.md SQ-22 and SQ-23.
- **IDs** of the earlier runs are kept where the scope is the same; their version goes up by one minor step (0.2.0 → 0.3.0, 0.1.0 → 0.2.0). New issues start at 0.1.0. REQ-PIPELINE-050 and REQ-IMAGES-030, left free by the second run, now hold the scope they were reserved for. New capability names come from the sources: CONSENT (REQUIREMENTS.md "Legal pages and consent", FR-9.2) and AUTHN (SECURITY.md SEC-AUTHN-*).
- **Requirements that changed** since the second run's drafts were re-drafted, not only annotated: e.g. REQ-EXHIBITIONS-010 (the linked works moved to the entry page, FR-4.7), REQ-PRIV-010 and REQ-HTTP-020 (Google Analytics is the one allowed third party), REQ-CONTENT-050 (required fields, end-before-start, description, installation images, slug), REQ-MIGRATION-010 (captions kept; materials and dimensions blocked again, PQ-58).
- **Standards Alignment**: SECURITY.md uses no ASVS identifiers until they are verified; every issue states the target (ASVS 5.0.0 Level 1) and marks NIST SP 800-53 and Zero Trust as not applicable (SECURITY.md). Only references SECURITY.md cites (`REF-*`) or that define the mechanism (RFC 6265, RFC 6797, W3C CSP, WCAG 2.2) are listed. Threat references are STRIDE categories and T-IDs of SECURITY.md Threat Model v3, plus CWE identifiers where they match directly.
- **Models**: verified on 2026-10-31 against Anthropic's documentation (platform.claude.com/docs/en/about-claude/models/choosing-a-model and the API primer): Claude Opus 5.5 `claude-opus-5-5`, Claude Sonnet 5.5 `claude-sonnet-5-5`, Claude Fable 5.1 `claude-fable-5-1`. Fable is not assigned: no leaf is long-horizon or crosses many boundaries. OpenAI GPT is not assigned: the project uses no OpenAI tooling.
- **Release gate.** The epic is not releasable while any Blocked Scope row below is undrafted (all are answered in step 10 a third time; the next step 9 run drafts them): without them Nina cannot edit (PQ-54), consent cannot be shown to stay valid when the banner changes (PQ-55), Ives gets no alert for failed certificate renewals, token refreshes or content checks (PQ-56), Exhibition pages can collide (PQ-57), the migrated works lack their materials and dimensions (PQ-58), and the manual Works order that replaces the year order is missing (PQ-59).

## Open Questions

PQ-1 to PQ-53 are answered (`.planning/OPEN_QUESTIONS.md`). This run raised PQ-54 to PQ-66; all are answered in step 10 a third time (2026-10-31; full answers in `.planning/OPEN_QUESTIONS.md`, written into the spec files that own each domain). The answer column is a short form. The next step 9 run decomposes the blocked scope below and re-drafts the affected issues.

### Blocking

| ID | Question | Source and conflict | Affected IDs | Answer (step 10 a third time) |
|---|---|---|---|---|
| PQ-54 | How do the Editing Interface files (the Sveltia CMS bundle, the CMS configuration, and the Deno OAuth helper's code and lockfile) get from `dalini/nina-site` onto the VPS, and under which account: installed by Ives as administrator, or deployed by the pipeline with a separate key limited to those paths? Whatever is chosen, the web server, the helper's own account and the production and preview deploy keys must not be able to write them. | SECURITY.md SQ-22, SEC-CICD-2, T-82, DF-16; ARCHITECTURE.md Editing Interface (TO BE DECIDED) | REQ-EDIT-010, REQ-AUTHN-030 (excluded parts); REQ-AUTHN-010, REQ-AUTHN-000, REQ-EDIT-000 (Open Decisions); FR-10.1, SEC-CICD-2, SEC-AUTHN-4 | Installed by hand by Ives as VPS administrator from a tagged, verified release; files owned by root, read-only to nginx and the helper; no deploy key writes them (SEC-CICD-2) |
| PQ-55 | Must the stored consent choice record which version of the banner the visitor answered, so that the banner asks again when its text, the purposes or the Google Analytics settings change? Must a visitor's consent be demonstrable beyond the visitor's own browser? | SECURITY.md SQ-23, T-73; REQUIREMENTS.md FR-9.2 (open note) | REQ-CONSENT-010 (excluded part), REQ-CONSENT-020, REQ-CONSENT-000; FR-9.2 | The stored choice records the banner version; Ives raises it whenever text, purposes or GA settings change, and everyone is asked again; no record outside the browser (FR-9.2, SEC-PRIV-7) |
| PQ-56 | Where is the credential of the SMTP relay kept, and which account may read it? SEC-HTTP-5 sends three alerts through one SMTP relay: certificate renewal, the Instagram token refresh (deploy account, SEC-SECRET-3) and the content check (its own account, SEC-CICD-4). SEC-SECRET-1 lists every secret on the VPS with exactly one owning account and forbids root-run services and other jobs' accounts from reading it, but lists no relay credential, nor says whether the relay needs one. | Gap: SECURITY.md SEC-HTTP-5 vs SEC-SECRET-1, SEC-HOST-2; ARCHITECTURE.md Static Hosting | REQ-HTTP-060, REQ-INSTAGRAM-050, REQ-PIPELINE-130 (alert delivery excluded); REQ-HOST-030 (Out of Scope); SEC-HTTP-5, SEC-SECRET-3, SEC-CICD-4 | A local mail relay agent under its own account alone holds the relay credential; jobs hand mail to local `sendmail` (SEC-HTTP-5, SEC-SECRET-1) |
| PQ-57 | How is the slug of an Exhibition entry set (typed by Nina, or generated from the title, and when), may it change after the page is published, and what happens when two entries have the same slug? FR-4.7 gives each entry a page at `/exhibitions/<slug>/` and SEC-INPUT-6 fixes only its characters; ARCHITECTURE.md Content Store does not list the field. | Gap: REQUIREMENTS.md FR-4.7; SECURITY.md SEC-INPUT-6; ARCHITECTURE.md Content Store | REQ-CONTENT-050, REQ-CONTENT-080, REQ-EDIT-030, REQ-EXHIBITIONS-030 (excluded parts); FR-4.7 | Generated from title and start year at first save (e.g. `faltungen-2025`), never changed afterwards; duplicates fail the publish, naming both entries (FR-4.7, SEC-INPUT-6) |
| PQ-58 | Which source gives a migrated work its materials and dimensions, and what is imported when it does not fit? FR-11.1 takes them from the caption of the work's first legacy image ("materials \| dimensions \| year"), but in `docs/legacy-content/works/` 25 of the 53 works have work-level `materials` and `dimensions` fields, 18 of them with an empty first caption, so the rule would drop data that exists; where both exist they differ (e.g. `heiter-bis-wolkig`: fields "Schwarzstahl, Kupfer, Gips, Lack" and "58 x 75 x 5 cm", first caption "Metall, Gips"). Many first captions are not in the form: installation-view texts ("Ausstellungsansicht …", "Kunstweg am Reichenbach"), parts in another order ("Lack, Aluminium getrieben \| 2018 \| 90 x 120 x5 cm"), more parts ("Raumforderung 1.3 \| 3-tlg. Serie \| C-Print aufkaschiert \| 30x20 \| 2013"), or dimensions without "cm" ("4x70x170"), which REQ-CONTENT-010 (two or three values in centimeters, FR-3.3) would reject. | Conflict: REQUIREMENTS.md FR-11.1 vs the legacy data it points to; FR-3.3 | REQ-MIGRATION-010 (excluded part), REQ-MIGRATION-000; REQ-MIGRATION-020 (AC-02 assumes the imported content builds); FR-11.1 | Work-level legacy fields; else a first caption in the clean form; else empty; a migration report lists what Nina must complete and values that break FR-3.3 (FR-11.1) |
| PQ-59 | How does Nina choose between the two orders of the Works page? FR-3.1 lists works newest year first with works of the same year in the order Nina sets, and also lets her set "a manual order that replaces it"; the content model holds one manual position per work (REQ-CONTENT-010). Nothing says how Nina switches the whole list to her manual order or back, or how the build knows which order applies. | Gap: REQUIREMENTS.md FR-3.1, FR-10.3 | REQ-WORKS-010, REQ-CONTENT-010, REQ-EDIT-020 (excluded parts); FR-3.1 | The replacing order is dropped: always newest year first, manual position within a year only (FR-3.1) |

### Non-blocking

| ID | Question | Source | Affected | Answer (step 10 a third time) |
|---|---|---|---|---|
| PQ-60 | Wording of the consent banner in German and English (short text, Accept and Decline labels, the footer link that reopens it), which no source gives; DESIGN.md says it is written with the Datenschutzerklärung and approved by Nina. Is it fixed text in the templates, like DESIGN.md Interface labels, or a content item Nina can edit (FR-10.1 does not list it)? | DESIGN.md Components (Consent banner); REQUIREMENTS.md FR-9.2, FR-10.1; ARCHITECTURE.md DR-2 | REQ-CONSENT-010 | Fixed text in the templates; Ives writes it with the Datenschutzerklärung at implementation, Nina approves; no claim that nothing reaches Google before consent (FR-9.2, DESIGN.md) |
| PQ-61 | Which Google tag setting gives host-only cookies on `nina-laaf.de`? SEC-PRIV-5 says "the tag's cookie domain set to that host", but under RFC 6265 a cookie with `Domain=nina-laaf.de` is also sent to `edit.` and `preview.`; Google documents `cookie_domain` values (and `'none'` for local servers, `REF-GTAG-COOKIES`) without saying whether they write a `Domain` attribute. Verified at implementation; if no setting gives host-only cookies, SEC-PRIV-5 cannot be met as written and the question becomes blocking. | SECURITY.md SEC-PRIV-5, T-79, `REF-GTAG-COOKIES` | REQ-CONSENT-030 | Verified at implementation; if no setting gives host-only cookies, cookies for `nina-laaf.de` are an accepted risk (SEC-PRIV-5) |
| PQ-62 | Does the preview build show the consent banner (without the tag loader and measurement ID, SEC-PRIV-5), or leave the consent script out? | REQUIREMENTS.md FR-9.2 ("every page"), FR-10.9; SECURITY.md SEC-PRIV-5 (preview) | REQ-CONSENT-040, REQ-PIPELINE-060 | No banner on the preview: the preview build leaves out the consent script and footer link (FR-9.2, SEC-PRIV-5) |
| PQ-63 | Wording (and language) and appearance of the publish status banner in the Editing Interface. DESIGN.md defines no component for it, and the Sveltia CMS UI is used without restyling. | REQUIREMENTS.md FR-10.8; DESIGN.md Components; ARCHITECTURE.md Editing Interface | REQ-EDIT-090 | German, informal ("Deine Änderungen sind noch nicht online." / "Bitte prüfe: <Eintrag>, Feld <Feld>."), a strip above the CMS on `surface` with an `error` left border (FR-10.8, DESIGN.md) |
| PQ-64 | Wording of a CV line derived from a work in public space; FR-5.3 defines only the line derived from an Exhibition entry (year, title, venue, city). | REQUIREMENTS.md FR-5.3, FR-5.4 | REQ-CV-020 | Year, title (e.g. "2019  Raumforderung") (FR-5.3) |
| PQ-65 | Date formats shown on the site in German and English: Exhibition dates and date ranges, News dates, and `<date>` / `<Datum>` in the Instagram fallback alt text (FR-8.7). | REQUIREMENTS.md FR-4.2, FR-4.6, FR-4.7, FR-8.7; DESIGN.md (no date format) | REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-NEWS-010, REQ-INSTAGRAM-020 | Numeric: DE `DD.MM.YYYY`, EN `YYYY-MM-DD`; ranges with both full dates and an en dash (FR-1.8) |
| PQ-66 | Image viewer at the first and last image of a work: do next and previous stop, or wrap around? | REQUIREMENTS.md FR-3.11; DESIGN.md Components (Image viewer) | REQ-WORKS-050 | Stops at the ends; Previous/Next disabled there; position shown, e.g. "3 / 7" (FR-3.11) |

## Blocked Scope

Not decomposed into issues yet. Every row is answered (step 10 a third time); the next step 9 run drafts it.

| Capability | Questions | Requirements |
|---|---|---|
| Getting the Editing Interface files (CMS bundle, CMS configuration, the OAuth helper's code and lockfile) from `dalini/nina-site` onto the VPS, and the account that installs them | PQ-54 | SEC-CICD-2 (Editing Interface files on the VPS, DF-16, T-82); SEC-AUTHN-4 (helper installation); FR-10.1 (Nina can only edit once the Editing Interface is installed) |
| Recording the banner version and asking again when the banner or the Google Analytics settings change; any record of consent outside the browser | PQ-55 | FR-9.2 (part), T-73 |
| Email alerts from the VPS to Ives: failed certificate renewal, failed Instagram token refresh, failed content check | PQ-56 | SEC-HTTP-5 (alert), SEC-SECRET-3 (alert), SEC-CICD-4 (alert) |
| How the slug of an Exhibition entry is set and changed, and duplicate slugs | PQ-57 | FR-4.7 (part) |
| Materials and dimensions of the migrated works | PQ-58 | FR-11.1 (materials, dimensions) |
| The manual Works order that replaces the year order, and how Nina switches to it | PQ-59 | FR-3.1 (part), FR-10.3 (part) |

## Coverage

Every FR, NFR, SEC and DEP rule of the sources, plus the one architecture decision (T-31 limits) that an issue implements without a rule ID. Status is `COVERED`, `PARTIALLY COVERED`, `BLOCKED` or `OUT OF SCOPE`; the note names the open question for the rest.

| Requirement ID | Issue IDs | Boundary | Security rules | Design sections | Status |
|---|---|---|---|---|---|
| FR-1.1 | REQ-NAV-010 | Site Build, Public Site (TB-1) | — | Components (Links, Interface labels, Focus state) | COVERED |
| FR-1.2 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-1.3 | REQ-NAV-020, REQ-EXHIBITIONS-030 | Site Build, Public Site (TB-1) | SEC-OUT-5 (only with client code) | Components (Language switch) | COVERED |
| FR-1.4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-NAV-020, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Content Store, Site Build, Editing Interface | SEC-OUT-1 | — | COVERED |
| FR-1.5 | REQ-CONTENT-010, REQ-NAV-020, REQ-WORKS-020, REQ-EDIT-020 | Content Store, Site Build | — | Typography | COVERED |
| FR-1.6 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Components (Language switch) | COVERED |
| FR-1.7 | REQ-NAV-030, REQ-WORKS-030 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-2.1 | REQ-HOME-010 | Site Build, Public Site (TB-1) | — | Layout and Spacing | COVERED |
| FR-2.2 | REQ-HOME-010 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-2.3 | REQ-HOME-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing | COVERED |
| FR-2.4 | REQ-INSTAGRAM-020, REQ-HOME-020 | Site Build, Public Site (TB-1) | SEC-PRIV-3 | Components (Instagram posts) | COVERED |
| FR-3.1 | REQ-CONTENT-010, REQ-WORKS-010, REQ-EDIT-020 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | Layout and Spacing (Grid) | PARTIALLY COVERED (manual order that replaces the year order: PQ-59, answered in step 10 a third time; drafted by the next step 9 run) |
| FR-3.2 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.3 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.4 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.5 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.6 | REQ-CONTENT-010, REQ-WORKS-030, REQ-WORKS-050, REQ-EDIT-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing (no cropping); Components (Image viewer) | COVERED |
| FR-3.7 | REQ-CONTENT-010, REQ-CONTENT-050, REQ-WORKS-030, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-TEXTS-010, REQ-NEWS-010, REQ-INSTAGRAM-020, REQ-MIGRATION-010, REQ-MIGRATION-030, REQ-EDIT-020, REQ-EDIT-030 | Site Build, Editing Interface | SEC-OUT-1 | Components (Image with credit), Accessibility | COVERED |
| FR-3.8 | REQ-CONTENT-010, REQ-WORKS-010, REQ-EDIT-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing (Grid) | COVERED |
| FR-3.9 | REQ-WORKS-040 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | — | COVERED |
| FR-3.10 | REQ-IMAGES-010, REQ-WORKS-030 | Site Build (TB-4) | SEC-PRIV-4 | — | COVERED |
| FR-3.11 | REQ-WORKS-050 | Public Site (TB-1) | SEC-OUT-5 | Components (Image viewer), Accessibility | COVERED (first and last image: PQ-66, answered in step 10 a third time) |
| FR-4.1 | REQ-CONTENT-050, REQ-EXHIBITIONS-020 | Site Build | — | Components (Interface labels) | COVERED |
| FR-4.2 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-EDIT-030 | Content Store, Site Build | — | Components (Interface labels, Exhibition entry page) | COVERED (date format: PQ-65, answered in step 10 a third time) |
| FR-4.3 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-OUTPUT-030 | Content Store, Site Build | SEC-OUT-2 | Components (Links) | COVERED |
| FR-4.4 | REQ-EXHIBITIONS-020 | Site Build | — | — | COVERED |
| FR-4.5 | REQ-CONTENT-050, REQ-EXHIBITIONS-030, REQ-EDIT-030 | Content Store, Site Build | SEC-INPUT-4 | Components (Exhibition entry page) | COVERED |
| FR-4.6 | REQ-CONTENT-050, REQ-NEWS-010, REQ-EDIT-030 | Content Store, Site Build | SEC-OUT-1, SEC-OUT-2 | Components (Image with credit) | COVERED (date format: PQ-65, answered in step 10 a third time) |
| FR-4.7 | REQ-CONTENT-050, REQ-CONTENT-080, REQ-EXHIBITIONS-030, REQ-EDIT-030 | Content Store, Site Build, Public Site (TB-1, TB-4) | SEC-INPUT-6, SEC-OUT-1 | Components (Exhibition entry page) | PARTIALLY COVERED (slug source and duplicates: PQ-57, answered in step 10 a third time; drafted by the next step 9 run) |
| FR-5.1 | REQ-CONTENT-020, REQ-TEXTS-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-1, SEC-INPUT-4 | Typography, Layout and Spacing | COVERED |
| FR-5.2 | REQ-CONTENT-020, REQ-CV-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Interface labels), Typography | COVERED |
| FR-5.3 | REQ-CV-020 | Site Build | SEC-INPUT-4 | — | COVERED (public-space line wording: PQ-64, answered in step 10 a third time) |
| FR-5.4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CV-010, REQ-CV-020, REQ-CV-030, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Content Store, Site Build, Editing Interface | SEC-OUT-1 | — | COVERED |
| FR-6.1 | REQ-CONTENT-020, REQ-DOWNLOADS-010, REQ-IMAGES-030, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-INPUT-4, SEC-PRIV-4 | Components (Links) | COVERED |
| FR-6.2 | REQ-DOWNLOADS-010 | Site Build, Public Site (TB-1) | — | Components (Interface labels: PDF) | COVERED |
| FR-7.1 | REQ-CONTENT-020, REQ-CONTACT-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-2 | Components (Links) | COVERED |
| FR-7.2 | REQ-CONTENT-020, REQ-CONTACT-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-2 | Components (Links) | COVERED |
| FR-7.3 | — | — | — | — | OUT OF SCOPE (not part of this release) |
| FR-7.4 | REQ-CONTACT-010 | Site Build, Public Site (TB-1) | — | — | COVERED |
| FR-8.1 | REQ-INSTAGRAM-010 | Site Build, Public Site (TB-1) | SEC-PRIV-1, SEC-OUT-2 | Components (Links) | COVERED |
| FR-8.2 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030, REQ-INSTAGRAM-050 | Instagram Feed Integration, Site Build (TB-5) | SEC-PRIV-3, SEC-OUT-3, SEC-SECRET-3 | Components (Instagram posts) | COVERED |
| FR-8.3 | REQ-PRIV-010, REQ-INSTAGRAM-030 | Public Site, Instagram Feed Integration | SEC-PRIV-1, SEC-PRIV-3 | — | COVERED |
| FR-8.4 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-040 | Site Build, Public Site | SEC-PRIV-1 | Components (Instagram posts) | COVERED |
| FR-8.5 | REQ-INSTAGRAM-030, REQ-INSTAGRAM-040 | Instagram Feed Integration, Site Build (TB-4, TB-5) | SEC-OUT-3, SEC-CICD-1 | — | COVERED |
| FR-8.6 | REQ-CONTENT-060, REQ-INSTAGRAM-020, REQ-EDIT-070 | Content Store, Editing Interface, Site Build | — | — | COVERED |
| FR-8.7 | REQ-INSTAGRAM-020 | Site Build | SEC-OUT-3 | Accessibility | COVERED (date format: PQ-65, answered in step 10 a third time) |
| FR-9.1 | REQ-LEGAL-010, REQ-CONTENT-020 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-9.2 | REQ-CONSENT-010, REQ-CONSENT-020 | Public Site (TB-1, TB-6) | SEC-PRIV-5, SEC-OUT-5 | Components (Consent banner) | PARTIALLY COVERED (asking again when the banner changes: PQ-55, answered in step 10 a third time; wording: PQ-60, answered in step 10 a third time; drafted by the next step 9 run) |
| FR-10.1 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040, REQ-AUTHN-010 | Editing Interface, Content Store (TB-2, TB-3) | SEC-AUTHZ-1 | — | PARTIALLY COVERED (installing the Editing Interface on the VPS: PQ-54, answered in step 10 a third time; drafted by the next step 9 run) |
| FR-10.2 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040, REQ-EDIT-050 | Editing Interface (TB-2) | SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5 | — | COVERED |
| FR-10.3 | REQ-CONTENT-010, REQ-CONTENT-030, REQ-EDIT-020, REQ-EDIT-040 | Content Store, Editing Interface, Site Build | — | — | PARTIALLY COVERED (manual Works order replacing the year order: PQ-59, answered in step 10 a third time; drafted by the next step 9 run) |
| FR-10.4 | REQ-PIPELINE-010, REQ-PIPELINE-080, REQ-PIPELINE-130 | Site Build → Static Hosting (TB-4, DF-4) | SEC-CICD-1, SEC-CICD-4 | — | COVERED |
| FR-10.5 | REQ-STORE-010, REQ-STORE-030, REQ-PREVIEW-010, REQ-AUTHN-010, REQ-AUTHN-020 | Editing Interface, Content Store, preview site (TB-2, TB-3) | SEC-AUTHN-1, SEC-AUTHZ-1, SEC-AUTHZ-2 | — | COVERED |
| FR-10.6 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030, REQ-CONTENT-080, REQ-IMAGES-040, REQ-IMAGES-050, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Site Build (TB-4), Editing Interface | SEC-INPUT-3 | — | COVERED |
| FR-10.7 | REQ-EDIT-080, REQ-STORE-040 | Editing Interface, Content Store | SEC-AUDIT-1 | — | COVERED |
| FR-10.8 | REQ-CONTENT-030, REQ-PIPELINE-110, REQ-PIPELINE-120, REQ-EDIT-090 | Site Build, Editing Interface (DF-9) | SEC-CICD-9, SEC-OUT-4 | — | COVERED (banner wording: PQ-63, answered in step 10 a third time) |
| FR-10.9 | REQ-PIPELINE-060, REQ-PREVIEW-010 | Site Build, preview site (TB-2, TB-4) | SEC-CICD-3, SEC-AUTHN-1 | — | COVERED |
| FR-11.1 | REQ-MIGRATION-010 | Content Store (DF-3) | SEC-OUT-1, SEC-PRIV-4 | — | PARTIALLY COVERED (materials and dimensions: PQ-58, answered in step 10 a third time; drafted by the next step 9 run) |
| FR-11.2 | REQ-MIGRATION-020, REQ-CONTENT-040 | Content Store, Site Build | SEC-INPUT-4 | — | COVERED |
| FR-11.3 | REQ-MIGRATION-030 | Content Store | — | — | COVERED |
| FR-11.4 | REQ-MIGRATION-020 | Content Store | — | — | COVERED |
| NFR-1 | REQ-IMAGES-010, REQ-PERF-010 | Site Build, Static Hosting | SEC-INPUT-5 | Layout and Spacing | COVERED |
| NFR-2 | REQ-NAV-010, REQ-NAV-020 | Site Build, Public Site (TB-1) | — | — | COVERED |
| NFR-3 | REQ-PRIV-010, REQ-HTTP-020, REQ-CONSENT-020 | Public Site, Static Hosting (TB-1, TB-6) | SEC-PRIV-1, SEC-PRIV-5, SEC-HTTP-2 | Typography (system fonts) | COVERED |
| NFR-4 | REQ-LAYOUT-010 and every Public Site leaf (Required Test Environment), REQ-HTTP-010 | Site Build, Public Site (TB-1) | SEC-HTTP-1 | Layout and Spacing (Breakpoints) | COVERED |
| SEC-HTTP-1 | REQ-HTTP-010 | Static Hosting (TB-1) | SEC-HTTP-1 | N/A | COVERED |
| SEC-HTTP-2 | REQ-HTTP-020, REQ-CONSENT-020 | Static Hosting, Public Site (TB-1, TB-6) | SEC-HTTP-2 | N/A | COVERED |
| SEC-HTTP-3 | REQ-HTTP-030 | Static Hosting (TB-1) | SEC-HTTP-3 | N/A | COVERED |
| SEC-HTTP-4 | REQ-HTTP-040, REQ-HTTP-050 | Editing Interface, preview site (TB-2) | SEC-HTTP-4 | N/A | COVERED |
| SEC-HTTP-5 | REQ-HTTP-060 | Static Hosting (TB-1, TB-2) | SEC-HTTP-5 | N/A | PARTIALLY COVERED (email alert: PQ-56, answered in step 10 a third time; drafted by the next step 9 run) |
| SEC-HOST-1 | REQ-HOST-010 | Domain, DNS, Static Hosting (TB-1) | SEC-HOST-1 | N/A | COVERED |
| SEC-HOST-2 | REQ-HOST-020, REQ-HOST-030, REQ-AUTHN-030, REQ-PIPELINE-010 | Static Hosting (VPS) | SEC-HOST-2 | N/A | COVERED |
| SEC-PRIV-1 | REQ-PRIV-010, REQ-LAYOUT-010, REQ-INSTAGRAM-010 | Public Site, Site Build (TB-1) | SEC-PRIV-1 | Typography | COVERED |
| SEC-PRIV-2 | — | — | — | N/A | OUT OF SCOPE (withdrawn in step 10) |
| SEC-PRIV-3 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-PRIV-3 | N/A | COVERED |
| SEC-PRIV-4 | REQ-IMAGES-010, REQ-IMAGES-020, REQ-IMAGES-030 | Site Build | SEC-PRIV-4 | N/A | COVERED |
| SEC-PRIV-5 | REQ-CONSENT-010, REQ-CONSENT-020, REQ-CONSENT-030, REQ-CONSENT-040, REQ-PRIV-010, REQ-PRIV-030, REQ-LEGAL-010 | Public Site, Site Build templates (TB-6) | SEC-PRIV-5 | Components (Consent banner) | COVERED (host-only cookie setting: PQ-61, answered in step 10 a third time) |
| SEC-PRIV-6 | REQ-PRIV-020, REQ-LEGAL-010 | Static Hosting (TB-1) | SEC-PRIV-6 | N/A | COVERED |
| SEC-PRIV-7 | REQ-PRIV-030 | Google Analytics property (TB-6) | SEC-PRIV-7 | N/A | COVERED |
| SEC-OUT-1 | REQ-OUTPUT-010, REQ-EDIT-060 | Site Build, Editing Interface | SEC-OUT-1 | N/A | COVERED |
| SEC-OUT-2 | REQ-OUTPUT-030 | Site Build | SEC-OUT-2 | N/A | COVERED |
| SEC-OUT-3 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-OUT-3 | N/A | COVERED |
| SEC-OUT-4 | REQ-EDIT-060, REQ-EDIT-090 | Editing Interface (TB-2) | SEC-OUT-4 | N/A | COVERED |
| SEC-OUT-5 | REQ-WORKS-050, REQ-CONSENT-010, REQ-CONSENT-020 | Public Site client-side code | SEC-OUT-5 | N/A | COVERED |
| SEC-OUT-6 | — | — | — | N/A | OUT OF SCOPE (withdrawn in step 10) |
| SEC-OUT-7 | REQ-OUTPUT-040 | Site Build (TB-4) | SEC-OUT-7 | N/A | COVERED |
| SEC-AUTHN-1 | REQ-AUTHN-010, REQ-PREVIEW-010 | Editing Interface, preview site (TB-2) | SEC-AUTHN-1 | N/A | COVERED |
| SEC-AUTHN-2 | REQ-STORE-010, REQ-PREVIEW-010 | Editing Interface, preview site (TB-2) | SEC-AUTHN-2 | N/A | COVERED |
| SEC-AUTHN-3 | REQ-AUTHN-020, REQ-AUTHN-040 | Editing Interface (TB-2) | SEC-AUTHN-3 | N/A | COVERED (accepted exception documented) |
| SEC-AUTHN-4 | REQ-AUTHN-010, REQ-AUTHN-020, REQ-AUTHN-030 | OAuth helper (TB-2, TB-3) | SEC-AUTHN-4 | N/A | PARTIALLY COVERED (installing the helper on the VPS: PQ-54, answered in step 10 a third time; drafted by the next step 9 run) |
| SEC-AUTHN-5 | REQ-AUTHN-050 | Editing Interface, Content Store (TB-3) | SEC-AUTHN-5 | N/A | COVERED |
| SEC-AUTHZ-1 | REQ-STORE-010 | Content Store (TB-3) | SEC-AUTHZ-1 | N/A | COVERED |
| SEC-AUTHZ-2 | REQ-STORE-010 | Content Store | SEC-AUTHZ-2 | N/A | COVERED |
| SEC-AUTHZ-3 | REQ-STORE-030, REQ-AUTHN-010 | Content Store, Editing Interface, Site Build (TB-3, TB-4) | SEC-AUTHZ-3 | N/A | COVERED (accepted risks) |
| SEC-AUTHZ-4 | REQ-STORE-010, REQ-STORE-050, REQ-PIPELINE-130 | Content Store (TB-3) | SEC-AUTHZ-4 | N/A | COVERED |
| SEC-AUDIT-1 | REQ-STORE-040, REQ-STORE-060, REQ-EDIT-080 | Content Store, Editing Interface | SEC-AUDIT-1 | N/A | COVERED |
| SEC-AUDIT-2 | REQ-PIPELINE-070 | Site Build, Static Hosting (TB-4) | SEC-AUDIT-2 | N/A | COVERED |
| SEC-AVAIL-1 | REQ-STORE-050 | Content Store, Static Hosting | SEC-AVAIL-1 | N/A | COVERED |
| SEC-INPUT-1 | REQ-EDIT-050, REQ-IMAGES-050, REQ-INSTAGRAM-030 | Editing Interface, Site Build | SEC-INPUT-1 | N/A | COVERED |
| SEC-INPUT-2 | REQ-EDIT-050, REQ-IMAGES-050 | Editing Interface, Site Build | SEC-INPUT-2 | N/A | COVERED |
| SEC-INPUT-3 | REQ-CONTENT-030, REQ-PIPELINE-010 | Site Build, Static Hosting (TB-4) | SEC-INPUT-3 | N/A | COVERED |
| SEC-INPUT-4 | REQ-CONTENT-040, REQ-PIPELINE-060 | Site Build | SEC-INPUT-4 | N/A | COVERED |
| SEC-INPUT-5 | REQ-EDIT-050, REQ-IMAGES-040, REQ-INSTAGRAM-030 | Editing Interface, Site Build | SEC-INPUT-5 | N/A | COVERED |
| SEC-INPUT-6 | REQ-OUTPUT-020, REQ-CONTENT-080 | Site Build (TB-4) | SEC-INPUT-6 | N/A | COVERED |
| SEC-INPUT-7 | REQ-CONTENT-070 | Site Build (TB-4) | SEC-INPUT-7 | N/A | COVERED |
| SEC-SECRET-1 | REQ-PIPELINE-040, REQ-PIPELINE-050, REQ-HOST-030, REQ-INSTAGRAM-050, REQ-AUTHN-010 | Site Build, Static Hosting, OAuth helper (TB-4); `docs/ops/` | SEC-SECRET-1 | N/A | COVERED |
| SEC-SECRET-2 | REQ-PIPELINE-050, REQ-PIPELINE-060, REQ-PIPELINE-090 | Site Build pipeline (TB-4) | SEC-SECRET-2 | N/A | COVERED |
| SEC-SECRET-3 | REQ-INSTAGRAM-030, REQ-INSTAGRAM-050 | Instagram Feed Integration (TB-5), Static Hosting | SEC-SECRET-3 | N/A | PARTIALLY COVERED (email alert: PQ-56, answered in step 10 a third time; drafted by the next step 9 run) |
| SEC-CICD-1 | REQ-PIPELINE-010, REQ-PIPELINE-140, REQ-INSTAGRAM-040, REQ-INSTAGRAM-050 | Site Build, Static Hosting (TB-4) | SEC-CICD-1 | N/A | COVERED |
| SEC-CICD-2 | REQ-PIPELINE-030, REQ-EDIT-010, REQ-AUTHN-030 | Site Build pipeline, Editing Interface (TB-2, TB-4) | SEC-CICD-2 | N/A | PARTIALLY COVERED (Editing Interface files on the VPS: PQ-54, answered in step 10 a third time; drafted by the next step 9 run) |
| SEC-CICD-3 | REQ-PIPELINE-060, REQ-PREVIEW-010 | Site Build pipeline, Static Hosting | SEC-CICD-3 | N/A | COVERED |
| SEC-CICD-4 | REQ-PIPELINE-080, REQ-PIPELINE-130 | Site Build pipeline, Static Hosting (VPS) | SEC-CICD-4 | N/A | PARTIALLY COVERED (email alert: PQ-56, answered in step 10 a third time; drafted by the next step 9 run) |
| SEC-CICD-5 | REQ-PIPELINE-020 | Site Build pipeline, Static Hosting | SEC-CICD-5 | N/A | COVERED |
| SEC-CICD-6 | REQ-PIPELINE-090, REQ-INSTAGRAM-030 | Site Build pipeline (TB-4) | SEC-CICD-6 | N/A | COVERED |
| SEC-CICD-7 | REQ-STORE-020 | Content Store (TB-3) | SEC-CICD-7 | N/A | COVERED |
| SEC-CICD-8 | REQ-PIPELINE-100, REQ-PIPELINE-130 | Site Build pipeline, GitHub account | SEC-CICD-8 | N/A | COVERED |
| SEC-CICD-9 | REQ-PIPELINE-120 | Site Build, Editing Interface (DF-9) | SEC-CICD-9 | N/A | COVERED |
| T-31 (ARCHITECTURE.md Static Hosting: per-IP limits) | REQ-HTTP-070 | Static Hosting (TB-1, TB-2) | — | N/A | COVERED |
| DEP-1 to DEP-6, DEP-8 | All leaf issues (Constraints) | All components | DEP-1 to DEP-6, DEP-8 | N/A | COVERED (applied whenever a dependency is added) |
| DEP-7 | REQ-PIPELINE-030, REQ-EDIT-010, REQ-AUTHN-030 | Site Build pipeline, Editing Interface | DEP-7 | N/A | COVERED |

Totals: COVERED 106, PARTIALLY COVERED 11, BLOCKED 0, OUT OF SCOPE 3 (120 rows). Every leaf issue appears in at least one row.

## Hierarchy

```text
REQ-EPIC-001 Build the bilingual nina-laaf.de website from the specification
├── REQ-CONTENT-000 Content model and build-time content rules
│   ├── REQ-CONTENT-010 Define the Work and Image content model with its required-field rules
│   ├── REQ-CONTENT-020 Define the Text, CV entry, Download and Page content models
│   ├── REQ-CONTENT-050 Define the Exhibition entry and News item content models
│   ├── REQ-CONTENT-060 Define the Instagram settings: hidden posts and the feed switch
│   ├── REQ-CONTENT-030 Fail the Site Build and publish nothing when content violates the content model
│   ├── REQ-CONTENT-040 Exclude unpublished content from the production output
│   ├── REQ-CONTENT-070 Fail the Site Build when the Content Store checkout contains a symbolic link or special file
│   ├── REQ-CONTENT-080 Fail the publish for content files that are not Markdown, use front-matter fields outside the content model, or carry an unsafe slug
├── REQ-OUTPUT-000 Output encoding and safe rendering
│   ├── REQ-OUTPUT-010 HTML-encode editor text and render rich text only from the restricted format
│   ├── REQ-OUTPUT-020 Treat content fields as data and never evaluate generator syntax inside them
│   ├── REQ-OUTPUT-030 Allow only https: and mailto: links in content and open external links with noopener
│   ├── REQ-OUTPUT-040 Fail the build when a generated page contains a script other than the site's own script files
├── REQ-NAV-000 Navigation and languages
│   ├── REQ-NAV-020 Generate every page in English at / and German under /de/ with a language switch to the same page
│   ├── REQ-NAV-030 Show the other language's value, marked with its lang attribute, when a field's value is empty
│   ├── REQ-NAV-010 Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page
├── REQ-WORKS-000 Works
│   ├── REQ-WORKS-020 Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text
│   ├── REQ-WORKS-010 List all published works newest year first with Nina's order within a year, and show works in public space as a group
│   ├── REQ-WORKS-030 Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking
│   ├── REQ-WORKS-050 Open a work's images in a full-screen image viewer with buttons and arrow keys, and fall back to plain links without JavaScript
│   ├── REQ-WORKS-040 Link each work page to the pages of the published Exhibition entries linked to that work
├── REQ-IMAGES-000 Image processing in the Site Build
│   ├── REQ-IMAGES-010 Publish images only as responsive sizes of at most 2400 px on the long side, never as originals
│   ├── REQ-IMAGES-020 Remove embedded metadata from published images except the copyright notice and the creator
│   ├── REQ-IMAGES-040 Reject images larger than 40 megapixels before decoding them fully
│   ├── REQ-IMAGES-050 Recheck the type and size of every file the Site Build publishes and reject SVGs with active content
│   ├── REQ-IMAGES-030 Remove embedded metadata from published PDFs automatically, except the copyright notice and the creator
├── REQ-EXHIBITIONS-000 Exhibitions and News
│   ├── REQ-EXHIBITIONS-010 List each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors and external link
│   ├── REQ-EXHIBITIONS-030 Give each Exhibition entry its own page with its facts, linked works, description and installation images, and link it from the list
│   ├── REQ-EXHIBITIONS-020 Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive
│   ├── REQ-NEWS-010 List News items in their own section of the Exhibitions/News page, newest date first
├── REQ-CV-000 CV
│   ├── REQ-CV-010 Show the CV page with its six sections in order, hide empty sections and list entries newest year first
│   ├── REQ-CV-020 Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works
│   ├── REQ-CV-030 Let Nina hide a derived CV line or replace its generated wording
├── REQ-INSTAGRAM-000 Instagram
│   ├── REQ-INSTAGRAM-010 Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram
│   ├── REQ-INSTAGRAM-020 Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link
│   ├── REQ-INSTAGRAM-050 Keep the Instagram token in a file of the deploy account and refresh it monthly with a job under that account
│   ├── REQ-INSTAGRAM-030 Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks
│   ├── REQ-INSTAGRAM-040 Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails
├── REQ-CONSENT-000 Visitor statistics with Google Analytics and the consent banner
│   ├── REQ-CONSENT-010 Show the consent banner until a visitor chooses, store the choice strictly, and let the footer link reopen it
│   ├── REQ-PRIV-030 Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings
│   ├── REQ-CONSENT-020 Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage
│   ├── REQ-CONSENT-030 Set Google Analytics cookies for the host nina-laaf.de only, so that edit. and preview. never receive them
│   ├── REQ-CONSENT-040 Keep the Google tag loader and the measurement ID out of the preview output and the Editing Interface files
├── REQ-HTTP-000 Public site and HTTP boundary
│   ├── REQ-HTTP-010 Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS
│   ├── REQ-HTTP-020 Send the SEC-HTTP-2 Content Security Policy with only the Google Analytics origins on every public response
│   ├── REQ-HTTP-030 Send nosniff and forbid framing of nina-laaf.de by other origins
│   ├── REQ-HTTP-040 Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site
│   ├── REQ-HTTP-060 Renew the TLS certificates of all three hosts automatically from Let's Encrypt and detect failed renewals
│   ├── REQ-HTTP-070 Limit requests and connections per IP address on all three hosts
│   ├── REQ-HTTP-050 Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub
├── REQ-STORE-000 Content Store and code repository access
│   ├── REQ-STORE-010 Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record
│   ├── REQ-STORE-020 Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there
│   ├── REQ-STORE-030 Limit write access to dalini/nina-site to the maintainer
│   ├── REQ-STORE-040 Protect the history of main in dalini/nina-content and require verified commits there
│   ├── REQ-STORE-050 Mirror dalini/nina-content weekly to the VPS, keep 30 days outside the served directories, and test a restore yearly
│   ├── REQ-STORE-060 Document the purge of content from history and backups on a legal request, with a record that names nothing purged
├── REQ-PIPELINE-000 Secrets and build/deploy pipeline
│   ├── REQ-PIPELINE-010 Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key
│   ├── REQ-PIPELINE-020 Deploy only the Site Build's generated output
│   ├── REQ-PIPELINE-030 Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile
│   ├── REQ-PIPELINE-040 Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change
│   ├── REQ-PIPELINE-050 Keep the deploy, preview-deploy and content-read secrets in a production environment limited to main
│   ├── REQ-PIPELINE-060 Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key
│   ├── REQ-PREVIEW-010 Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins
│   ├── REQ-PIPELINE-070 Record what each production deploy was built from and restore the previous version without a content change
│   ├── REQ-PIPELINE-080 Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group
│   ├── REQ-PIPELINE-130 Check main of the Content Store every minute from the VPS and start the build through workflow_dispatch when it changed
│   ├── REQ-PIPELINE-090 Process Content Store files without access to the deploy keys or the Instagram token
│   ├── REQ-PIPELINE-140 Restrict the production and preview deploy keys to the deploy's file transfers and reads, with no shell, other command or forwarding
│   ├── REQ-PIPELINE-100 Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives
│   ├── REQ-PIPELINE-110 Email Ives when a build fails
│   ├── REQ-PIPELINE-120 Write the publish status file with only the content version, the result and the failing item and field
├── REQ-AUTHN-000 Editing Interface sign-in (OAuth helper)
│   ├── REQ-AUTHN-010 Sign editors in through the Deno OAuth helper, request only the allowed scopes, and release the token only to https://edit.nina-laaf.de
│   ├── REQ-AUTHN-020 Verify the OAuth state of every sign-in with __Host- cookies and reject callbacks with more than one state cookie
│   ├── REQ-AUTHN-030 Run the OAuth helper under its own account with a pinned Deno version, pinned dependencies and only the Deno permissions it needs
│   ├── REQ-AUTHN-040 Obtain a new token on each sign-in and keep tokens and session values out of URLs and logs
│   ├── REQ-AUTHN-050 Revoke all Editing Interface tokens of an editor at once, and drill it
├── REQ-EDIT-000 Editing Interface (Sveltia CMS)
│   ├── REQ-EDIT-010 Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN
│   ├── REQ-EDIT-020 Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings
│   ├── REQ-EDIT-030 Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks
│   ├── REQ-EDIT-040 Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order
│   ├── REQ-EDIT-050 Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload
│   ├── REQ-EDIT-060 Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup
│   ├── REQ-EDIT-070 Let Nina hide a single Instagram post and switch the feed off in the Editing Interface
│   ├── REQ-EDIT-080 Show who changed each content item and when, and restore an earlier version
│   ├── REQ-EDIT-090 Show Nina in the Editing Interface when her changes are not live yet and which item and field failed
├── REQ-MIGRATION-000 Legacy migration
│   ├── REQ-MIGRATION-010 Import the 53 legacy works with their German title, year, images and image captions into the Content Store
│   ├── REQ-MIGRATION-020 Import legacy works without a year as unpublished, publish the rest, and feature the newest work
│   ├── REQ-MIGRATION-030 Give every migrated image interim alt text made of the work title and the view number
├── REQ-LAYOUT-010 Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon
├── REQ-HOME-010 Show the artist's name and the featured work's image, linked to its work page, on the home page
├── REQ-HOME-020 Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page
├── REQ-TEXTS-010 List published Texts in Nina's order and give each its own page
├── REQ-DOWNLOADS-010 Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF
├── REQ-CONTACT-010 Show Nina's email address as a mailto link and link to galerie burster on the Contact page
├── REQ-LEGAL-010 Publish the Impressum and Datenschutzerklärung pages and link them from every page
├── REQ-HOST-010 Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly
├── REQ-HOST-020 Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root
├── REQ-HOST-030 Run each VPS job and the OAuth helper under its own unprivileged account that can read only its own secret file
├── REQ-PRIV-020 Delete nginx access logs after 14 days and keep them readable only by the maintainer
├── REQ-PERF-010 Meet Core Web Vitals "good" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run
├── REQ-PRIV-010 Request no third-party resource except the Google Analytics origins, and include no other analytics, tracking, cookies or pixels in any page
```

## Dependency Graph

Acyclic (checked by topological sort when the drafts were generated). Arrows point from an issue to the issues it depends on. Workstream and epic issues depend on nothing; they are created first so that leaves can be created with `--parent`.

```text
REQ-CONTENT-010 → (none)
REQ-CONTENT-020 → REQ-CONTENT-010
REQ-CONTENT-050 → REQ-CONTENT-010
REQ-CONTENT-060 → REQ-CONTENT-010
REQ-CONTENT-030 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-060
REQ-CONTENT-040 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050
REQ-CONTENT-070 → REQ-CONTENT-030
REQ-CONTENT-080 → REQ-CONTENT-030, REQ-CONTENT-050
REQ-OUTPUT-010 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050
REQ-OUTPUT-020 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-OUTPUT-030 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030
REQ-OUTPUT-040 → REQ-CONTENT-030, REQ-OUTPUT-010
REQ-LAYOUT-010 → (none)
REQ-NAV-020 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-LAYOUT-010
REQ-NAV-030 → REQ-NAV-020, REQ-OUTPUT-010
REQ-NAV-010 → REQ-NAV-020
REQ-WORKS-020 → REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010
REQ-IMAGES-010 → REQ-CONTENT-010
REQ-HOME-010 → REQ-CONTENT-010, REQ-CONTENT-030, REQ-NAV-020, REQ-IMAGES-010
REQ-WORKS-010 → REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020, REQ-IMAGES-010
REQ-WORKS-030 → REQ-WORKS-020, REQ-IMAGES-010
REQ-WORKS-050 → REQ-WORKS-030, REQ-IMAGES-010
REQ-IMAGES-020 → REQ-IMAGES-010
REQ-IMAGES-040 → REQ-IMAGES-010, REQ-CONTENT-030
REQ-IMAGES-050 → REQ-CONTENT-030
REQ-IMAGES-030 → REQ-CONTENT-020, REQ-IMAGES-050
REQ-EXHIBITIONS-010 → REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030
REQ-EXHIBITIONS-030 → REQ-EXHIBITIONS-010, REQ-WORKS-020, REQ-IMAGES-010, REQ-CONTENT-080
REQ-WORKS-040 → REQ-WORKS-020, REQ-EXHIBITIONS-030
REQ-EXHIBITIONS-020 → REQ-EXHIBITIONS-010, REQ-CONTENT-040
REQ-HOME-020 → REQ-HOME-010, REQ-EXHIBITIONS-020
REQ-NEWS-010 → REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010
REQ-CV-010 → REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010
REQ-CV-020 → REQ-CV-010, REQ-EXHIBITIONS-020, REQ-CONTENT-040
REQ-CV-030 → REQ-CV-020
REQ-TEXTS-010 → REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010, REQ-IMAGES-010
REQ-DOWNLOADS-010 → REQ-CV-010, REQ-CONTENT-040, REQ-IMAGES-050, REQ-IMAGES-030
REQ-CONTACT-010 → REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-030
REQ-LEGAL-010 → REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010
REQ-INSTAGRAM-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030
REQ-INSTAGRAM-020 → REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010
REQ-CONSENT-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-LEGAL-010
REQ-PRIV-030 → (none)
REQ-HOST-010 → (none)
REQ-HOST-020 → (none)
REQ-HOST-030 → REQ-HOST-020
REQ-PRIV-020 → REQ-HOST-020
REQ-HTTP-010 → REQ-HOST-020
REQ-HTTP-020 → REQ-HTTP-010, REQ-NAV-020
REQ-CONSENT-020 → REQ-CONSENT-010, REQ-PRIV-030, REQ-HTTP-020, REQ-OUTPUT-040
REQ-PERF-010 → REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-CONSENT-020
REQ-PRIV-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-CONSENT-020
REQ-CONSENT-030 → REQ-CONSENT-020
REQ-HTTP-030 → REQ-HTTP-010
REQ-HTTP-040 → REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030
REQ-HTTP-060 → REQ-HTTP-010, REQ-HTTP-040
REQ-HTTP-070 → REQ-HTTP-010, REQ-HTTP-040
REQ-STORE-010 → (none)
REQ-STORE-020 → (none)
REQ-STORE-030 → (none)
REQ-STORE-040 → REQ-STORE-010
REQ-STORE-050 → REQ-HOST-030, REQ-STORE-010
REQ-STORE-060 → REQ-STORE-040, REQ-STORE-050
REQ-PIPELINE-010 → REQ-CONTENT-030, REQ-HOST-020, REQ-STORE-010
REQ-PIPELINE-020 → REQ-PIPELINE-010
REQ-PIPELINE-030 → REQ-PIPELINE-010
REQ-PIPELINE-040 → REQ-PIPELINE-010
REQ-PIPELINE-050 → REQ-PIPELINE-010
REQ-PIPELINE-060 → REQ-PIPELINE-010, REQ-CONTENT-040, REQ-PIPELINE-050
REQ-PREVIEW-010 → REQ-HTTP-040, REQ-PIPELINE-060
REQ-PIPELINE-070 → REQ-PIPELINE-010
REQ-PIPELINE-080 → REQ-PIPELINE-010
REQ-PIPELINE-130 → REQ-PIPELINE-080, REQ-HOST-030
REQ-PIPELINE-090 → REQ-PIPELINE-010, REQ-PIPELINE-050
REQ-PIPELINE-140 → REQ-PIPELINE-010, REQ-PIPELINE-060, REQ-HOST-030
REQ-INSTAGRAM-050 → REQ-HOST-030
REQ-INSTAGRAM-030 → REQ-INSTAGRAM-020, REQ-IMAGES-020, REQ-IMAGES-040, REQ-PIPELINE-090, REQ-INSTAGRAM-050
REQ-INSTAGRAM-040 → REQ-INSTAGRAM-030, REQ-PIPELINE-010
REQ-PIPELINE-100 → REQ-PIPELINE-010
REQ-PIPELINE-110 → REQ-PIPELINE-010
REQ-PIPELINE-120 → REQ-PIPELINE-010, REQ-CONTENT-030
REQ-EDIT-010 → REQ-HTTP-040, REQ-STORE-030
REQ-CONSENT-040 → REQ-CONSENT-020, REQ-PIPELINE-060, REQ-EDIT-010, REQ-HTTP-040
REQ-AUTHN-010 → REQ-EDIT-010, REQ-HTTP-040
REQ-AUTHN-020 → REQ-AUTHN-010
REQ-AUTHN-030 → REQ-AUTHN-010, REQ-HOST-030
REQ-AUTHN-040 → REQ-AUTHN-020, REQ-PRIV-020
REQ-AUTHN-050 → REQ-AUTHN-010
REQ-HTTP-050 → REQ-HTTP-040, REQ-EDIT-010
REQ-EDIT-020 → REQ-EDIT-010, REQ-CONTENT-010
REQ-EDIT-030 → REQ-EDIT-010, REQ-CONTENT-050
REQ-EDIT-040 → REQ-EDIT-010, REQ-CONTENT-020
REQ-EDIT-050 → REQ-EDIT-020, REQ-EDIT-040
REQ-EDIT-060 → REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
REQ-EDIT-070 → REQ-EDIT-010, REQ-CONTENT-060
REQ-EDIT-080 → REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
REQ-EDIT-090 → REQ-PIPELINE-120, REQ-EDIT-010, REQ-HTTP-050
REQ-MIGRATION-010 → REQ-CONTENT-010
REQ-MIGRATION-020 → REQ-MIGRATION-010, REQ-CONTENT-040
REQ-MIGRATION-030 → REQ-MIGRATION-010
```

## Manifest

File names are `NNN-<ID>.md`, where `NNN` is the zero-based position in the Topological Creation Order (`000` is the epic). Effort is engineer-days; LOC is changed human-authored lines, excluding generated files and lockfiles. Recommended models (verified 2026-10-31, Planning Rules): Claude Opus 5.5 for 37 leaves, Claude Sonnet 5.5 for 63; none for Claude Fable or OpenAI GPT.

| # | ID | Title | Parent | Depends on | Effort | LOC | Model | Model rationale |
|---|---|---|---|---|---|---|---|---|
| 000 | REQ-EPIC-001 | Build the bilingual nina-laaf.de website from the specification | — | — | — | — | — | Parent issue, no implementation |
| 001 | REQ-CONTENT-000 | Content model and build-time content rules | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 002 | REQ-OUTPUT-000 | Output encoding and safe rendering | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 003 | REQ-NAV-000 | Navigation and languages | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 004 | REQ-WORKS-000 | Works | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 005 | REQ-IMAGES-000 | Image processing in the Site Build | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 006 | REQ-EXHIBITIONS-000 | Exhibitions and News | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 007 | REQ-CV-000 | CV | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 008 | REQ-INSTAGRAM-000 | Instagram | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 009 | REQ-CONSENT-000 | Visitor statistics with Google Analytics and the consent banner | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 010 | REQ-HTTP-000 | Public site and HTTP boundary | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 011 | REQ-STORE-000 | Content Store and code repository access | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 012 | REQ-PIPELINE-000 | Secrets and build/deploy pipeline | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 013 | REQ-AUTHN-000 | Editing Interface sign-in (OAuth helper) | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 014 | REQ-EDIT-000 | Editing Interface (Sveltia CMS) | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 015 | REQ-MIGRATION-000 | Legacy migration | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 016 | REQ-CONTENT-010 | Define the Work and Image content model with its required-field rules | REQ-CONTENT-000 | — | 1–1.5 days | 250–500 | Claude Opus 5.5 (`claude-opus-5-5`) | It sets the shared rule set and portable format that every page, validation and editing issue reads; a modelling mistake here spreads everywhere. |
| 017 | REQ-CONTENT-020 | Define the Text, CV entry, Download and Page content models | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | It follows the pattern REQ-CONTENT-010 sets; fields and rules come straight from FR-5 to FR-7 and FR-10.6. |
| 018 | REQ-CONTENT-050 | Define the Exhibition entry and News item content models | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A field list taken directly from FR-4 and FR-10.6, following the REQ-CONTENT-010 pattern. |
| 019 | REQ-CONTENT-060 | Define the Instagram settings: hidden posts and the feed switch | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small, fully specified settings item. |
| 020 | REQ-CONTENT-030 | Fail the Site Build and publish nothing when content violates the content model | REQ-CONTENT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-060 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | It wires the existing rule set into a fail-closed first stage; SEC-INPUT-3 fully specifies the behavior. |
| 021 | REQ-CONTENT-040 | Exclude unpublished content from the production output | REQ-CONTENT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050 | 0.5–1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`) | A confidentiality control with subtle failure modes: generators copy resources and build sitemaps and feeds on their own (T-25, T-29). |
| 022 | REQ-CONTENT-070 | Fail the Site Build when the Content Store checkout contains a symbolic link or special file | REQ-CONTENT-000 | REQ-CONTENT-030 | 0.5 day | 60–150 | Claude Opus 5.5 (`claude-opus-5-5`) | Trust-boundary control on the build machine; link handling in checkouts has edge cases (submodules, links to links) that need care. |
| 023 | REQ-CONTENT-080 | Fail the publish for content files that are not Markdown, use front-matter fields outside the content model, or carry an unsafe slug | REQ-CONTENT-000 | REQ-CONTENT-030, REQ-CONTENT-050 | 0.5–1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Generator-reserved fields and content formats are a Hugo-internals trust boundary (T-77); a gap lets a direct write take over the Impressum's URL or bring raw HTML into a page. |
| 024 | REQ-OUTPUT-010 | HTML-encode editor text and render rich text only from the restricted format | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-critical encoding and sanitizing that needs careful use of the renderer and a hostile test corpus. |
| 025 | REQ-OUTPUT-020 | Treat content fields as data and never evaluate generator syntax inside them | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Template-injection prevention depends on generator internals and how content is loaded (T-18). |
| 026 | REQ-OUTPUT-030 | Allow only https: and mailto: links in content and open external links with noopener | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small allow-list with a known bypass corpus, fully specified by SEC-OUT-2. |
| 027 | REQ-OUTPUT-040 | Fail the build when a generated page contains a script other than the site's own script files | REQ-OUTPUT-000 | REQ-CONTENT-030, REQ-OUTPUT-010 | 0.5–1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | The last barrier against a Google or Tag Manager script injected into a page (T-76) must parse real HTML output robustly for both builds. |
| 028 | REQ-LAYOUT-010 | Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon | REQ-EPIC-001 | — | 1–1.5 days | 300–600 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Translating a complete token table and component list into one stylesheet and base template is well specified. |
| 029 | REQ-NAV-020 | Generate every page in English at / and German under /de/ with a language switch to the same page | REQ-NAV-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-LAYOUT-010 | 1–2 days | 300–700 | Claude Opus 5.5 (`claude-opus-5-5`) | It defines the page pairing and URL structure every page issue builds on, the largest structural piece of the public site. |
| 030 | REQ-NAV-030 | Show the other language's value, marked with its lang attribute, when a field's value is empty | REQ-NAV-000 | REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | One shared helper applied consistently; FR-1.7 defines the behavior exactly. |
| 031 | REQ-NAV-010 | Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page | REQ-NAV-000 | REQ-NAV-020 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine shared-layout work with fixed targets and DESIGN.md states. |
| 032 | REQ-WORKS-020 | Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text | REQ-WORKS-000 | REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template rendering of defined fields with optional-field handling. |
| 033 | REQ-IMAGES-010 | Publish images only as responsive sizes of at most 2400 px on the long side, never as originals | REQ-IMAGES-000 | REQ-CONTENT-010 | 1–2 days | 200–500 | Claude Opus 5.5 (`claude-opus-5-5`) | Generator resource handling can leak originals silently (T-25); getting sizes, caps and output hygiene right needs care. |
| 034 | REQ-HOME-010 | Show the artist's name and the featured work's image, linked to its work page, on the home page | REQ-EPIC-001 | REQ-CONTENT-010, REQ-CONTENT-030, REQ-NAV-020, REQ-IMAGES-010 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A single template section driven by one content reference. |
| 035 | REQ-WORKS-010 | List all published works newest year first with Nina's order within a year, and show works in public space as a group | REQ-WORKS-000 | REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020, REQ-IMAGES-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | List and filtered view from fields already in the model; layout fixed by DESIGN.md. |
| 036 | REQ-WORKS-030 | Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking | REQ-WORKS-000 | REQ-WORKS-020, REQ-IMAGES-010 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Ordered image rendering with labels; the image processing itself is a separate issue. |
| 037 | REQ-WORKS-050 | Open a work's images in a full-screen image viewer with buttons and arrow keys, and fall back to plain links without JavaScript | REQ-WORKS-000 | REQ-WORKS-030, REQ-IMAGES-010 | 1–1.5 days | 250–500 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small vanilla-JavaScript component fully described by FR-3.11 and DESIGN.md, with clear keyboard and focus tests. |
| 038 | REQ-IMAGES-020 | Remove embedded metadata from published images except the copyright notice and the creator | REQ-IMAGES-000 | REQ-IMAGES-010 | 0.5–1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Privacy control with format-specific metadata blocks (EXIF, XMP, IPTC) and a keep-list that is easy to get wrong. |
| 039 | REQ-IMAGES-040 | Reject images larger than 40 megapixels before decoding them fully | REQ-IMAGES-000 | REQ-IMAGES-010, REQ-CONTENT-030 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Denial-of-service guard that must run before the generator's own decoder and handle malformed headers safely. |
| 040 | REQ-IMAGES-050 | Recheck the type and size of every file the Site Build publishes and reject SVGs with active content | REQ-IMAGES-000 | REQ-CONTENT-030 | 0.5–1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Security recheck with polyglot and SVG edge cases; must not trust extensions. |
| 041 | REQ-IMAGES-030 | Remove embedded metadata from published PDFs automatically, except the copyright notice and the creator | REQ-IMAGES-000 | REQ-CONTENT-020, REQ-IMAGES-050 | 1 day | 150–350 | Claude Opus 5.5 (`claude-opus-5-5`) | A privacy control over a complex file format (document info, XMP, embedded images) with a keep-list, where a publish must never fail because of metadata. |
| 042 | REQ-EXHIBITIONS-010 | List each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors and external link | REQ-EXHIBITIONS-000 | REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template rendering of defined fields with optional-field handling. |
| 043 | REQ-EXHIBITIONS-030 | Give each Exhibition entry its own page with its facts, linked works, description and installation images, and link it from the list | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-010, REQ-WORKS-020, REQ-IMAGES-010, REQ-CONTENT-080 | 1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template work over fields the model already holds, following the DESIGN.md Exhibition entry page component. |
| 044 | REQ-WORKS-040 | Link each work page to the pages of the published Exhibition entries linked to that work | REQ-WORKS-000 | REQ-WORKS-020, REQ-EXHIBITIONS-030 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A reverse lookup over a relation that the model already holds. |
| 045 | REQ-EXHIBITIONS-020 | Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-010, REQ-CONTENT-040 | 0.5–1 day | 120–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Date-boundary logic fully defined by FR-4.1, with clear test cases. |
| 046 | REQ-HOME-020 | Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page | REQ-EPIC-001 | REQ-HOME-010, REQ-EXHIBITIONS-020 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small selection rule on top of the grouping in REQ-EXHIBITIONS-020. |
| 047 | REQ-NEWS-010 | List News items in their own section of the Exhibitions/News page, newest date first | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine list with optional fields. |
| 048 | REQ-CV-010 | Show the CV page with its six sections in order, hide empty sections and list entries newest year first | REQ-CV-000 | REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine grouping of entries into six fixed sections. |
| 049 | REQ-CV-020 | Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works | REQ-CV-000 | REQ-CV-010, REQ-EXHIBITIONS-020, REQ-CONTENT-040 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A clear mapping rule on top of the grouping function. |
| 050 | REQ-CV-030 | Let Nina hide a derived CV line or replace its generated wording | REQ-CV-000 | REQ-CV-020 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two small, fully specified rules on derived lines. |
| 051 | REQ-TEXTS-010 | List published Texts in Nina's order and give each its own page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010, REQ-IMAGES-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine list and detail pages. |
| 052 | REQ-DOWNLOADS-010 | Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF | REQ-EPIC-001 | REQ-CV-010, REQ-CONTENT-040, REQ-IMAGES-050, REQ-IMAGES-030 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A routine link list with a text label. |
| 053 | REQ-CONTACT-010 | Show Nina's email address as a mailto link and link to galerie burster on the Contact page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 50–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A single page with two content-driven links. |
| 054 | REQ-LEGAL-010 | Publish the Impressum and Datenschutzerklärung pages and link them from every page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two Page renders and footer links; the wording is content. |
| 055 | REQ-INSTAGRAM-010 | Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram | REQ-INSTAGRAM-000 | REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A plain link plus a network check. |
| 056 | REQ-INSTAGRAM-020 | Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link | REQ-INSTAGRAM-000 | REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Rendering from a neutral list with a small selection rule; the risky fetch is a separate issue. |
| 057 | REQ-CONSENT-010 | Show the consent banner until a visitor chooses, store the choice strictly, and let the footer link reopen it | REQ-CONSENT-000 | REQ-LAYOUT-010, REQ-NAV-020, REQ-LEGAL-010 | 1–1.5 days | 200–400 | Claude Opus 5.5 (`claude-opus-5-5`) | First-party script that reads browser storage and decides whether a visitor has consented; a parsing mistake grants consent the visitor never gave (T-72). |
| 058 | REQ-PRIV-030 | Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings | REQ-CONSENT-000 | — | 0.5 day | 0–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A settings checklist and its yearly review, fully specified by SEC-PRIV-7; no code. |
| 059 | REQ-HOST-010 | Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly | REQ-EPIC-001 | — | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A checklist and its first review record; no code beyond a DNS check script. |
| 060 | REQ-HOST-020 | Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root | REQ-EPIC-001 | — | 1–1.5 days | 80–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Server hardening is security-sensitive and a mistake (e.g. locking out SSH) is costly to undo. |
| 061 | REQ-HOST-030 | Run each VPS job and the OAuth helper under its own unprivileged account that can read only its own secret file | REQ-EPIC-001 | REQ-HOST-020 | 1 day | 60–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Account and permission design on the VPS that every job and secret depends on; mistakes are silent and widen what one flaw can reach. |
| 062 | REQ-PRIV-020 | Delete nginx access logs after 14 days and keep them readable only by the maintainer | REQ-EPIC-001 | REQ-HOST-020 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Log rotation and file permissions with a clear retention value. |
| 063 | REQ-HTTP-010 | Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS | REQ-HTTP-000 | REQ-HOST-020 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Host configuration and checks fully specified by SEC-HTTP-1. |
| 064 | REQ-HTTP-020 | Send the SEC-HTTP-2 Content Security Policy with only the Google Analytics origins on every public response | REQ-HTTP-000 | REQ-HTTP-010, REQ-NAV-020 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`) | A strict CSP has to be fitted to the generator's output (inline styles, scripts) without weakening it. |
| 065 | REQ-CONSENT-020 | Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage | REQ-CONSENT-000 | REQ-CONSENT-010, REQ-PRIV-030, REQ-HTTP-020, REQ-OUTPUT-040 | 1–1.5 days | 150–350 | Claude Opus 5.5 (`claude-opus-5-5`) | Consent Mode depends on the order of first-party and Google code; a mistake sets cookies or sends full measurements before consent (T-71) and has to be proven by network-level browser tests. |
| 066 | REQ-PERF-010 | Meet Core Web Vitals "good" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run | REQ-EPIC-001 | REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-CONSENT-020 | 1 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Measurement and tuning against fixed numeric budgets. |
| 067 | REQ-PRIV-010 | Request no third-party resource except the Google Analytics origins, and include no other analytics, tracking, cookies or pixels in any page | REQ-EPIC-001 | REQ-LAYOUT-010, REQ-NAV-020, REQ-CONSENT-020 | 1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A browser network-recording test over a known list of page types; the rule is unambiguous. |
| 068 | REQ-CONSENT-030 | Set Google Analytics cookies for the host nina-laaf.de only, so that edit. and preview. never receive them | REQ-CONSENT-000 | REQ-CONSENT-020 | 0.5 day | 20–80 | Claude Opus 5.5 (`claude-opus-5-5`) | The setting must be verified against how browsers scope cookies (RFC 6265), and the issue must stop rather than ship a mechanism that does not meet SEC-PRIV-5. |
| 069 | REQ-HTTP-030 | Send nosniff and forbid framing of nina-laaf.de by other origins | REQ-HTTP-000 | REQ-HTTP-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two headers and a framing test. |
| 070 | REQ-HTTP-040 | Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Reuses the public host's header set on two more hosts. |
| 071 | REQ-HTTP-060 | Renew the TLS certificates of all three hosts automatically from Let's Encrypt and detect failed renewals | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-040 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Standard renewal job and expiry check once the source and alert channel are chosen. |
| 072 | REQ-HTTP-070 | Limit requests and connections per IP address on all three hosts | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-040 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Standard nginx limits once the values are set. |
| 073 | REQ-STORE-010 | Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record | REQ-STORE-000 | — | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Repository settings and a record file, fully specified by SEC-AUTHZ-1, -2 and -4. |
| 074 | REQ-STORE-020 | Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there | REQ-STORE-000 | — | 0.5 day | 0–20 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | One settings change and one test. |
| 075 | REQ-STORE-030 | Limit write access to dalini/nina-site to the maintainer | REQ-STORE-000 | — | 0.5 day | 0–20 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Repository settings with one negative test. |
| 076 | REQ-STORE-040 | Protect the history of main in dalini/nina-content and require verified commits there | REQ-STORE-000 | REQ-STORE-010 | 0.5–1 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Branch protection settings with negative tests, plus one documented check whose result decides whether a setting stays. |
| 077 | REQ-STORE-050 | Mirror dalini/nina-content weekly to the VPS, keep 30 days outside the served directories, and test a restore yearly | REQ-STORE-000 | REQ-HOST-030, REQ-STORE-010 | 1 day | 60–180 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A cron mirror with retention and permissions, plus a yearly restore drill; values and locations are fixed by SEC-AVAIL-1. |
| 078 | REQ-STORE-060 | Document the purge of content from history and backups on a legal request, with a record that names nothing purged | REQ-STORE-000 | REQ-STORE-040, REQ-STORE-050 | 0.5 day | 0–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A runbook and a record format; the rule fixes who may do it and what the record holds. |
| 079 | REQ-PIPELINE-010 | Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key | REQ-PIPELINE-000 | REQ-CONTENT-030, REQ-HOST-020, REQ-STORE-010 | 1–2 days | 100–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Least-privilege deploy identity, host-key pinning and fail-closed gating across GitHub Actions and the VPS. |
| 080 | REQ-PIPELINE-020 | Deploy only the Site Build's generated output | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Point the upload at the output directory and add a post-deploy path check. |
| 081 | REQ-PIPELINE-030 | Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Mechanical pinning and checksum verification with a lint check. |
| 082 | REQ-PIPELINE-040 | Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Secret scanning, masking and a fail-closed gate with platform features. |
| 083 | REQ-PIPELINE-050 | Keep the deploy, preview-deploy and content-read secrets in a production environment limited to main | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | GitHub environment settings with a negative test from another branch; fully specified by SEC-SECRET-1 and SEC-SECRET-2. |
| 084 | REQ-PIPELINE-060 | Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-CONTENT-040, REQ-PIPELINE-050 | 0.5–1 day | 60–180 | Claude Opus 5.5 (`claude-opus-5-5`) | Key separation between two outputs from one run is easy to get subtly wrong (T-20, T-21). |
| 085 | REQ-PREVIEW-010 | Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins | REQ-PIPELINE-000 | REQ-HTTP-040, REQ-PIPELINE-060 | 0.5–1 day | 30–100 | Claude Opus 5.5 (`claude-opus-5-5`) | Authentication setup with password-storage and guessing-limit requirements. |
| 086 | REQ-PIPELINE-070 | Record what each production deploy was built from and restore the previous version without a content change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A deploy record and a restore drill using the release layout from REQ-PIPELINE-010. |
| 087 | REQ-PIPELINE-080 | Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Workflow trigger configuration fully specified by SEC-CICD-4. |
| 088 | REQ-PIPELINE-130 | Check main of the Content Store every minute from the VPS and start the build through workflow_dispatch when it changed | REQ-PIPELINE-000 | REQ-PIPELINE-080, REQ-HOST-030 | 1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | A small job that holds a trigger token, must never pass content into a command, and is the only path that meets the 15-minute limit. |
| 089 | REQ-PIPELINE-090 | Process Content Store files without access to the deploy keys or the Instagram token | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-PIPELINE-050 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`) | Job isolation for secrets in GitHub Actions has subtle leaks (shared workspaces, artifacts, environment inheritance). |
| 090 | REQ-PIPELINE-140 | Restrict the production and preview deploy keys to the deploy's file transfers and reads, with no shell, other command or forwarding | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-PIPELINE-060, REQ-HOST-030 | 0.5–1 day | 40–120 | Claude Opus 5.5 (`claude-opus-5-5`) | SSH key restrictions (forced commands, path limits) are easy to get subtly wrong, and a gap turns a leaked key into a persistent foothold (T-81). |
| 091 | REQ-INSTAGRAM-050 | Keep the Instagram token in a file of the deploy account and refresh it monthly with a job under that account | REQ-INSTAGRAM-000 | REQ-HOST-030 | 0.5–1 day | 60–180 | Claude Opus 5.5 (`claude-opus-5-5`) | Secret handling on the VPS across a cron job, file permissions and an outbound API call, where a mistake leaks a token or silently stops the feed. |
| 092 | REQ-INSTAGRAM-030 | Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks | REQ-INSTAGRAM-000 | REQ-INSTAGRAM-020, REQ-IMAGES-020, REQ-IMAGES-040, REQ-PIPELINE-090, REQ-INSTAGRAM-050 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-sensitive integration with untrusted external data, a secret and per-item failure handling. |
| 093 | REQ-INSTAGRAM-040 | Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails | REQ-INSTAGRAM-000 | REQ-INSTAGRAM-030, REQ-PIPELINE-010 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | It reads back across a trust boundary from hosting and must fail safe in several ways. |
| 094 | REQ-PIPELINE-100 | Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Configuration and settings with a simple test. |
| 095 | REQ-PIPELINE-110 | Email Ives when a build fails | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 0–30 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Notification settings and two forced-failure tests. |
| 096 | REQ-PIPELINE-120 | Write the publish status file with only the content version, the result and the failing item and field | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-CONTENT-030 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small writer with a fixed schema; the open access question is excluded. |
| 097 | REQ-EDIT-010 | Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN | REQ-EDIT-000 | REQ-HTTP-040, REQ-STORE-030 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`) | Supply-chain control for code that runs with every editor's token. |
| 098 | REQ-CONSENT-040 | Keep the Google tag loader and the measurement ID out of the preview output and the Editing Interface files | REQ-CONSENT-000 | REQ-CONSENT-020, REQ-PIPELINE-060, REQ-EDIT-010, REQ-HTTP-040 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A build-time search over two outputs plus a browser check on two hosts; the rule is unambiguous. |
| 099 | REQ-AUTHN-010 | Sign editors in through the Deno OAuth helper, request only the allowed scopes, and release the token only to https://edit.nina-laaf.de | REQ-AUTHN-000 | REQ-EDIT-010, REQ-HTTP-040 | 1.5–2 days | 300–600 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-critical port of an OAuth relay where upstream behavior (empty allowed-domains, host-only comparison) must be changed, not copied (T-80, T-43). |
| 100 | REQ-AUTHN-020 | Verify the OAuth state of every sign-in with __Host- cookies and reject callbacks with more than one state cookie | REQ-AUTHN-000 | REQ-AUTHN-010 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Cookie-scoping subtleties across hosts of one site (T-78) and CSRF on the sign-in flow need careful, adversarial tests. |
| 101 | REQ-AUTHN-030 | Run the OAuth helper under its own account with a pinned Deno version, pinned dependencies and only the Deno permissions it needs | REQ-AUTHN-000 | REQ-AUTHN-010, REQ-HOST-030 | 0.5–1 day | 40–120 | Claude Opus 5.5 (`claude-opus-5-5`) | Least-privilege runtime configuration for the only internet-facing server-side code (T-80, T-61). |
| 102 | REQ-AUTHN-040 | Obtain a new token on each sign-in and keep tokens and session values out of URLs and logs | REQ-AUTHN-000 | REQ-AUTHN-020, REQ-PRIV-020 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Verification-heavy work over known places (URLs, nginx and helper logs) with a documented, accepted exception. |
| 103 | REQ-AUTHN-050 | Revoke all Editing Interface tokens of an editor at once, and drill it | REQ-AUTHN-000 | REQ-AUTHN-010 | 0.5 day | 0–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A runbook and a drill using GitHub's own revocation; fully specified by SEC-AUTHN-5. |
| 104 | REQ-HTTP-050 | Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub | REQ-HTTP-000 | REQ-HTTP-040, REQ-EDIT-010 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`) | Fitting a strict CSP to a third-party single-page app without weakening it protects the editors' tokens. |
| 105 | REQ-EDIT-020 | Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-010 | 1–2 days | 150–400 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Declarative CMS configuration following the documented model. |
| 106 | REQ-EDIT-030 | Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-050 | 1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Declarative CMS configuration following the documented model. |
| 107 | REQ-EDIT-040 | Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-020 | 1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Declarative CMS configuration following the documented model. |
| 108 | REQ-EDIT-050 | Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-040 | 0.5–1 day | 50–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-relevant validation in a third-party UI, where support for content sniffing and pixel checks must be verified. |
| 109 | REQ-EDIT-060 | Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`) | Security verification across every view of a third-party UI, with configuration to restrict formats. |
| 110 | REQ-EDIT-070 | Let Nina hide a single Instagram post and switch the feed off in the Editing Interface | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-060 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small settings form. |
| 111 | REQ-EDIT-080 | Show who changed each content item and when, and restore an earlier version | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | 0.5–1 day | 30–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Depends on a capability of the editing system that must be verified, with a careful restore path. |
| 112 | REQ-EDIT-090 | Show Nina in the Editing Interface when her changes are not live yet and which item and field failed | REQ-EDIT-000 | REQ-PIPELINE-120, REQ-EDIT-010, REQ-HTTP-050 | 1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Browser code on the editing origin that reads the public status file and the editor's own latest save, must show untrusted text safely, and runs where editor tokens live (T-45, T-60). |
| 113 | REQ-MIGRATION-010 | Import the 53 legacy works with their German title, year, images and image captions into the Content Store | REQ-MIGRATION-000 | REQ-CONTENT-010 | 1–1.5 days | 200–450 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A mapping script from a known, uniform source format. |
| 114 | REQ-MIGRATION-020 | Import legacy works without a year as unpublished, publish the rest, and feature the newest work | REQ-MIGRATION-000 | REQ-MIGRATION-010, REQ-CONTENT-040 | 0.5 day | 40–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two fixed rules added to the import. |
| 115 | REQ-MIGRATION-030 | Give every migrated image interim alt text made of the work title and the view number | REQ-MIGRATION-000 | REQ-MIGRATION-010 | 0.5 day | 20–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A single string rule added to the import. |

Total for the 100 leaves: 62.5–90 engineer-days.

## Topological Creation Order

Epic first, then the workstreams, then the leaves with every upstream issue before the issues that depend on it. In CREATE mode each issue is created with `--parent` set to its parent's issue, then every `{{ISSUE_URL:<ID>}}` placeholder is replaced with the captured URL.

1. REQ-EPIC-001
2. REQ-CONTENT-000
3. REQ-OUTPUT-000
4. REQ-NAV-000
5. REQ-WORKS-000
6. REQ-IMAGES-000
7. REQ-EXHIBITIONS-000
8. REQ-CV-000
9. REQ-INSTAGRAM-000
10. REQ-CONSENT-000
11. REQ-HTTP-000
12. REQ-STORE-000
13. REQ-PIPELINE-000
14. REQ-AUTHN-000
15. REQ-EDIT-000
16. REQ-MIGRATION-000
17. REQ-CONTENT-010
18. REQ-CONTENT-020
19. REQ-CONTENT-050
20. REQ-CONTENT-060
21. REQ-CONTENT-030
22. REQ-CONTENT-040
23. REQ-CONTENT-070
24. REQ-CONTENT-080
25. REQ-OUTPUT-010
26. REQ-OUTPUT-020
27. REQ-OUTPUT-030
28. REQ-OUTPUT-040
29. REQ-LAYOUT-010
30. REQ-NAV-020
31. REQ-NAV-030
32. REQ-NAV-010
33. REQ-WORKS-020
34. REQ-IMAGES-010
35. REQ-HOME-010
36. REQ-WORKS-010
37. REQ-WORKS-030
38. REQ-WORKS-050
39. REQ-IMAGES-020
40. REQ-IMAGES-040
41. REQ-IMAGES-050
42. REQ-IMAGES-030
43. REQ-EXHIBITIONS-010
44. REQ-EXHIBITIONS-030
45. REQ-WORKS-040
46. REQ-EXHIBITIONS-020
47. REQ-HOME-020
48. REQ-NEWS-010
49. REQ-CV-010
50. REQ-CV-020
51. REQ-CV-030
52. REQ-TEXTS-010
53. REQ-DOWNLOADS-010
54. REQ-CONTACT-010
55. REQ-LEGAL-010
56. REQ-INSTAGRAM-010
57. REQ-INSTAGRAM-020
58. REQ-CONSENT-010
59. REQ-PRIV-030
60. REQ-HOST-010
61. REQ-HOST-020
62. REQ-HOST-030
63. REQ-PRIV-020
64. REQ-HTTP-010
65. REQ-HTTP-020
66. REQ-CONSENT-020
67. REQ-PERF-010
68. REQ-PRIV-010
69. REQ-CONSENT-030
70. REQ-HTTP-030
71. REQ-HTTP-040
72. REQ-HTTP-060
73. REQ-HTTP-070
74. REQ-STORE-010
75. REQ-STORE-020
76. REQ-STORE-030
77. REQ-STORE-040
78. REQ-STORE-050
79. REQ-STORE-060
80. REQ-PIPELINE-010
81. REQ-PIPELINE-020
82. REQ-PIPELINE-030
83. REQ-PIPELINE-040
84. REQ-PIPELINE-050
85. REQ-PIPELINE-060
86. REQ-PREVIEW-010
87. REQ-PIPELINE-070
88. REQ-PIPELINE-080
89. REQ-PIPELINE-130
90. REQ-PIPELINE-090
91. REQ-PIPELINE-140
92. REQ-INSTAGRAM-050
93. REQ-INSTAGRAM-030
94. REQ-INSTAGRAM-040
95. REQ-PIPELINE-100
96. REQ-PIPELINE-110
97. REQ-PIPELINE-120
98. REQ-EDIT-010
99. REQ-CONSENT-040
100. REQ-AUTHN-010
101. REQ-AUTHN-020
102. REQ-AUTHN-030
103. REQ-AUTHN-040
104. REQ-AUTHN-050
105. REQ-HTTP-050
106. REQ-EDIT-020
107. REQ-EDIT-030
108. REQ-EDIT-040
109. REQ-EDIT-050
110. REQ-EDIT-060
111. REQ-EDIT-070
112. REQ-EDIT-080
113. REQ-EDIT-090
114. REQ-MIGRATION-010
115. REQ-MIGRATION-020
116. REQ-MIGRATION-030

## Proposed Commands (not run)

Pre-flight checks for CREATE mode: `gh auth status`; confirm the target repository is `dalini/nina-website` (the specification repository, as in the earlier runs); confirm that `gh issue create` in the installed version supports `--body-file` and `--parent` (otherwise link sub-issues through the GitHub API); confirm no existing issue reuses a planning ID for different scope. PQ-54 to PQ-59 are answered (step 10 a third time), but their scope is not drafted yet: run step 9 in `DRAFT_ONLY` first; a CREATE run is not allowed while any Blocked Scope row is undrafted (prompt: stop before any GitHub change).

```sh
gh issue create --title "[REQ-EPIC-001] Build the bilingual nina-laaf.de website from the specification" --body-file ".planning/github-issues/000-REQ-EPIC-001.md"
gh issue create --title "[REQ-CONTENT-000] Content model and build-time content rules" --body-file ".planning/github-issues/001-REQ-CONTENT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-OUTPUT-000] Output encoding and safe rendering" --body-file ".planning/github-issues/002-REQ-OUTPUT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NAV-000] Navigation and languages" --body-file ".planning/github-issues/003-REQ-NAV-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-WORKS-000] Works" --body-file ".planning/github-issues/004-REQ-WORKS-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-IMAGES-000] Image processing in the Site Build" --body-file ".planning/github-issues/005-REQ-IMAGES-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-EXHIBITIONS-000] Exhibitions and News" --body-file ".planning/github-issues/006-REQ-EXHIBITIONS-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CV-000] CV" --body-file ".planning/github-issues/007-REQ-CV-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-INSTAGRAM-000] Instagram" --body-file ".planning/github-issues/008-REQ-INSTAGRAM-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONSENT-000] Visitor statistics with Google Analytics and the consent banner" --body-file ".planning/github-issues/009-REQ-CONSENT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HTTP-000] Public site and HTTP boundary" --body-file ".planning/github-issues/010-REQ-HTTP-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-STORE-000] Content Store and code repository access" --body-file ".planning/github-issues/011-REQ-STORE-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PIPELINE-000] Secrets and build/deploy pipeline" --body-file ".planning/github-issues/012-REQ-PIPELINE-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-AUTHN-000] Editing Interface sign-in (OAuth helper)" --body-file ".planning/github-issues/013-REQ-AUTHN-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-EDIT-000] Editing Interface (Sveltia CMS)" --body-file ".planning/github-issues/014-REQ-EDIT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-MIGRATION-000] Legacy migration" --body-file ".planning/github-issues/015-REQ-MIGRATION-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTENT-010] Define the Work and Image content model with its required-field rules" --body-file ".planning/github-issues/016-REQ-CONTENT-010.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-020] Define the Text, CV entry, Download and Page content models" --body-file ".planning/github-issues/017-REQ-CONTENT-020.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-050] Define the Exhibition entry and News item content models" --body-file ".planning/github-issues/018-REQ-CONTENT-050.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-060] Define the Instagram settings: hidden posts and the feed switch" --body-file ".planning/github-issues/019-REQ-CONTENT-060.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-030] Fail the Site Build and publish nothing when content violates the content model" --body-file ".planning/github-issues/020-REQ-CONTENT-030.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-040] Exclude unpublished content from the production output" --body-file ".planning/github-issues/021-REQ-CONTENT-040.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-070] Fail the Site Build when the Content Store checkout contains a symbolic link or special file" --body-file ".planning/github-issues/022-REQ-CONTENT-070.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-080] Fail the publish for content files that are not Markdown, use front-matter fields outside the content model, or carry an unsafe slug" --body-file ".planning/github-issues/023-REQ-CONTENT-080.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-OUTPUT-010] HTML-encode editor text and render rich text only from the restricted format" --body-file ".planning/github-issues/024-REQ-OUTPUT-010.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-020] Treat content fields as data and never evaluate generator syntax inside them" --body-file ".planning/github-issues/025-REQ-OUTPUT-020.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-030] Allow only https: and mailto: links in content and open external links with noopener" --body-file ".planning/github-issues/026-REQ-OUTPUT-030.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-040] Fail the build when a generated page contains a script other than the site's own script files" --body-file ".planning/github-issues/027-REQ-OUTPUT-040.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-LAYOUT-010] Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon" --body-file ".planning/github-issues/028-REQ-LAYOUT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NAV-020] Generate every page in English at / and German under /de/ with a language switch to the same page" --body-file ".planning/github-issues/029-REQ-NAV-020.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-030] Show the other language's value, marked with its lang attribute, when a field's value is empty" --body-file ".planning/github-issues/030-REQ-NAV-030.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-010] Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page" --body-file ".planning/github-issues/031-REQ-NAV-010.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-WORKS-020] Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text" --body-file ".planning/github-issues/032-REQ-WORKS-020.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-IMAGES-010] Publish images only as responsive sizes of at most 2400 px on the long side, never as originals" --body-file ".planning/github-issues/033-REQ-IMAGES-010.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-HOME-010] Show the artist's name and the featured work's image, linked to its work page, on the home page" --body-file ".planning/github-issues/034-REQ-HOME-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-WORKS-010] List all published works newest year first with Nina's order within a year, and show works in public space as a group" --body-file ".planning/github-issues/035-REQ-WORKS-010.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-030] Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking" --body-file ".planning/github-issues/036-REQ-WORKS-030.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-050] Open a work's images in a full-screen image viewer with buttons and arrow keys, and fall back to plain links without JavaScript" --body-file ".planning/github-issues/037-REQ-WORKS-050.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-IMAGES-020] Remove embedded metadata from published images except the copyright notice and the creator" --body-file ".planning/github-issues/038-REQ-IMAGES-020.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-040] Reject images larger than 40 megapixels before decoding them fully" --body-file ".planning/github-issues/039-REQ-IMAGES-040.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-050] Recheck the type and size of every file the Site Build publishes and reject SVGs with active content" --body-file ".planning/github-issues/040-REQ-IMAGES-050.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-030] Remove embedded metadata from published PDFs automatically, except the copyright notice and the creator" --body-file ".planning/github-issues/041-REQ-IMAGES-030.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-EXHIBITIONS-010] List each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors and external link" --body-file ".planning/github-issues/042-REQ-EXHIBITIONS-010.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-EXHIBITIONS-030] Give each Exhibition entry its own page with its facts, linked works, description and installation images, and link it from the list" --body-file ".planning/github-issues/043-REQ-EXHIBITIONS-030.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-WORKS-040] Link each work page to the pages of the published Exhibition entries linked to that work" --body-file ".planning/github-issues/044-REQ-WORKS-040.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-EXHIBITIONS-020] Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive" --body-file ".planning/github-issues/045-REQ-EXHIBITIONS-020.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-HOME-020] Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page" --body-file ".planning/github-issues/046-REQ-HOME-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NEWS-010] List News items in their own section of the Exhibitions/News page, newest date first" --body-file ".planning/github-issues/047-REQ-NEWS-010.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-CV-010] Show the CV page with its six sections in order, hide empty sections and list entries newest year first" --body-file ".planning/github-issues/048-REQ-CV-010.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-CV-020] Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works" --body-file ".planning/github-issues/049-REQ-CV-020.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-CV-030] Let Nina hide a derived CV line or replace its generated wording" --body-file ".planning/github-issues/050-REQ-CV-030.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-TEXTS-010] List published Texts in Nina's order and give each its own page" --body-file ".planning/github-issues/051-REQ-TEXTS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-DOWNLOADS-010] Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF" --body-file ".planning/github-issues/052-REQ-DOWNLOADS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTACT-010] Show Nina's email address as a mailto link and link to galerie burster on the Contact page" --body-file ".planning/github-issues/053-REQ-CONTACT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-LEGAL-010] Publish the Impressum and Datenschutzerklärung pages and link them from every page" --body-file ".planning/github-issues/054-REQ-LEGAL-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-INSTAGRAM-010] Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram" --body-file ".planning/github-issues/055-REQ-INSTAGRAM-010.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-020] Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link" --body-file ".planning/github-issues/056-REQ-INSTAGRAM-020.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-CONSENT-010] Show the consent banner until a visitor chooses, store the choice strictly, and let the footer link reopen it" --body-file ".planning/github-issues/057-REQ-CONSENT-010.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-PRIV-030] Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings" --body-file ".planning/github-issues/058-REQ-PRIV-030.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-HOST-010] Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly" --body-file ".planning/github-issues/059-REQ-HOST-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-020] Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root" --body-file ".planning/github-issues/060-REQ-HOST-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-030] Run each VPS job and the OAuth helper under its own unprivileged account that can read only its own secret file" --body-file ".planning/github-issues/061-REQ-HOST-030.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-020] Delete nginx access logs after 14 days and keep them readable only by the maintainer" --body-file ".planning/github-issues/062-REQ-PRIV-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HTTP-010] Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS" --body-file ".planning/github-issues/063-REQ-HTTP-010.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-020] Send the SEC-HTTP-2 Content Security Policy with only the Google Analytics origins on every public response" --body-file ".planning/github-issues/064-REQ-HTTP-020.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-CONSENT-020] Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage" --body-file ".planning/github-issues/065-REQ-CONSENT-020.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-PERF-010] Meet Core Web Vitals \"good\" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run" --body-file ".planning/github-issues/066-REQ-PERF-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-010] Request no third-party resource except the Google Analytics origins, and include no other analytics, tracking, cookies or pixels in any page" --body-file ".planning/github-issues/067-REQ-PRIV-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONSENT-030] Set Google Analytics cookies for the host nina-laaf.de only, so that edit. and preview. never receive them" --body-file ".planning/github-issues/068-REQ-CONSENT-030.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-HTTP-030] Send nosniff and forbid framing of nina-laaf.de by other origins" --body-file ".planning/github-issues/069-REQ-HTTP-030.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-040] Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site" --body-file ".planning/github-issues/070-REQ-HTTP-040.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-060] Renew the TLS certificates of all three hosts automatically from Let's Encrypt and detect failed renewals" --body-file ".planning/github-issues/071-REQ-HTTP-060.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-070] Limit requests and connections per IP address on all three hosts" --body-file ".planning/github-issues/072-REQ-HTTP-070.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-STORE-010] Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record" --body-file ".planning/github-issues/073-REQ-STORE-010.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-020] Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there" --body-file ".planning/github-issues/074-REQ-STORE-020.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-030] Limit write access to dalini/nina-site to the maintainer" --body-file ".planning/github-issues/075-REQ-STORE-030.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-040] Protect the history of main in dalini/nina-content and require verified commits there" --body-file ".planning/github-issues/076-REQ-STORE-040.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-050] Mirror dalini/nina-content weekly to the VPS, keep 30 days outside the served directories, and test a restore yearly" --body-file ".planning/github-issues/077-REQ-STORE-050.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-060] Document the purge of content from history and backups on a legal request, with a record that names nothing purged" --body-file ".planning/github-issues/078-REQ-STORE-060.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-PIPELINE-010] Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key" --body-file ".planning/github-issues/079-REQ-PIPELINE-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-020] Deploy only the Site Build's generated output" --body-file ".planning/github-issues/080-REQ-PIPELINE-020.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-030] Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile" --body-file ".planning/github-issues/081-REQ-PIPELINE-030.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-040] Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change" --body-file ".planning/github-issues/082-REQ-PIPELINE-040.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-050] Keep the deploy, preview-deploy and content-read secrets in a production environment limited to main" --body-file ".planning/github-issues/083-REQ-PIPELINE-050.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-060] Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key" --body-file ".planning/github-issues/084-REQ-PIPELINE-060.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PREVIEW-010] Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins" --body-file ".planning/github-issues/085-REQ-PREVIEW-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-070] Record what each production deploy was built from and restore the previous version without a content change" --body-file ".planning/github-issues/086-REQ-PIPELINE-070.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-080] Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group" --body-file ".planning/github-issues/087-REQ-PIPELINE-080.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-130] Check main of the Content Store every minute from the VPS and start the build through workflow_dispatch when it changed" --body-file ".planning/github-issues/088-REQ-PIPELINE-130.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-090] Process Content Store files without access to the deploy keys or the Instagram token" --body-file ".planning/github-issues/089-REQ-PIPELINE-090.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-140] Restrict the production and preview deploy keys to the deploy's file transfers and reads, with no shell, other command or forwarding" --body-file ".planning/github-issues/090-REQ-PIPELINE-140.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-INSTAGRAM-050] Keep the Instagram token in a file of the deploy account and refresh it monthly with a job under that account" --body-file ".planning/github-issues/091-REQ-INSTAGRAM-050.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-030] Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks" --body-file ".planning/github-issues/092-REQ-INSTAGRAM-030.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-040] Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails" --body-file ".planning/github-issues/093-REQ-INSTAGRAM-040.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-PIPELINE-100] Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives" --body-file ".planning/github-issues/094-REQ-PIPELINE-100.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-110] Email Ives when a build fails" --body-file ".planning/github-issues/095-REQ-PIPELINE-110.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-120] Write the publish status file with only the content version, the result and the failing item and field" --body-file ".planning/github-issues/096-REQ-PIPELINE-120.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-EDIT-010] Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN" --body-file ".planning/github-issues/097-REQ-EDIT-010.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-CONSENT-040] Keep the Google tag loader and the measurement ID out of the preview output and the Editing Interface files" --body-file ".planning/github-issues/098-REQ-CONSENT-040.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-AUTHN-010] Sign editors in through the Deno OAuth helper, request only the allowed scopes, and release the token only to https://edit.nina-laaf.de" --body-file ".planning/github-issues/099-REQ-AUTHN-010.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-020] Verify the OAuth state of every sign-in with __Host- cookies and reject callbacks with more than one state cookie" --body-file ".planning/github-issues/100-REQ-AUTHN-020.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-030] Run the OAuth helper under its own account with a pinned Deno version, pinned dependencies and only the Deno permissions it needs" --body-file ".planning/github-issues/101-REQ-AUTHN-030.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-040] Obtain a new token on each sign-in and keep tokens and session values out of URLs and logs" --body-file ".planning/github-issues/102-REQ-AUTHN-040.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-050] Revoke all Editing Interface tokens of an editor at once, and drill it" --body-file ".planning/github-issues/103-REQ-AUTHN-050.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-HTTP-050] Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub" --body-file ".planning/github-issues/104-REQ-HTTP-050.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-EDIT-020] Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings" --body-file ".planning/github-issues/105-REQ-EDIT-020.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-030] Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks" --body-file ".planning/github-issues/106-REQ-EDIT-030.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-040] Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order" --body-file ".planning/github-issues/107-REQ-EDIT-040.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-050] Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload" --body-file ".planning/github-issues/108-REQ-EDIT-050.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-060] Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup" --body-file ".planning/github-issues/109-REQ-EDIT-060.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-070] Let Nina hide a single Instagram post and switch the feed off in the Editing Interface" --body-file ".planning/github-issues/110-REQ-EDIT-070.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-080] Show who changed each content item and when, and restore an earlier version" --body-file ".planning/github-issues/111-REQ-EDIT-080.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-090] Show Nina in the Editing Interface when her changes are not live yet and which item and field failed" --body-file ".planning/github-issues/112-REQ-EDIT-090.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-MIGRATION-010] Import the 53 legacy works with their German title, year, images and image captions into the Content Store" --body-file ".planning/github-issues/113-REQ-MIGRATION-010.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-020] Import legacy works without a year as unpublished, publish the rest, and feature the newest work" --body-file ".planning/github-issues/114-REQ-MIGRATION-020.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-030] Give every migrated image interim alt text made of the work title and the view number" --body-file ".planning/github-issues/115-REQ-MIGRATION-030.md"  # parent: REQ-MIGRATION-000
```
