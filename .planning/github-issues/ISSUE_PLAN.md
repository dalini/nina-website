# ISSUE_PLAN — nina-laaf.de

Step 9, fourth run (`prompts/09-github-epic.md`), 2026-10-31, after step 10 a third time (answers to PQ-54 to PQ-66 in `.planning/OPEN_QUESTIONS.md`). The specs changed after the third run's drafts, so the scope was decomposed again: the scope that PQ-54 to PQ-58 had blocked is now drafted, PQ-59 removed scope, the non-blocking answers PQ-60 to PQ-66 are written into the acceptance criteria of the issues they affect (not only noted), and the other changes of step 10 a third time (the single `script-src` host, no Tag Manager, GitHub Pro) are applied. Nothing on GitHub was changed.

## Run Inputs

- `Scope: ALL`
- `Execution mode: DRAFT_ONLY` — local files only; no mutating `gh` command was run.
- Sources read: `REQUIREMENTS.md`, `ARCHITECTURE.md`, `SECURITY.md`, `DESIGN.md`, `REQUIREMENT_TEMPLATE.md`; `CLAUDE.md` followed; `style-guide.html` cited for UI issues. `.planning/OPEN_QUESTIONS.md` read as the record of the step 10 decisions. `docs/legacy-content/works/` read only to check the legacy data that FR-11.1 points to (REQ-MIGRATION-040); it is not specification.
- Result: 123 draft issue bodies: 1 epic, 15 workstreams, 107 leaf issues (7 new: REQ-NAV-040, REQ-CONTENT-090, REQ-HOST-040, REQ-CONSENT-050, REQ-EDIT-100, REQ-EDIT-110, REQ-MIGRATION-040).

## Planning Rules Applied

- **Blocking** means the answer changes observable behavior, roles or permissions, auth, an API contract, data semantics, a trust boundary, a security control, failure behavior, or what an acceptance test can assert (prompt definition). Scope that depends on a blocking question is listed under Blocked Scope and is **not** decomposed. This run found no blocking question.
- **Non-blocking** questions are selections, values, wordings and orderings that leave every acceptance criterion pass/fail as written. They appear as `TO BE DECIDED` with their PQ reference in the issues' Open Decisions and still have to be answered before work on those issues starts, because an implementer must not make them alone.
- **Question numbering** continues after PQ-1 to PQ-66, which step 10, step 10 again and step 10 a third time answered (`.planning/OPEN_QUESTIONS.md`); this run's questions start at PQ-67 so that no ID means two things.
- **IDs** of the earlier runs are kept where the scope is the same; every revised issue's version goes up by one minor step, and its Author line names this run. New issues start at 0.1.0. File numbers are positions in the Topological Creation Order, so they changed where new issues were inserted; IDs did not.
- **Requirements that changed** since the third run's drafts were re-drafted, not only annotated. New issues for the unblocked scope: the Editing Interface installation (REQ-EDIT-100, PQ-54), the banner version (REQ-CONSENT-050, PQ-55), the mail relay agent (REQ-HOST-040, PQ-56; the alert delivery is now part of REQ-HTTP-060, REQ-INSTAGRAM-050 and REQ-PIPELINE-130), slug generation and duplicate slugs (REQ-EDIT-110, REQ-CONTENT-090, PQ-57), materials, dimensions and the migration report (REQ-MIGRATION-040, PQ-58), and the shared date format of the new FR-1.8 (REQ-NAV-040, PQ-65). Acceptance criteria now carry the answers to PQ-60 (fixed banner text, REQ-CONSENT-010), PQ-61 (verify, else accepted risk, REQ-CONSENT-030), PQ-62 (no banner on the preview, REQ-CONSENT-040), PQ-63 (German publish status banner, REQ-EDIT-090), PQ-64 (public-space CV line, REQ-CV-020), PQ-65 (dates, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-NEWS-010, REQ-INSTAGRAM-020) and PQ-66 (the viewer stops at the ends, REQ-WORKS-050). REQ-HTTP-020 and REQ-CONSENT-020 now use the decided `script-src` host `https://www.googletagmanager.com` instead of testing narrower sources; REQ-PRIV-030 checks that no Tag Manager or Ads product is linked and that the banner version is raised with every settings change (SEC-PRIV-7). REQ-EDIT-010 no longer covers the write protection of the installed files, which moved to REQ-EDIT-100. An Exhibition entry without a slug is reported by the content rule check (REQ-CONTENT-050), because its page address is formed from the slug (FR-4.7).
- **Legacy data check (REQ-MIGRATION-040):** in `docs/legacy-content/works/`, 25 of the 53 works have both work-level `materials` and `dimensions` fields and 28 have neither; every work-level `dimensions` value and every first caption of the form "materials | dimensions | year" holds two or three values in centimeters, so the dimensions rule of REQ-CONTENT-010 does not fail the publish of the imported works. Whether one caption with a stray literal `\n` after its year counts as that form is PQ-68.
- **Standards Alignment**: SECURITY.md uses no ASVS identifiers until they are verified; every issue states the target (ASVS 5.0.0 Level 1) and marks NIST SP 800-53 and Zero Trust as not applicable (SECURITY.md). Only references SECURITY.md cites (`REF-*`) or that define the mechanism (RFC 6265, RFC 6797, W3C CSP, WCAG 2.2) are listed. Threat references are STRIDE categories and T-IDs of SECURITY.md Threat Model v3, plus CWE identifiers where they match directly.
- **Models**: verified on 2026-10-31 against Anthropic's model pages (platform.claude.com/docs/…/models/opus-5-5/overview, …/sonnet-5-5/overview, …/fable-5-1/overview): Claude Opus 5.5 `claude-opus-5-5`, Claude Sonnet 5.5 `claude-sonnet-5-5`, Claude Fable 5.1 `claude-fable-5-1`. Fable is not assigned: no leaf is long-horizon or crosses many boundaries. OpenAI GPT is not assigned: the project uses no OpenAI tooling.
- **Release gate.** No scope is blocked. Before work starts on REQ-EDIT-110 and REQ-MIGRATION-040, the non-blocking questions PQ-67 to PQ-69 need answers (step 10). Deferred by Ives and still visible: the final logo mark (`logo.svg` stays a provisional placeholder; DESIGN.md).

## Open Questions

PQ-1 to PQ-66 are answered (`.planning/OPEN_QUESTIONS.md`). This run raised PQ-67 to PQ-69, all non-blocking.

### Blocking

None.

### Non-blocking

| ID | Question | Source | Affected | Status |
|---|---|---|---|---|
| PQ-67 | How are umlauts and ß transliterated in an Exhibition slug (e.g. ä → `ae` or `a`, ß → `ss`), and are repeated hyphens collapsed and leading or trailing hyphens removed? SEC-INPUT-6 says only "umlauts and ß transliterated, other characters replaced by hyphens"; the example `faltungen-2025` has neither case. Sveltia CMS's own slug options may decide what is possible. | SECURITY.md SEC-INPUT-6; REQUIREMENTS.md FR-4.7 | REQ-EDIT-110 (AC-03); REQ-EDIT-000 | TO BE DECIDED |
| PQ-68 | Does a first caption with stray characters around its parts count as the form "materials \| dimensions \| year"? In `aluminiumg-sse` the caption "Aluminiumguss \| 68 x 14 x 4 cm \| 2018" is followed by a literal `\n`. Either way the work is imported, and the migration report lists it if its values stay empty. | REQUIREMENTS.md FR-11.1; `docs/legacy-content/works/aluminiumg-sse.md` | REQ-MIGRATION-040; REQ-MIGRATION-000 | TO BE DECIDED |
| PQ-69 | Where does the import write the migration report, and in which format does Ives hand it to Nina? FR-11.1 and ARCHITECTURE.md say only that the import writes it; it must not become a content item of the Content Store (REQ-MIGRATION-040 AC-05). | REQUIREMENTS.md FR-11.1; ARCHITECTURE.md Content Store | REQ-MIGRATION-040; REQ-MIGRATION-000 | TO BE DECIDED |

## Blocked Scope

None. The scope blocked by PQ-54 to PQ-58 in the third run is drafted (REQ-EDIT-100, REQ-CONSENT-050, REQ-HOST-040, REQ-CONTENT-090, REQ-EDIT-110, REQ-MIGRATION-040); PQ-59 removed its scope (no Works order replaces the year order, FR-3.1).

## Coverage

Every FR, NFR, SEC and DEP rule of the sources, plus the one architecture decision (T-31 limits) that an issue implements without a rule ID. Status is `COVERED`, `PARTIALLY COVERED`, `BLOCKED` or `OUT OF SCOPE`; a note names the deciding answer or the non-blocking question that remains.

| Requirement ID | Issue IDs | Boundary | Security rules | Design sections | Status |
|---|---|---|---|---|---|
| FR-1.1 | REQ-NAV-010 | Site Build, Public Site (TB-1) | — | Components (Links, Interface labels, Focus state) | COVERED |
| FR-1.2 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-1.3 | REQ-NAV-020, REQ-EXHIBITIONS-030 | Site Build, Public Site (TB-1) | SEC-OUT-5 (only with client code) | Components (Language switch) | COVERED |
| FR-1.4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-NAV-020, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Content Store, Site Build, Editing Interface | SEC-OUT-1 | — | COVERED |
| FR-1.5 | REQ-CONTENT-010, REQ-NAV-020, REQ-WORKS-020, REQ-EDIT-020 | Content Store, Site Build | — | Typography | COVERED |
| FR-1.6 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Components (Language switch) | COVERED |
| FR-1.7 | REQ-NAV-030, REQ-WORKS-030 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-1.8 | REQ-NAV-040, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-NEWS-010, REQ-INSTAGRAM-020 | Site Build, Public Site (TB-1) | — | Components (Interface labels: Dates) | COVERED |
| FR-2.1 | REQ-HOME-010 | Site Build, Public Site (TB-1) | — | Layout and Spacing | COVERED |
| FR-2.2 | REQ-HOME-010 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-2.3 | REQ-HOME-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing | COVERED |
| FR-2.4 | REQ-INSTAGRAM-020, REQ-HOME-020 | Site Build, Public Site (TB-1) | SEC-PRIV-3 | Components (Instagram posts) | COVERED |
| FR-3.1 | REQ-CONTENT-010, REQ-WORKS-010, REQ-EDIT-020 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | Layout and Spacing (Grid) | COVERED (no order replaces the year order: PQ-59, step 10 a third time) |
| FR-3.2 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.3 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.4 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.5 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.6 | REQ-CONTENT-010, REQ-WORKS-030, REQ-WORKS-050, REQ-EDIT-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing (no cropping); Components (Image viewer) | COVERED |
| FR-3.7 | REQ-CONTENT-010, REQ-CONTENT-050, REQ-WORKS-030, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-TEXTS-010, REQ-NEWS-010, REQ-INSTAGRAM-020, REQ-MIGRATION-010, REQ-MIGRATION-030, REQ-EDIT-020, REQ-EDIT-030 | Site Build, Editing Interface | SEC-OUT-1 | Components (Image with credit), Accessibility | COVERED |
| FR-3.8 | REQ-CONTENT-010, REQ-WORKS-010, REQ-EDIT-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing (Grid) | COVERED |
| FR-3.9 | REQ-WORKS-040 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | — | COVERED |
| FR-3.10 | REQ-IMAGES-010, REQ-WORKS-030 | Site Build (TB-4) | SEC-PRIV-4 | — | COVERED |
| FR-3.11 | REQ-WORKS-050 | Public Site (TB-1) | SEC-OUT-5 | Components (Image viewer), Accessibility | COVERED (stops at the ends and shows the position: PQ-66, in REQ-WORKS-050) |
| FR-4.1 | REQ-CONTENT-050, REQ-EXHIBITIONS-020 | Site Build | — | Components (Interface labels) | COVERED |
| FR-4.2 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-EDIT-030, REQ-NAV-040 | Content Store, Site Build | — | Components (Interface labels, Exhibition entry page) | COVERED (dates as FR-1.8: REQ-NAV-040) |
| FR-4.3 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-030, REQ-OUTPUT-030 | Content Store, Site Build | SEC-OUT-2 | Components (Links) | COVERED |
| FR-4.4 | REQ-EXHIBITIONS-020 | Site Build | — | — | COVERED |
| FR-4.5 | REQ-CONTENT-050, REQ-EXHIBITIONS-030, REQ-EDIT-030 | Content Store, Site Build | SEC-INPUT-4 | Components (Exhibition entry page) | COVERED |
| FR-4.6 | REQ-CONTENT-050, REQ-NEWS-010, REQ-EDIT-030, REQ-NAV-040 | Content Store, Site Build | SEC-OUT-1, SEC-OUT-2 | Components (Image with credit) | COVERED (dates as FR-1.8: REQ-NAV-040) |
| FR-4.7 | REQ-CONTENT-050, REQ-CONTENT-080, REQ-EXHIBITIONS-030, REQ-EDIT-030, REQ-CONTENT-090, REQ-EDIT-110, REQ-NAV-040 | Content Store, Site Build, Public Site (TB-1, TB-4) | SEC-INPUT-6, SEC-OUT-1 | Components (Exhibition entry page) | COVERED (slug generation REQ-EDIT-110, duplicates REQ-CONTENT-090; transliteration: PQ-67, non-blocking) |
| FR-5.1 | REQ-CONTENT-020, REQ-TEXTS-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-1, SEC-INPUT-4 | Typography, Layout and Spacing | COVERED |
| FR-5.2 | REQ-CONTENT-020, REQ-CV-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Interface labels), Typography | COVERED |
| FR-5.3 | REQ-CV-020 | Site Build | SEC-INPUT-4 | — | COVERED (public-space line "year, title": PQ-64, in REQ-CV-020) |
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
| FR-8.7 | REQ-INSTAGRAM-020, REQ-NAV-040 | Site Build | SEC-OUT-3 | Accessibility | COVERED (date as FR-1.8: REQ-NAV-040) |
| FR-9.1 | REQ-LEGAL-010, REQ-CONTENT-020 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-9.2 | REQ-CONSENT-010, REQ-CONSENT-020, REQ-CONSENT-040, REQ-CONSENT-050 | Public Site (TB-1, TB-6) | SEC-PRIV-5, SEC-OUT-5 | Components (Consent banner) | COVERED (banner version: REQ-CONSENT-050; fixed text: REQ-CONSENT-010; none on the preview: REQ-CONSENT-040) |
| FR-10.1 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040, REQ-AUTHN-010, REQ-EDIT-100 | Editing Interface, Content Store (TB-2, TB-3) | SEC-AUTHZ-1, SEC-CICD-2 | — | COVERED (installation on the VPS: REQ-EDIT-100) |
| FR-10.2 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040, REQ-EDIT-050 | Editing Interface (TB-2) | SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5 | — | COVERED |
| FR-10.3 | REQ-CONTENT-010, REQ-CONTENT-030, REQ-EDIT-020, REQ-EDIT-040 | Content Store, Editing Interface, Site Build | — | — | COVERED (manual position within a year only: PQ-59) |
| FR-10.4 | REQ-PIPELINE-010, REQ-PIPELINE-080, REQ-PIPELINE-130 | Site Build → Static Hosting (TB-4, DF-4) | SEC-CICD-1, SEC-CICD-4 | — | COVERED |
| FR-10.5 | REQ-STORE-010, REQ-STORE-030, REQ-PREVIEW-010, REQ-AUTHN-010, REQ-AUTHN-020 | Editing Interface, Content Store, preview site (TB-2, TB-3) | SEC-AUTHN-1, SEC-AUTHZ-1, SEC-AUTHZ-2 | — | COVERED |
| FR-10.6 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030, REQ-CONTENT-080, REQ-IMAGES-040, REQ-IMAGES-050, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Site Build (TB-4), Editing Interface | SEC-INPUT-3 | — | COVERED |
| FR-10.7 | REQ-EDIT-080, REQ-STORE-040 | Editing Interface, Content Store | SEC-AUDIT-1 | — | COVERED |
| FR-10.8 | REQ-CONTENT-030, REQ-PIPELINE-110, REQ-PIPELINE-120, REQ-EDIT-090 | Site Build, Editing Interface (DF-9) | SEC-CICD-9, SEC-OUT-4 | — | COVERED (German banner text and look: PQ-63, in REQ-EDIT-090) |
| FR-10.9 | REQ-PIPELINE-060, REQ-PREVIEW-010 | Site Build, preview site (TB-2, TB-4) | SEC-CICD-3, SEC-AUTHN-1 | — | COVERED |
| FR-11.1 | REQ-MIGRATION-010, REQ-MIGRATION-040 | Content Store (DF-3) | SEC-OUT-1, SEC-PRIV-4 | — | COVERED (materials, dimensions and report: REQ-MIGRATION-040; PQ-68, PQ-69 non-blocking) |
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
| SEC-HTTP-5 | REQ-HTTP-060, REQ-HOST-040 | Static Hosting (TB-1, TB-2) | SEC-HTTP-5 | N/A | COVERED (email alerts through the local mail relay agent: REQ-HOST-040) |
| SEC-HOST-1 | REQ-HOST-010 | Domain, DNS, Static Hosting (TB-1) | SEC-HOST-1 | N/A | COVERED |
| SEC-HOST-2 | REQ-HOST-020, REQ-HOST-030, REQ-AUTHN-030, REQ-PIPELINE-010, REQ-HOST-040 | Static Hosting (VPS) | SEC-HOST-2 | N/A | COVERED |
| SEC-PRIV-1 | REQ-PRIV-010, REQ-LAYOUT-010, REQ-INSTAGRAM-010 | Public Site, Site Build (TB-1) | SEC-PRIV-1 | Typography | COVERED |
| SEC-PRIV-2 | — | — | — | N/A | OUT OF SCOPE (withdrawn in step 10) |
| SEC-PRIV-3 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-PRIV-3 | N/A | COVERED |
| SEC-PRIV-4 | REQ-IMAGES-010, REQ-IMAGES-020, REQ-IMAGES-030 | Site Build | SEC-PRIV-4 | N/A | COVERED |
| SEC-PRIV-5 | REQ-CONSENT-010, REQ-CONSENT-020, REQ-CONSENT-030, REQ-CONSENT-040, REQ-PRIV-010, REQ-PRIV-030, REQ-LEGAL-010, REQ-CONSENT-050 | Public Site, Site Build templates (TB-6) | SEC-PRIV-5 | Components (Consent banner) | COVERED (host-only cookie setting verified at implementation, else accepted risk: PQ-61, in REQ-CONSENT-030) |
| SEC-PRIV-6 | REQ-PRIV-020, REQ-LEGAL-010 | Static Hosting (TB-1) | SEC-PRIV-6 | N/A | COVERED |
| SEC-PRIV-7 | REQ-PRIV-030, REQ-CONSENT-050 | Google Analytics property (TB-6) | SEC-PRIV-7 | N/A | COVERED |
| SEC-OUT-1 | REQ-OUTPUT-010, REQ-EDIT-060 | Site Build, Editing Interface | SEC-OUT-1 | N/A | COVERED |
| SEC-OUT-2 | REQ-OUTPUT-030 | Site Build | SEC-OUT-2 | N/A | COVERED |
| SEC-OUT-3 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-OUT-3 | N/A | COVERED |
| SEC-OUT-4 | REQ-EDIT-060, REQ-EDIT-090 | Editing Interface (TB-2) | SEC-OUT-4 | N/A | COVERED |
| SEC-OUT-5 | REQ-WORKS-050, REQ-CONSENT-010, REQ-CONSENT-020, REQ-CONSENT-050 | Public Site client-side code | SEC-OUT-5 | N/A | COVERED |
| SEC-OUT-6 | — | — | — | N/A | OUT OF SCOPE (withdrawn in step 10) |
| SEC-OUT-7 | REQ-OUTPUT-040 | Site Build (TB-4) | SEC-OUT-7 | N/A | COVERED |
| SEC-AUTHN-1 | REQ-AUTHN-010, REQ-PREVIEW-010 | Editing Interface, preview site (TB-2) | SEC-AUTHN-1 | N/A | COVERED |
| SEC-AUTHN-2 | REQ-STORE-010, REQ-PREVIEW-010 | Editing Interface, preview site (TB-2) | SEC-AUTHN-2 | N/A | COVERED |
| SEC-AUTHN-3 | REQ-AUTHN-020, REQ-AUTHN-040 | Editing Interface (TB-2) | SEC-AUTHN-3 | N/A | COVERED (accepted exception documented) |
| SEC-AUTHN-4 | REQ-AUTHN-010, REQ-AUTHN-020, REQ-AUTHN-030, REQ-EDIT-100 | OAuth helper (TB-2, TB-3) | SEC-AUTHN-4 | N/A | COVERED (installed from a tagged release: REQ-EDIT-100) |
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
| SEC-INPUT-3 | REQ-CONTENT-030, REQ-PIPELINE-010, REQ-CONTENT-090 | Site Build, Static Hosting (TB-4) | SEC-INPUT-3 | N/A | COVERED |
| SEC-INPUT-4 | REQ-CONTENT-040, REQ-PIPELINE-060 | Site Build | SEC-INPUT-4 | N/A | COVERED |
| SEC-INPUT-5 | REQ-EDIT-050, REQ-IMAGES-040, REQ-INSTAGRAM-030 | Editing Interface, Site Build | SEC-INPUT-5 | N/A | COVERED |
| SEC-INPUT-6 | REQ-OUTPUT-020, REQ-CONTENT-080, REQ-CONTENT-090, REQ-EDIT-110 | Site Build (TB-4) | SEC-INPUT-6 | N/A | COVERED (transliteration: PQ-67, non-blocking) |
| SEC-INPUT-7 | REQ-CONTENT-070 | Site Build (TB-4) | SEC-INPUT-7 | N/A | COVERED |
| SEC-SECRET-1 | REQ-PIPELINE-040, REQ-PIPELINE-050, REQ-HOST-030, REQ-INSTAGRAM-050, REQ-AUTHN-010, REQ-HOST-040 | Site Build, Static Hosting, OAuth helper (TB-4); `docs/ops/` | SEC-SECRET-1 | N/A | COVERED |
| SEC-SECRET-2 | REQ-PIPELINE-050, REQ-PIPELINE-060, REQ-PIPELINE-090 | Site Build pipeline (TB-4) | SEC-SECRET-2 | N/A | COVERED |
| SEC-SECRET-3 | REQ-INSTAGRAM-030, REQ-INSTAGRAM-050, REQ-HOST-040 | Instagram Feed Integration (TB-5), Static Hosting | SEC-SECRET-3 | N/A | COVERED (alert through REQ-HOST-040) |
| SEC-CICD-1 | REQ-PIPELINE-010, REQ-PIPELINE-140, REQ-INSTAGRAM-040, REQ-INSTAGRAM-050, REQ-EDIT-100 | Site Build, Static Hosting (TB-4) | SEC-CICD-1 | N/A | COVERED |
| SEC-CICD-2 | REQ-PIPELINE-030, REQ-EDIT-010, REQ-AUTHN-030, REQ-EDIT-100 | Site Build pipeline, Editing Interface (TB-2, TB-4) | SEC-CICD-2 | N/A | COVERED (Editing Interface files installed by hand by Ives: REQ-EDIT-100) |
| SEC-CICD-3 | REQ-PIPELINE-060, REQ-PREVIEW-010 | Site Build pipeline, Static Hosting | SEC-CICD-3 | N/A | COVERED |
| SEC-CICD-4 | REQ-PIPELINE-080, REQ-PIPELINE-130, REQ-HOST-040 | Site Build pipeline, Static Hosting (VPS) | SEC-CICD-4 | N/A | COVERED (alert through REQ-HOST-040) |
| SEC-CICD-5 | REQ-PIPELINE-020 | Site Build pipeline, Static Hosting | SEC-CICD-5 | N/A | COVERED |
| SEC-CICD-6 | REQ-PIPELINE-090, REQ-INSTAGRAM-030 | Site Build pipeline (TB-4) | SEC-CICD-6 | N/A | COVERED |
| SEC-CICD-7 | REQ-STORE-020 | Content Store (TB-3) | SEC-CICD-7 | N/A | COVERED |
| SEC-CICD-8 | REQ-PIPELINE-100, REQ-PIPELINE-130 | Site Build pipeline, GitHub account | SEC-CICD-8 | N/A | COVERED |
| SEC-CICD-9 | REQ-PIPELINE-120 | Site Build, Editing Interface (DF-9) | SEC-CICD-9 | N/A | COVERED |
| T-31 (ARCHITECTURE.md Static Hosting: per-IP limits) | REQ-HTTP-070 | Static Hosting (TB-1, TB-2) | — | N/A | COVERED |
| DEP-1 to DEP-6, DEP-8 | All leaf issues (Constraints) | All components | DEP-1 to DEP-6, DEP-8 | N/A | COVERED (applied whenever a dependency is added) |
| DEP-7 | REQ-PIPELINE-030, REQ-EDIT-010, REQ-AUTHN-030 | Site Build pipeline, Editing Interface | DEP-7 | N/A | COVERED |

Totals: COVERED 118, PARTIALLY COVERED 0, BLOCKED 0, OUT OF SCOPE 3 (121 rows). Every leaf issue appears in at least one row.

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
│   ├── REQ-CONTENT-090 Fail the publish when two Exhibition entries share a slug, naming both entries and the slug field
├── REQ-OUTPUT-000 Output encoding and safe rendering
│   ├── REQ-OUTPUT-010 HTML-encode editor text and render rich text only from the restricted format
│   ├── REQ-OUTPUT-020 Treat content fields as data and never evaluate generator syntax inside them
│   ├── REQ-OUTPUT-030 Allow only https: and mailto: links in content and open external links with noopener
│   ├── REQ-OUTPUT-040 Fail the build when a generated page contains a script other than the site's own script files
├── REQ-NAV-000 Navigation and languages
│   ├── REQ-NAV-020 Generate every page in English at / and German under /de/ with a language switch to the same page
│   ├── REQ-NAV-040 Show dates numerically in the page's language, DD.MM.YYYY in German and YYYY-MM-DD in English, with both full dates in a range
│   ├── REQ-NAV-030 Show the other language's value, marked with its lang attribute, when a field's value is empty
│   ├── REQ-NAV-010 Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page
├── REQ-WORKS-000 Works
│   ├── REQ-WORKS-020 Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text
│   ├── REQ-WORKS-010 List all published works newest year first with Nina's order within a year, and show works in public space as a group
│   ├── REQ-WORKS-030 Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking
│   ├── REQ-WORKS-050 Open a work's images in a full-screen image viewer with buttons and arrow keys that stops at the first and last image and shows the position, and fall back to plain links without JavaScript
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
│   ├── REQ-INSTAGRAM-050 Keep the Instagram token in a file of the deploy account, refresh it monthly with a job under that account, and email Ives when a refresh fails
│   ├── REQ-INSTAGRAM-030 Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks
│   ├── REQ-INSTAGRAM-040 Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails
├── REQ-CONSENT-000 Visitor statistics with Google Analytics and the consent banner
│   ├── REQ-CONSENT-010 Show the consent banner with its fixed text until a visitor chooses, store the choice strictly, and let the footer link reopen it
│   ├── REQ-PRIV-030 Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings
│   ├── REQ-CONSENT-020 Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage
│   ├── REQ-CONSENT-050 Store the banner version with the consent choice and ask again when the banner version changes
│   ├── REQ-CONSENT-030 Set Google Analytics cookies for the host nina-laaf.de only, verified in a browser test, or record the accepted risk when no tag setting allows it
│   ├── REQ-CONSENT-040 Leave the consent banner, the Google tag loader and the measurement ID out of the preview output and the Editing Interface files
├── REQ-HTTP-000 Public site and HTTP boundary
│   ├── REQ-HTTP-010 Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS
│   ├── REQ-HTTP-020 Send the SEC-HTTP-2 Content Security Policy with only the Google Analytics origins on every public response
│   ├── REQ-HTTP-030 Send nosniff and forbid framing of nina-laaf.de by other origins
│   ├── REQ-HTTP-040 Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site
│   ├── REQ-HTTP-060 Renew the TLS certificates of all three hosts automatically from Let's Encrypt and email Ives about failed renewals
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
│   ├── REQ-PIPELINE-130 Check main of the Content Store every minute from the VPS, start the build through workflow_dispatch when it changed, and email Ives when the check fails
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
│   ├── REQ-EDIT-100 Install the Editing Interface files on the VPS by hand from a tagged release of dalini/nina-site, owned by root and read-only to nginx and the OAuth helper
│   ├── REQ-EDIT-020 Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings
│   ├── REQ-EDIT-030 Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks
│   ├── REQ-EDIT-110 Generate an Exhibition entry's slug from its title and start year when it is first saved, and keep it when the entry is edited later
│   ├── REQ-EDIT-040 Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order
│   ├── REQ-EDIT-050 Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload
│   ├── REQ-EDIT-060 Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup
│   ├── REQ-EDIT-070 Let Nina hide a single Instagram post and switch the feed off in the Editing Interface
│   ├── REQ-EDIT-080 Show who changed each content item and when, and restore an earlier version
│   ├── REQ-EDIT-090 Show Nina in the Editing Interface when her changes are not live yet and which item and field failed
├── REQ-MIGRATION-000 Legacy migration
│   ├── REQ-MIGRATION-010 Import the 53 legacy works with their German title, year, images and image captions into the Content Store
│   ├── REQ-MIGRATION-040 Import each legacy work's materials and dimensions from its legacy fields or a clean first caption, and write a migration report for Nina
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
├── REQ-HOST-040 Run a local mail relay agent under its own unprivileged account that alone reads the SMTP relay credential, and deliver the VPS alerts to Ives
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
REQ-CONTENT-090 → REQ-CONTENT-080
REQ-OUTPUT-010 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050
REQ-OUTPUT-020 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-OUTPUT-030 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030
REQ-OUTPUT-040 → REQ-CONTENT-030, REQ-OUTPUT-010
REQ-LAYOUT-010 → (none)
REQ-NAV-020 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-LAYOUT-010
REQ-NAV-040 → REQ-NAV-020
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
REQ-EXHIBITIONS-010 → REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-NAV-040
REQ-EXHIBITIONS-030 → REQ-EXHIBITIONS-010, REQ-WORKS-020, REQ-IMAGES-010, REQ-CONTENT-080, REQ-CONTENT-090
REQ-WORKS-040 → REQ-WORKS-020, REQ-EXHIBITIONS-030
REQ-EXHIBITIONS-020 → REQ-EXHIBITIONS-010, REQ-CONTENT-040
REQ-HOME-020 → REQ-HOME-010, REQ-EXHIBITIONS-020
REQ-NEWS-010 → REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010, REQ-NAV-040
REQ-CV-010 → REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010
REQ-CV-020 → REQ-CV-010, REQ-EXHIBITIONS-020, REQ-CONTENT-040
REQ-CV-030 → REQ-CV-020
REQ-TEXTS-010 → REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010, REQ-IMAGES-010
REQ-DOWNLOADS-010 → REQ-CV-010, REQ-CONTENT-040, REQ-IMAGES-050, REQ-IMAGES-030
REQ-CONTACT-010 → REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-030
REQ-LEGAL-010 → REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010
REQ-INSTAGRAM-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030
REQ-INSTAGRAM-020 → REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010, REQ-NAV-040
REQ-CONSENT-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-LEGAL-010
REQ-PRIV-030 → (none)
REQ-HOST-010 → (none)
REQ-HOST-020 → (none)
REQ-HOST-030 → REQ-HOST-020
REQ-HOST-040 → REQ-HOST-030
REQ-PRIV-020 → REQ-HOST-020
REQ-HTTP-010 → REQ-HOST-020
REQ-HTTP-020 → REQ-HTTP-010, REQ-NAV-020
REQ-CONSENT-020 → REQ-CONSENT-010, REQ-PRIV-030, REQ-HTTP-020, REQ-OUTPUT-040
REQ-CONSENT-050 → REQ-CONSENT-010, REQ-CONSENT-020
REQ-PERF-010 → REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-CONSENT-020
REQ-PRIV-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-CONSENT-020
REQ-CONSENT-030 → REQ-CONSENT-020
REQ-HTTP-030 → REQ-HTTP-010
REQ-HTTP-040 → REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030
REQ-HTTP-060 → REQ-HTTP-010, REQ-HTTP-040, REQ-HOST-040
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
REQ-PIPELINE-130 → REQ-PIPELINE-080, REQ-HOST-030, REQ-HOST-040
REQ-PIPELINE-090 → REQ-PIPELINE-010, REQ-PIPELINE-050
REQ-PIPELINE-140 → REQ-PIPELINE-010, REQ-PIPELINE-060, REQ-HOST-030
REQ-INSTAGRAM-050 → REQ-HOST-030, REQ-HOST-040
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
REQ-EDIT-100 → REQ-EDIT-010, REQ-AUTHN-030
REQ-AUTHN-040 → REQ-AUTHN-020, REQ-PRIV-020
REQ-AUTHN-050 → REQ-AUTHN-010
REQ-HTTP-050 → REQ-HTTP-040, REQ-EDIT-010
REQ-EDIT-020 → REQ-EDIT-010, REQ-CONTENT-010
REQ-EDIT-030 → REQ-EDIT-010, REQ-CONTENT-050
REQ-EDIT-110 → REQ-EDIT-030
REQ-EDIT-040 → REQ-EDIT-010, REQ-CONTENT-020
REQ-EDIT-050 → REQ-EDIT-020, REQ-EDIT-040
REQ-EDIT-060 → REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
REQ-EDIT-070 → REQ-EDIT-010, REQ-CONTENT-060
REQ-EDIT-080 → REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
REQ-EDIT-090 → REQ-PIPELINE-120, REQ-EDIT-010, REQ-HTTP-050
REQ-MIGRATION-010 → REQ-CONTENT-010
REQ-MIGRATION-040 → REQ-MIGRATION-010
REQ-MIGRATION-020 → REQ-MIGRATION-010, REQ-CONTENT-040
REQ-MIGRATION-030 → REQ-MIGRATION-010
```

## Manifest

File names are `NNN-<ID>.md`, where `NNN` is the zero-based position in the Topological Creation Order (`000` is the epic). Effort is engineer-days; LOC is changed human-authored lines, excluding generated files and lockfiles. Recommended models (verified 2026-10-31, Planning Rules): Claude Opus 5.5 for 40 leaves, Claude Sonnet 5.5 for 67; none for Claude Fable or OpenAI GPT.

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
| 024 | REQ-CONTENT-090 | Fail the publish when two Exhibition entries share a slug, naming both entries and the slug field | REQ-CONTENT-000 | REQ-CONTENT-080 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small, fully specified uniqueness check in an existing fail-closed stage. |
| 025 | REQ-OUTPUT-010 | HTML-encode editor text and render rich text only from the restricted format | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-critical encoding and sanitizing that needs careful use of the renderer and a hostile test corpus. |
| 026 | REQ-OUTPUT-020 | Treat content fields as data and never evaluate generator syntax inside them | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Template-injection prevention depends on generator internals and how content is loaded (T-18). |
| 027 | REQ-OUTPUT-030 | Allow only https: and mailto: links in content and open external links with noopener | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small allow-list with a known bypass corpus, fully specified by SEC-OUT-2. |
| 028 | REQ-OUTPUT-040 | Fail the build when a generated page contains a script other than the site's own script files | REQ-OUTPUT-000 | REQ-CONTENT-030, REQ-OUTPUT-010 | 0.5–1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | The last barrier against a Google or Tag Manager script injected into a page (T-76) must parse real HTML output robustly for both builds. |
| 029 | REQ-LAYOUT-010 | Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon | REQ-EPIC-001 | — | 1–1.5 days | 300–600 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Translating a complete token table and component list into one stylesheet and base template is well specified. |
| 030 | REQ-NAV-020 | Generate every page in English at / and German under /de/ with a language switch to the same page | REQ-NAV-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-LAYOUT-010 | 1–2 days | 300–700 | Claude Opus 5.5 (`claude-opus-5-5`) | It defines the page pairing and URL structure every page issue builds on, the largest structural piece of the public site. |
| 031 | REQ-NAV-040 | Show dates numerically in the page's language, DD.MM.YYYY in German and YYYY-MM-DD in English, with both full dates in a range | REQ-NAV-000 | REQ-NAV-020 | 0.5 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small, fully specified formatting function shared by four page types. |
| 032 | REQ-NAV-030 | Show the other language's value, marked with its lang attribute, when a field's value is empty | REQ-NAV-000 | REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | One shared helper applied consistently; FR-1.7 defines the behavior exactly. |
| 033 | REQ-NAV-010 | Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page | REQ-NAV-000 | REQ-NAV-020 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine shared-layout work with fixed targets and DESIGN.md states. |
| 034 | REQ-WORKS-020 | Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text | REQ-WORKS-000 | REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template rendering of defined fields with optional-field handling. |
| 035 | REQ-IMAGES-010 | Publish images only as responsive sizes of at most 2400 px on the long side, never as originals | REQ-IMAGES-000 | REQ-CONTENT-010 | 1–2 days | 200–500 | Claude Opus 5.5 (`claude-opus-5-5`) | Generator resource handling can leak originals silently (T-25); getting sizes, caps and output hygiene right needs care. |
| 036 | REQ-HOME-010 | Show the artist's name and the featured work's image, linked to its work page, on the home page | REQ-EPIC-001 | REQ-CONTENT-010, REQ-CONTENT-030, REQ-NAV-020, REQ-IMAGES-010 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A single template section driven by one content reference. |
| 037 | REQ-WORKS-010 | List all published works newest year first with Nina's order within a year, and show works in public space as a group | REQ-WORKS-000 | REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020, REQ-IMAGES-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | List and filtered view from fields already in the model; layout fixed by DESIGN.md. |
| 038 | REQ-WORKS-030 | Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking | REQ-WORKS-000 | REQ-WORKS-020, REQ-IMAGES-010 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Ordered image rendering with labels; the image processing itself is a separate issue. |
| 039 | REQ-WORKS-050 | Open a work's images in a full-screen image viewer with buttons and arrow keys that stops at the first and last image and shows the position, and fall back to plain links without JavaScript | REQ-WORKS-000 | REQ-WORKS-030, REQ-IMAGES-010 | 1–1.5 days | 250–500 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small vanilla-JavaScript component fully described by FR-3.11 and DESIGN.md, with clear keyboard and focus tests. |
| 040 | REQ-IMAGES-020 | Remove embedded metadata from published images except the copyright notice and the creator | REQ-IMAGES-000 | REQ-IMAGES-010 | 0.5–1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Privacy control with format-specific metadata blocks (EXIF, XMP, IPTC) and a keep-list that is easy to get wrong. |
| 041 | REQ-IMAGES-040 | Reject images larger than 40 megapixels before decoding them fully | REQ-IMAGES-000 | REQ-IMAGES-010, REQ-CONTENT-030 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Denial-of-service guard that must run before the generator's own decoder and handle malformed headers safely. |
| 042 | REQ-IMAGES-050 | Recheck the type and size of every file the Site Build publishes and reject SVGs with active content | REQ-IMAGES-000 | REQ-CONTENT-030 | 0.5–1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Security recheck with polyglot and SVG edge cases; must not trust extensions. |
| 043 | REQ-IMAGES-030 | Remove embedded metadata from published PDFs automatically, except the copyright notice and the creator | REQ-IMAGES-000 | REQ-CONTENT-020, REQ-IMAGES-050 | 1 day | 150–350 | Claude Opus 5.5 (`claude-opus-5-5`) | A privacy control over a complex file format (document info, XMP, embedded images) with a keep-list, where a publish must never fail because of metadata. |
| 044 | REQ-EXHIBITIONS-010 | List each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors and external link | REQ-EXHIBITIONS-000 | REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-NAV-040 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template rendering of defined fields with optional-field handling. |
| 045 | REQ-EXHIBITIONS-030 | Give each Exhibition entry its own page with its facts, linked works, description and installation images, and link it from the list | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-010, REQ-WORKS-020, REQ-IMAGES-010, REQ-CONTENT-080, REQ-CONTENT-090 | 1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template work over fields the model already holds, following the DESIGN.md Exhibition entry page component. |
| 046 | REQ-WORKS-040 | Link each work page to the pages of the published Exhibition entries linked to that work | REQ-WORKS-000 | REQ-WORKS-020, REQ-EXHIBITIONS-030 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A reverse lookup over a relation that the model already holds. |
| 047 | REQ-EXHIBITIONS-020 | Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-010, REQ-CONTENT-040 | 0.5–1 day | 120–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Date-boundary logic fully defined by FR-4.1, with clear test cases. |
| 048 | REQ-HOME-020 | Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page | REQ-EPIC-001 | REQ-HOME-010, REQ-EXHIBITIONS-020 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small selection rule on top of the grouping in REQ-EXHIBITIONS-020. |
| 049 | REQ-NEWS-010 | List News items in their own section of the Exhibitions/News page, newest date first | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010, REQ-NAV-040 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine list with optional fields. |
| 050 | REQ-CV-010 | Show the CV page with its six sections in order, hide empty sections and list entries newest year first | REQ-CV-000 | REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine grouping of entries into six fixed sections. |
| 051 | REQ-CV-020 | Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works | REQ-CV-000 | REQ-CV-010, REQ-EXHIBITIONS-020, REQ-CONTENT-040 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A clear mapping rule on top of the grouping function. |
| 052 | REQ-CV-030 | Let Nina hide a derived CV line or replace its generated wording | REQ-CV-000 | REQ-CV-020 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two small, fully specified rules on derived lines. |
| 053 | REQ-TEXTS-010 | List published Texts in Nina's order and give each its own page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010, REQ-IMAGES-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine list and detail pages. |
| 054 | REQ-DOWNLOADS-010 | Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF | REQ-EPIC-001 | REQ-CV-010, REQ-CONTENT-040, REQ-IMAGES-050, REQ-IMAGES-030 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A routine link list with a text label. |
| 055 | REQ-CONTACT-010 | Show Nina's email address as a mailto link and link to galerie burster on the Contact page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 50–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A single page with two content-driven links. |
| 056 | REQ-LEGAL-010 | Publish the Impressum and Datenschutzerklärung pages and link them from every page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two Page renders and footer links; the wording is content. |
| 057 | REQ-INSTAGRAM-010 | Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram | REQ-INSTAGRAM-000 | REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A plain link plus a network check. |
| 058 | REQ-INSTAGRAM-020 | Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link | REQ-INSTAGRAM-000 | REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010, REQ-NAV-040 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Rendering from a neutral list with a small selection rule; the risky fetch is a separate issue. |
| 059 | REQ-CONSENT-010 | Show the consent banner with its fixed text until a visitor chooses, store the choice strictly, and let the footer link reopen it | REQ-CONSENT-000 | REQ-LAYOUT-010, REQ-NAV-020, REQ-LEGAL-010 | 1–1.5 days | 200–400 | Claude Opus 5.5 (`claude-opus-5-5`) | First-party script that reads browser storage and decides whether a visitor has consented; a parsing mistake grants consent the visitor never gave (T-72). |
| 060 | REQ-PRIV-030 | Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings | REQ-CONSENT-000 | — | 0.5 day | 0–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A settings checklist and its yearly review, fully specified by SEC-PRIV-7; no code. |
| 061 | REQ-HOST-010 | Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly | REQ-EPIC-001 | — | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A checklist and its first review record; no code beyond a DNS check script. |
| 062 | REQ-HOST-020 | Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root | REQ-EPIC-001 | — | 1–1.5 days | 80–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Server hardening is security-sensitive and a mistake (e.g. locking out SSH) is costly to undo. |
| 063 | REQ-HOST-030 | Run each VPS job and the OAuth helper under its own unprivileged account that can read only its own secret file | REQ-EPIC-001 | REQ-HOST-020 | 1 day | 60–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Account and permission design on the VPS that every job and secret depends on; mistakes are silent and widen what one flaw can reach. |
| 064 | REQ-HOST-040 | Run a local mail relay agent under its own unprivileged account that alone reads the SMTP relay credential, and deliver the VPS alerts to Ives | REQ-EPIC-001 | REQ-HOST-030 | 0.5–1 day | 40–150 | Claude Opus 5.5 (`claude-opus-5-5`) | It places one more secret on a shared server, where the account, file-permission and queue-permission details decide whether every job can read it. |
| 065 | REQ-PRIV-020 | Delete nginx access logs after 14 days and keep them readable only by the maintainer | REQ-EPIC-001 | REQ-HOST-020 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Log rotation and file permissions with a clear retention value. |
| 066 | REQ-HTTP-010 | Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS | REQ-HTTP-000 | REQ-HOST-020 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Host configuration and checks fully specified by SEC-HTTP-1. |
| 067 | REQ-HTTP-020 | Send the SEC-HTTP-2 Content Security Policy with only the Google Analytics origins on every public response | REQ-HTTP-000 | REQ-HTTP-010, REQ-NAV-020 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`) | A strict CSP has to be fitted to the generator's output (inline styles, scripts) without weakening it. |
| 068 | REQ-CONSENT-020 | Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage | REQ-CONSENT-000 | REQ-CONSENT-010, REQ-PRIV-030, REQ-HTTP-020, REQ-OUTPUT-040 | 1–1.5 days | 150–350 | Claude Opus 5.5 (`claude-opus-5-5`) | Consent Mode depends on the order of first-party and Google code; a mistake sets cookies or sends full measurements before consent (T-71) and has to be proven by network-level browser tests. |
| 069 | REQ-CONSENT-050 | Store the banner version with the consent choice and ask again when the banner version changes | REQ-CONSENT-000 | REQ-CONSENT-010, REQ-CONSENT-020 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | It changes the consent decision itself; a comparison mistake turns an old answer into consent for new purposes (T-72, T-73). |
| 070 | REQ-PERF-010 | Meet Core Web Vitals "good" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run | REQ-EPIC-001 | REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-CONSENT-020 | 1 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Measurement and tuning against fixed numeric budgets. |
| 071 | REQ-PRIV-010 | Request no third-party resource except the Google Analytics origins, and include no other analytics, tracking, cookies or pixels in any page | REQ-EPIC-001 | REQ-LAYOUT-010, REQ-NAV-020, REQ-CONSENT-020 | 1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A browser network-recording test over a known list of page types; the rule is unambiguous. |
| 072 | REQ-CONSENT-030 | Set Google Analytics cookies for the host nina-laaf.de only, verified in a browser test, or record the accepted risk when no tag setting allows it | REQ-CONSENT-000 | REQ-CONSENT-020 | 0.5 day | 30–100 | Claude Opus 5.5 (`claude-opus-5-5`) | The setting must be verified against how browsers scope cookies (RFC 6265), and when no setting meets SEC-PRIV-5 the accepted risk must be recorded with the test results rather than assumed. |
| 073 | REQ-HTTP-030 | Send nosniff and forbid framing of nina-laaf.de by other origins | REQ-HTTP-000 | REQ-HTTP-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two headers and a framing test. |
| 074 | REQ-HTTP-040 | Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Reuses the public host's header set on two more hosts. |
| 075 | REQ-HTTP-060 | Renew the TLS certificates of all three hosts automatically from Let's Encrypt and email Ives about failed renewals | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-040, REQ-HOST-040 | 0.5 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A standard renewal job and expiry check that hands its alert to an existing local mail interface. |
| 076 | REQ-HTTP-070 | Limit requests and connections per IP address on all three hosts | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-040 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Standard nginx limits once the values are set. |
| 077 | REQ-STORE-010 | Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record | REQ-STORE-000 | — | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Repository settings and a record file, fully specified by SEC-AUTHZ-1, -2 and -4. |
| 078 | REQ-STORE-020 | Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there | REQ-STORE-000 | — | 0.5 day | 0–20 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | One settings change and one test. |
| 079 | REQ-STORE-030 | Limit write access to dalini/nina-site to the maintainer | REQ-STORE-000 | — | 0.5 day | 0–20 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Repository settings with one negative test. |
| 080 | REQ-STORE-040 | Protect the history of main in dalini/nina-content and require verified commits there | REQ-STORE-000 | REQ-STORE-010 | 0.5–1 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Branch protection settings with negative tests, plus one documented check whose result decides whether a setting stays. |
| 081 | REQ-STORE-050 | Mirror dalini/nina-content weekly to the VPS, keep 30 days outside the served directories, and test a restore yearly | REQ-STORE-000 | REQ-HOST-030, REQ-STORE-010 | 1 day | 60–180 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A cron mirror with retention and permissions, plus a yearly restore drill; values and locations are fixed by SEC-AVAIL-1. |
| 082 | REQ-STORE-060 | Document the purge of content from history and backups on a legal request, with a record that names nothing purged | REQ-STORE-000 | REQ-STORE-040, REQ-STORE-050 | 0.5 day | 0–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A runbook and a record format; the rule fixes who may do it and what the record holds. |
| 083 | REQ-PIPELINE-010 | Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key | REQ-PIPELINE-000 | REQ-CONTENT-030, REQ-HOST-020, REQ-STORE-010 | 1–2 days | 100–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Least-privilege deploy identity, host-key pinning and fail-closed gating across GitHub Actions and the VPS. |
| 084 | REQ-PIPELINE-020 | Deploy only the Site Build's generated output | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Point the upload at the output directory and add a post-deploy path check. |
| 085 | REQ-PIPELINE-030 | Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Mechanical pinning and checksum verification with a lint check. |
| 086 | REQ-PIPELINE-040 | Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Secret scanning, masking and a fail-closed gate with platform features. |
| 087 | REQ-PIPELINE-050 | Keep the deploy, preview-deploy and content-read secrets in a production environment limited to main | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | GitHub environment settings with a negative test from another branch; fully specified by SEC-SECRET-1 and SEC-SECRET-2. |
| 088 | REQ-PIPELINE-060 | Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-CONTENT-040, REQ-PIPELINE-050 | 0.5–1 day | 60–180 | Claude Opus 5.5 (`claude-opus-5-5`) | Key separation between two outputs from one run is easy to get subtly wrong (T-20, T-21). |
| 089 | REQ-PREVIEW-010 | Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins | REQ-PIPELINE-000 | REQ-HTTP-040, REQ-PIPELINE-060 | 0.5–1 day | 30–100 | Claude Opus 5.5 (`claude-opus-5-5`) | Authentication setup with password-storage and guessing-limit requirements. |
| 090 | REQ-PIPELINE-070 | Record what each production deploy was built from and restore the previous version without a content change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A deploy record and a restore drill using the release layout from REQ-PIPELINE-010. |
| 091 | REQ-PIPELINE-080 | Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Workflow trigger configuration fully specified by SEC-CICD-4. |
| 092 | REQ-PIPELINE-130 | Check main of the Content Store every minute from the VPS, start the build through workflow_dispatch when it changed, and email Ives when the check fails | REQ-PIPELINE-000 | REQ-PIPELINE-080, REQ-HOST-030, REQ-HOST-040 | 1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | A small job that holds a trigger token, must never pass content into a command, and is the only path that meets the 15-minute limit. |
| 093 | REQ-PIPELINE-090 | Process Content Store files without access to the deploy keys or the Instagram token | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-PIPELINE-050 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`) | Job isolation for secrets in GitHub Actions has subtle leaks (shared workspaces, artifacts, environment inheritance). |
| 094 | REQ-PIPELINE-140 | Restrict the production and preview deploy keys to the deploy's file transfers and reads, with no shell, other command or forwarding | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-PIPELINE-060, REQ-HOST-030 | 0.5–1 day | 40–120 | Claude Opus 5.5 (`claude-opus-5-5`) | SSH key restrictions (forced commands, path limits) are easy to get subtly wrong, and a gap turns a leaked key into a persistent foothold (T-81). |
| 095 | REQ-INSTAGRAM-050 | Keep the Instagram token in a file of the deploy account, refresh it monthly with a job under that account, and email Ives when a refresh fails | REQ-INSTAGRAM-000 | REQ-HOST-030, REQ-HOST-040 | 0.5–1 day | 60–180 | Claude Opus 5.5 (`claude-opus-5-5`) | Secret handling on the VPS across a cron job, file permissions and an outbound API call, where a mistake leaks a token or silently stops the feed. |
| 096 | REQ-INSTAGRAM-030 | Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks | REQ-INSTAGRAM-000 | REQ-INSTAGRAM-020, REQ-IMAGES-020, REQ-IMAGES-040, REQ-PIPELINE-090, REQ-INSTAGRAM-050 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-sensitive integration with untrusted external data, a secret and per-item failure handling. |
| 097 | REQ-INSTAGRAM-040 | Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails | REQ-INSTAGRAM-000 | REQ-INSTAGRAM-030, REQ-PIPELINE-010 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | It reads back across a trust boundary from hosting and must fail safe in several ways. |
| 098 | REQ-PIPELINE-100 | Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Configuration and settings with a simple test. |
| 099 | REQ-PIPELINE-110 | Email Ives when a build fails | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 0–30 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Notification settings and two forced-failure tests. |
| 100 | REQ-PIPELINE-120 | Write the publish status file with only the content version, the result and the failing item and field | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-CONTENT-030 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small writer with a fixed schema; the open access question is excluded. |
| 101 | REQ-EDIT-010 | Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN | REQ-EDIT-000 | REQ-HTTP-040, REQ-STORE-030 | 0.5–1 day | 60–180 | Claude Opus 5.5 (`claude-opus-5-5`) | Supply-chain control for code that runs with every editor's token. |
| 102 | REQ-CONSENT-040 | Leave the consent banner, the Google tag loader and the measurement ID out of the preview output and the Editing Interface files | REQ-CONSENT-000 | REQ-CONSENT-020, REQ-PIPELINE-060, REQ-EDIT-010, REQ-HTTP-040 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A build-time search over two outputs plus a browser check on two hosts; the rule is unambiguous. |
| 103 | REQ-AUTHN-010 | Sign editors in through the Deno OAuth helper, request only the allowed scopes, and release the token only to https://edit.nina-laaf.de | REQ-AUTHN-000 | REQ-EDIT-010, REQ-HTTP-040 | 1.5–2 days | 300–600 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-critical port of an OAuth relay where upstream behavior (empty allowed-domains, host-only comparison) must be changed, not copied (T-80, T-43). |
| 104 | REQ-AUTHN-020 | Verify the OAuth state of every sign-in with __Host- cookies and reject callbacks with more than one state cookie | REQ-AUTHN-000 | REQ-AUTHN-010 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Cookie-scoping subtleties across hosts of one site (T-78) and CSRF on the sign-in flow need careful, adversarial tests. |
| 105 | REQ-AUTHN-030 | Run the OAuth helper under its own account with a pinned Deno version, pinned dependencies and only the Deno permissions it needs | REQ-AUTHN-000 | REQ-AUTHN-010, REQ-HOST-030 | 0.5–1 day | 40–120 | Claude Opus 5.5 (`claude-opus-5-5`) | Least-privilege runtime configuration for the only internet-facing server-side code (T-80, T-61). |
| 106 | REQ-EDIT-100 | Install the Editing Interface files on the VPS by hand from a tagged release of dalini/nina-site, owned by root and read-only to nginx and the OAuth helper | REQ-EDIT-000 | REQ-EDIT-010, REQ-AUTHN-030 | 1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`) | It is the supply-chain and file-permission control for the code that holds every editor's token, with a verification step that must fail closed. |
| 107 | REQ-AUTHN-040 | Obtain a new token on each sign-in and keep tokens and session values out of URLs and logs | REQ-AUTHN-000 | REQ-AUTHN-020, REQ-PRIV-020 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Verification-heavy work over known places (URLs, nginx and helper logs) with a documented, accepted exception. |
| 108 | REQ-AUTHN-050 | Revoke all Editing Interface tokens of an editor at once, and drill it | REQ-AUTHN-000 | REQ-AUTHN-010 | 0.5 day | 0–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A runbook and a drill using GitHub's own revocation; fully specified by SEC-AUTHN-5. |
| 109 | REQ-HTTP-050 | Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub | REQ-HTTP-000 | REQ-HTTP-040, REQ-EDIT-010 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`) | Fitting a strict CSP to a third-party single-page app without weakening it protects the editors' tokens. |
| 110 | REQ-EDIT-020 | Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-010 | 1–2 days | 150–400 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Declarative CMS configuration following the documented model. |
| 111 | REQ-EDIT-030 | Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-050 | 1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Declarative CMS configuration following the documented model. |
| 112 | REQ-EDIT-110 | Generate an Exhibition entry's slug from its title and start year when it is first saved, and keep it when the entry is edited later | REQ-EDIT-000 | REQ-EDIT-030 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A CMS configuration change with a clear rule and one capability to verify first. |
| 113 | REQ-EDIT-040 | Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-020 | 1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Declarative CMS configuration following the documented model. |
| 114 | REQ-EDIT-050 | Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-040 | 0.5–1 day | 50–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-relevant validation in a third-party UI, where support for content sniffing and pixel checks must be verified. |
| 115 | REQ-EDIT-060 | Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`) | Security verification across every view of a third-party UI, with configuration to restrict formats. |
| 116 | REQ-EDIT-070 | Let Nina hide a single Instagram post and switch the feed off in the Editing Interface | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-060 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A small settings form. |
| 117 | REQ-EDIT-080 | Show who changed each content item and when, and restore an earlier version | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | 0.5–1 day | 30–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Depends on a capability of the editing system that must be verified, with a careful restore path. |
| 118 | REQ-EDIT-090 | Show Nina in the Editing Interface when her changes are not live yet and which item and field failed | REQ-EDIT-000 | REQ-PIPELINE-120, REQ-EDIT-010, REQ-HTTP-050 | 1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Browser code on the editing origin that reads the public status file and the editor's own latest save, must show untrusted text safely, and runs where editor tokens live (T-45, T-60). |
| 119 | REQ-MIGRATION-010 | Import the 53 legacy works with their German title, year, images and image captions into the Content Store | REQ-MIGRATION-000 | REQ-CONTENT-010 | 1–1.5 days | 200–450 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A mapping script from a known, uniform source format. |
| 120 | REQ-MIGRATION-040 | Import each legacy work's materials and dimensions from its legacy fields or a clean first caption, and write a migration report for Nina | REQ-MIGRATION-000 | REQ-MIGRATION-010 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A one-time mapping with a precise precedence rule and known fixture data. |
| 121 | REQ-MIGRATION-020 | Import legacy works without a year as unpublished, publish the rest, and feature the newest work | REQ-MIGRATION-000 | REQ-MIGRATION-010, REQ-CONTENT-040 | 0.5 day | 40–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two fixed rules added to the import. |
| 122 | REQ-MIGRATION-030 | Give every migrated image interim alt text made of the work title and the view number | REQ-MIGRATION-000 | REQ-MIGRATION-010 | 0.5 day | 20–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A single string rule added to the import. |

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
25. REQ-CONTENT-090
26. REQ-OUTPUT-010
27. REQ-OUTPUT-020
28. REQ-OUTPUT-030
29. REQ-OUTPUT-040
30. REQ-LAYOUT-010
31. REQ-NAV-020
32. REQ-NAV-040
33. REQ-NAV-030
34. REQ-NAV-010
35. REQ-WORKS-020
36. REQ-IMAGES-010
37. REQ-HOME-010
38. REQ-WORKS-010
39. REQ-WORKS-030
40. REQ-WORKS-050
41. REQ-IMAGES-020
42. REQ-IMAGES-040
43. REQ-IMAGES-050
44. REQ-IMAGES-030
45. REQ-EXHIBITIONS-010
46. REQ-EXHIBITIONS-030
47. REQ-WORKS-040
48. REQ-EXHIBITIONS-020
49. REQ-HOME-020
50. REQ-NEWS-010
51. REQ-CV-010
52. REQ-CV-020
53. REQ-CV-030
54. REQ-TEXTS-010
55. REQ-DOWNLOADS-010
56. REQ-CONTACT-010
57. REQ-LEGAL-010
58. REQ-INSTAGRAM-010
59. REQ-INSTAGRAM-020
60. REQ-CONSENT-010
61. REQ-PRIV-030
62. REQ-HOST-010
63. REQ-HOST-020
64. REQ-HOST-030
65. REQ-HOST-040
66. REQ-PRIV-020
67. REQ-HTTP-010
68. REQ-HTTP-020
69. REQ-CONSENT-020
70. REQ-CONSENT-050
71. REQ-PERF-010
72. REQ-PRIV-010
73. REQ-CONSENT-030
74. REQ-HTTP-030
75. REQ-HTTP-040
76. REQ-HTTP-060
77. REQ-HTTP-070
78. REQ-STORE-010
79. REQ-STORE-020
80. REQ-STORE-030
81. REQ-STORE-040
82. REQ-STORE-050
83. REQ-STORE-060
84. REQ-PIPELINE-010
85. REQ-PIPELINE-020
86. REQ-PIPELINE-030
87. REQ-PIPELINE-040
88. REQ-PIPELINE-050
89. REQ-PIPELINE-060
90. REQ-PREVIEW-010
91. REQ-PIPELINE-070
92. REQ-PIPELINE-080
93. REQ-PIPELINE-130
94. REQ-PIPELINE-090
95. REQ-PIPELINE-140
96. REQ-INSTAGRAM-050
97. REQ-INSTAGRAM-030
98. REQ-INSTAGRAM-040
99. REQ-PIPELINE-100
100. REQ-PIPELINE-110
101. REQ-PIPELINE-120
102. REQ-EDIT-010
103. REQ-CONSENT-040
104. REQ-AUTHN-010
105. REQ-AUTHN-020
106. REQ-AUTHN-030
107. REQ-EDIT-100
108. REQ-AUTHN-040
109. REQ-AUTHN-050
110. REQ-HTTP-050
111. REQ-EDIT-020
112. REQ-EDIT-030
113. REQ-EDIT-110
114. REQ-EDIT-040
115. REQ-EDIT-050
116. REQ-EDIT-060
117. REQ-EDIT-070
118. REQ-EDIT-080
119. REQ-EDIT-090
120. REQ-MIGRATION-010
121. REQ-MIGRATION-040
122. REQ-MIGRATION-020
123. REQ-MIGRATION-030

## Proposed Commands (not run)

Pre-flight checks for CREATE mode: `gh auth status`; confirm the target repository is `dalini/nina-website` (the specification repository, as in the earlier runs); confirm that `gh issue create` in the installed version supports `--body-file` and `--parent` (otherwise link sub-issues through the GitHub API); confirm no existing issue reuses a planning ID for different scope. No scope is blocked, so a CREATE run may create these issues from the bodies as they are; PQ-67 to PQ-69 are non-blocking and stay marked `TO BE DECIDED` in REQ-EDIT-110 and REQ-MIGRATION-040 until step 10 answers them.

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
gh issue create --title "[REQ-CONTENT-090] Fail the publish when two Exhibition entries share a slug, naming both entries and the slug field" --body-file ".planning/github-issues/024-REQ-CONTENT-090.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-OUTPUT-010] HTML-encode editor text and render rich text only from the restricted format" --body-file ".planning/github-issues/025-REQ-OUTPUT-010.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-020] Treat content fields as data and never evaluate generator syntax inside them" --body-file ".planning/github-issues/026-REQ-OUTPUT-020.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-030] Allow only https: and mailto: links in content and open external links with noopener" --body-file ".planning/github-issues/027-REQ-OUTPUT-030.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-040] Fail the build when a generated page contains a script other than the site's own script files" --body-file ".planning/github-issues/028-REQ-OUTPUT-040.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-LAYOUT-010] Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon" --body-file ".planning/github-issues/029-REQ-LAYOUT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NAV-020] Generate every page in English at / and German under /de/ with a language switch to the same page" --body-file ".planning/github-issues/030-REQ-NAV-020.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-040] Show dates numerically in the page's language, DD.MM.YYYY in German and YYYY-MM-DD in English, with both full dates in a range" --body-file ".planning/github-issues/031-REQ-NAV-040.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-030] Show the other language's value, marked with its lang attribute, when a field's value is empty" --body-file ".planning/github-issues/032-REQ-NAV-030.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-010] Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page" --body-file ".planning/github-issues/033-REQ-NAV-010.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-WORKS-020] Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text" --body-file ".planning/github-issues/034-REQ-WORKS-020.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-IMAGES-010] Publish images only as responsive sizes of at most 2400 px on the long side, never as originals" --body-file ".planning/github-issues/035-REQ-IMAGES-010.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-HOME-010] Show the artist's name and the featured work's image, linked to its work page, on the home page" --body-file ".planning/github-issues/036-REQ-HOME-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-WORKS-010] List all published works newest year first with Nina's order within a year, and show works in public space as a group" --body-file ".planning/github-issues/037-REQ-WORKS-010.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-030] Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking" --body-file ".planning/github-issues/038-REQ-WORKS-030.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-050] Open a work's images in a full-screen image viewer with buttons and arrow keys that stops at the first and last image and shows the position, and fall back to plain links without JavaScript" --body-file ".planning/github-issues/039-REQ-WORKS-050.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-IMAGES-020] Remove embedded metadata from published images except the copyright notice and the creator" --body-file ".planning/github-issues/040-REQ-IMAGES-020.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-040] Reject images larger than 40 megapixels before decoding them fully" --body-file ".planning/github-issues/041-REQ-IMAGES-040.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-050] Recheck the type and size of every file the Site Build publishes and reject SVGs with active content" --body-file ".planning/github-issues/042-REQ-IMAGES-050.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-030] Remove embedded metadata from published PDFs automatically, except the copyright notice and the creator" --body-file ".planning/github-issues/043-REQ-IMAGES-030.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-EXHIBITIONS-010] List each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors and external link" --body-file ".planning/github-issues/044-REQ-EXHIBITIONS-010.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-EXHIBITIONS-030] Give each Exhibition entry its own page with its facts, linked works, description and installation images, and link it from the list" --body-file ".planning/github-issues/045-REQ-EXHIBITIONS-030.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-WORKS-040] Link each work page to the pages of the published Exhibition entries linked to that work" --body-file ".planning/github-issues/046-REQ-WORKS-040.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-EXHIBITIONS-020] Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive" --body-file ".planning/github-issues/047-REQ-EXHIBITIONS-020.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-HOME-020] Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page" --body-file ".planning/github-issues/048-REQ-HOME-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NEWS-010] List News items in their own section of the Exhibitions/News page, newest date first" --body-file ".planning/github-issues/049-REQ-NEWS-010.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-CV-010] Show the CV page with its six sections in order, hide empty sections and list entries newest year first" --body-file ".planning/github-issues/050-REQ-CV-010.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-CV-020] Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works" --body-file ".planning/github-issues/051-REQ-CV-020.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-CV-030] Let Nina hide a derived CV line or replace its generated wording" --body-file ".planning/github-issues/052-REQ-CV-030.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-TEXTS-010] List published Texts in Nina's order and give each its own page" --body-file ".planning/github-issues/053-REQ-TEXTS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-DOWNLOADS-010] Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF" --body-file ".planning/github-issues/054-REQ-DOWNLOADS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTACT-010] Show Nina's email address as a mailto link and link to galerie burster on the Contact page" --body-file ".planning/github-issues/055-REQ-CONTACT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-LEGAL-010] Publish the Impressum and Datenschutzerklärung pages and link them from every page" --body-file ".planning/github-issues/056-REQ-LEGAL-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-INSTAGRAM-010] Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram" --body-file ".planning/github-issues/057-REQ-INSTAGRAM-010.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-020] Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link" --body-file ".planning/github-issues/058-REQ-INSTAGRAM-020.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-CONSENT-010] Show the consent banner with its fixed text until a visitor chooses, store the choice strictly, and let the footer link reopen it" --body-file ".planning/github-issues/059-REQ-CONSENT-010.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-PRIV-030] Administer the Google Analytics property with a second factor, Ives as only administrator and Nina as viewer, and keep Google's default data settings" --body-file ".planning/github-issues/060-REQ-PRIV-030.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-HOST-010] Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly" --body-file ".planning/github-issues/061-REQ-HOST-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-020] Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root" --body-file ".planning/github-issues/062-REQ-HOST-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-030] Run each VPS job and the OAuth helper under its own unprivileged account that can read only its own secret file" --body-file ".planning/github-issues/063-REQ-HOST-030.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-040] Run a local mail relay agent under its own unprivileged account that alone reads the SMTP relay credential, and deliver the VPS alerts to Ives" --body-file ".planning/github-issues/064-REQ-HOST-040.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-020] Delete nginx access logs after 14 days and keep them readable only by the maintainer" --body-file ".planning/github-issues/065-REQ-PRIV-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HTTP-010] Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS" --body-file ".planning/github-issues/066-REQ-HTTP-010.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-020] Send the SEC-HTTP-2 Content Security Policy with only the Google Analytics origins on every public response" --body-file ".planning/github-issues/067-REQ-HTTP-020.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-CONSENT-020] Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage" --body-file ".planning/github-issues/068-REQ-CONSENT-020.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-CONSENT-050] Store the banner version with the consent choice and ask again when the banner version changes" --body-file ".planning/github-issues/069-REQ-CONSENT-050.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-PERF-010] Meet Core Web Vitals \"good\" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run" --body-file ".planning/github-issues/070-REQ-PERF-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-010] Request no third-party resource except the Google Analytics origins, and include no other analytics, tracking, cookies or pixels in any page" --body-file ".planning/github-issues/071-REQ-PRIV-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONSENT-030] Set Google Analytics cookies for the host nina-laaf.de only, verified in a browser test, or record the accepted risk when no tag setting allows it" --body-file ".planning/github-issues/072-REQ-CONSENT-030.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-HTTP-030] Send nosniff and forbid framing of nina-laaf.de by other origins" --body-file ".planning/github-issues/073-REQ-HTTP-030.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-040] Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP without Google origins on the preview site" --body-file ".planning/github-issues/074-REQ-HTTP-040.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-060] Renew the TLS certificates of all three hosts automatically from Let's Encrypt and email Ives about failed renewals" --body-file ".planning/github-issues/075-REQ-HTTP-060.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-070] Limit requests and connections per IP address on all three hosts" --body-file ".planning/github-issues/076-REQ-HTTP-070.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-STORE-010] Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record" --body-file ".planning/github-issues/077-REQ-STORE-010.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-020] Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there" --body-file ".planning/github-issues/078-REQ-STORE-020.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-030] Limit write access to dalini/nina-site to the maintainer" --body-file ".planning/github-issues/079-REQ-STORE-030.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-040] Protect the history of main in dalini/nina-content and require verified commits there" --body-file ".planning/github-issues/080-REQ-STORE-040.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-050] Mirror dalini/nina-content weekly to the VPS, keep 30 days outside the served directories, and test a restore yearly" --body-file ".planning/github-issues/081-REQ-STORE-050.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-060] Document the purge of content from history and backups on a legal request, with a record that names nothing purged" --body-file ".planning/github-issues/082-REQ-STORE-060.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-PIPELINE-010] Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key" --body-file ".planning/github-issues/083-REQ-PIPELINE-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-020] Deploy only the Site Build's generated output" --body-file ".planning/github-issues/084-REQ-PIPELINE-020.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-030] Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile" --body-file ".planning/github-issues/085-REQ-PIPELINE-030.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-040] Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change" --body-file ".planning/github-issues/086-REQ-PIPELINE-040.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-050] Keep the deploy, preview-deploy and content-read secrets in a production environment limited to main" --body-file ".planning/github-issues/087-REQ-PIPELINE-050.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-060] Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key" --body-file ".planning/github-issues/088-REQ-PIPELINE-060.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PREVIEW-010] Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins" --body-file ".planning/github-issues/089-REQ-PREVIEW-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-070] Record what each production deploy was built from and restore the previous version without a content change" --body-file ".planning/github-issues/090-REQ-PIPELINE-070.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-080] Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group" --body-file ".planning/github-issues/091-REQ-PIPELINE-080.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-130] Check main of the Content Store every minute from the VPS, start the build through workflow_dispatch when it changed, and email Ives when the check fails" --body-file ".planning/github-issues/092-REQ-PIPELINE-130.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-090] Process Content Store files without access to the deploy keys or the Instagram token" --body-file ".planning/github-issues/093-REQ-PIPELINE-090.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-140] Restrict the production and preview deploy keys to the deploy's file transfers and reads, with no shell, other command or forwarding" --body-file ".planning/github-issues/094-REQ-PIPELINE-140.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-INSTAGRAM-050] Keep the Instagram token in a file of the deploy account, refresh it monthly with a job under that account, and email Ives when a refresh fails" --body-file ".planning/github-issues/095-REQ-INSTAGRAM-050.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-030] Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks" --body-file ".planning/github-issues/096-REQ-INSTAGRAM-030.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-040] Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails" --body-file ".planning/github-issues/097-REQ-INSTAGRAM-040.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-PIPELINE-100] Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives" --body-file ".planning/github-issues/098-REQ-PIPELINE-100.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-110] Email Ives when a build fails" --body-file ".planning/github-issues/099-REQ-PIPELINE-110.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-120] Write the publish status file with only the content version, the result and the failing item and field" --body-file ".planning/github-issues/100-REQ-PIPELINE-120.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-EDIT-010] Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN" --body-file ".planning/github-issues/101-REQ-EDIT-010.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-CONSENT-040] Leave the consent banner, the Google tag loader and the measurement ID out of the preview output and the Editing Interface files" --body-file ".planning/github-issues/102-REQ-CONSENT-040.md"  # parent: REQ-CONSENT-000
gh issue create --title "[REQ-AUTHN-010] Sign editors in through the Deno OAuth helper, request only the allowed scopes, and release the token only to https://edit.nina-laaf.de" --body-file ".planning/github-issues/103-REQ-AUTHN-010.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-020] Verify the OAuth state of every sign-in with __Host- cookies and reject callbacks with more than one state cookie" --body-file ".planning/github-issues/104-REQ-AUTHN-020.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-030] Run the OAuth helper under its own account with a pinned Deno version, pinned dependencies and only the Deno permissions it needs" --body-file ".planning/github-issues/105-REQ-AUTHN-030.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-EDIT-100] Install the Editing Interface files on the VPS by hand from a tagged release of dalini/nina-site, owned by root and read-only to nginx and the OAuth helper" --body-file ".planning/github-issues/106-REQ-EDIT-100.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-AUTHN-040] Obtain a new token on each sign-in and keep tokens and session values out of URLs and logs" --body-file ".planning/github-issues/107-REQ-AUTHN-040.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-AUTHN-050] Revoke all Editing Interface tokens of an editor at once, and drill it" --body-file ".planning/github-issues/108-REQ-AUTHN-050.md"  # parent: REQ-AUTHN-000
gh issue create --title "[REQ-HTTP-050] Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub" --body-file ".planning/github-issues/109-REQ-HTTP-050.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-EDIT-020] Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings" --body-file ".planning/github-issues/110-REQ-EDIT-020.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-030] Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks" --body-file ".planning/github-issues/111-REQ-EDIT-030.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-110] Generate an Exhibition entry's slug from its title and start year when it is first saved, and keep it when the entry is edited later" --body-file ".planning/github-issues/112-REQ-EDIT-110.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-040] Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order" --body-file ".planning/github-issues/113-REQ-EDIT-040.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-050] Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload" --body-file ".planning/github-issues/114-REQ-EDIT-050.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-060] Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup" --body-file ".planning/github-issues/115-REQ-EDIT-060.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-070] Let Nina hide a single Instagram post and switch the feed off in the Editing Interface" --body-file ".planning/github-issues/116-REQ-EDIT-070.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-080] Show who changed each content item and when, and restore an earlier version" --body-file ".planning/github-issues/117-REQ-EDIT-080.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-090] Show Nina in the Editing Interface when her changes are not live yet and which item and field failed" --body-file ".planning/github-issues/118-REQ-EDIT-090.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-MIGRATION-010] Import the 53 legacy works with their German title, year, images and image captions into the Content Store" --body-file ".planning/github-issues/119-REQ-MIGRATION-010.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-040] Import each legacy work's materials and dimensions from its legacy fields or a clean first caption, and write a migration report for Nina" --body-file ".planning/github-issues/120-REQ-MIGRATION-040.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-020] Import legacy works without a year as unpublished, publish the rest, and feature the newest work" --body-file ".planning/github-issues/121-REQ-MIGRATION-020.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-030] Give every migrated image interim alt text made of the work title and the view number" --body-file ".planning/github-issues/122-REQ-MIGRATION-030.md"  # parent: REQ-MIGRATION-000
```

## CREATE Run Log

### 2026-10-31: CREATE attempt stopped at pre-flight

Step 9 with `Execution mode: CREATE`, run against the fourth-run drafts above. The specs have not changed since those drafts (the last spec commit is step 10 a third time, before the drafts), so they were to be created as they are.

| Pre-flight check | Result |
|---|---|
| 1. `gh auth status` | **FAILED**: "You are not logged into any GitHub hosts." |
| 2. Target repository | `origin` is `https://github.com/dalini/nina-website.git` (matches) |
| 3. `--body-file` and `--parent` | Both supported by `gh` 2.103.3 |
| 4. Existing issues reusing planning IDs | Not checked: needs an authenticated `gh` |

Nothing was created, changed or deleted on GitHub: 0 created, 0 reused, 0 failed. The `{{ISSUE_URL:<ID>}}` placeholders stay as they are. To continue, run `gh auth login` (or set `GH_TOKEN`) with an account that can create issues in `dalini/nina-website`, then run step 9 again with `Execution mode: CREATE`.

### 2026-10-31: CREATE run completed

After `gh auth login` as `dalini`, every pre-flight check passed:

| Pre-flight check | Result |
|---|---|
| 1. `gh auth status` | Logged in to github.com as `dalini` |
| 2. Target repository | `dalini/nina-website`; `viewerPermission` ADMIN, issues enabled |
| 3. `--body-file` and `--parent` | Both supported by `gh` 2.103.3 |
| 4. Existing issues reusing planning IDs | None: the repository had no issues (#1 is a pull request) |

The specs had not changed since the fourth-run drafts, so the issues were created from those bodies as they are:

1. Created 123 issues (1 epic, 15 workstreams, 107 leaves) in the Topological Creation Order, each with `--parent` set to its parent's number. Verified: every parent link matches this plan, and the epic has 29 direct sub-issues (15 workstreams, 14 leaves).
2. Replaced every `{{ISSUE_URL:<ID>}}` placeholder with the captured URL in the local body files and this plan, then updated each issue's body with `gh issue edit --body-file`. Verified: no issue body on GitHub still contains a placeholder.
3. Linked the 231 edges of the Dependency Graph as GitHub "blocked by" dependencies (`POST /repos/dalini/nina-website/issues/<n>/dependencies/blocked_by`). Verified: 231 blocked-by links in total.

Result: 123 created, 0 reused, 0 failed. Epic: https://github.com/dalini/nina-website/issues/2

#### Issue URLs

| ID | Issue |
|---|---|
| REQ-EPIC-001 | https://github.com/dalini/nina-website/issues/2 |
| REQ-CONTENT-000 | https://github.com/dalini/nina-website/issues/3 |
| REQ-OUTPUT-000 | https://github.com/dalini/nina-website/issues/4 |
| REQ-NAV-000 | https://github.com/dalini/nina-website/issues/5 |
| REQ-WORKS-000 | https://github.com/dalini/nina-website/issues/6 |
| REQ-IMAGES-000 | https://github.com/dalini/nina-website/issues/7 |
| REQ-EXHIBITIONS-000 | https://github.com/dalini/nina-website/issues/8 |
| REQ-CV-000 | https://github.com/dalini/nina-website/issues/9 |
| REQ-INSTAGRAM-000 | https://github.com/dalini/nina-website/issues/10 |
| REQ-CONSENT-000 | https://github.com/dalini/nina-website/issues/11 |
| REQ-HTTP-000 | https://github.com/dalini/nina-website/issues/12 |
| REQ-STORE-000 | https://github.com/dalini/nina-website/issues/13 |
| REQ-PIPELINE-000 | https://github.com/dalini/nina-website/issues/14 |
| REQ-AUTHN-000 | https://github.com/dalini/nina-website/issues/15 |
| REQ-EDIT-000 | https://github.com/dalini/nina-website/issues/16 |
| REQ-MIGRATION-000 | https://github.com/dalini/nina-website/issues/17 |
| REQ-CONTENT-010 | https://github.com/dalini/nina-website/issues/18 |
| REQ-CONTENT-020 | https://github.com/dalini/nina-website/issues/19 |
| REQ-CONTENT-050 | https://github.com/dalini/nina-website/issues/20 |
| REQ-CONTENT-060 | https://github.com/dalini/nina-website/issues/21 |
| REQ-CONTENT-030 | https://github.com/dalini/nina-website/issues/22 |
| REQ-CONTENT-040 | https://github.com/dalini/nina-website/issues/23 |
| REQ-CONTENT-070 | https://github.com/dalini/nina-website/issues/24 |
| REQ-CONTENT-080 | https://github.com/dalini/nina-website/issues/25 |
| REQ-CONTENT-090 | https://github.com/dalini/nina-website/issues/26 |
| REQ-OUTPUT-010 | https://github.com/dalini/nina-website/issues/27 |
| REQ-OUTPUT-020 | https://github.com/dalini/nina-website/issues/28 |
| REQ-OUTPUT-030 | https://github.com/dalini/nina-website/issues/29 |
| REQ-OUTPUT-040 | https://github.com/dalini/nina-website/issues/30 |
| REQ-LAYOUT-010 | https://github.com/dalini/nina-website/issues/31 |
| REQ-NAV-020 | https://github.com/dalini/nina-website/issues/32 |
| REQ-NAV-040 | https://github.com/dalini/nina-website/issues/33 |
| REQ-NAV-030 | https://github.com/dalini/nina-website/issues/34 |
| REQ-NAV-010 | https://github.com/dalini/nina-website/issues/35 |
| REQ-WORKS-020 | https://github.com/dalini/nina-website/issues/36 |
| REQ-IMAGES-010 | https://github.com/dalini/nina-website/issues/37 |
| REQ-HOME-010 | https://github.com/dalini/nina-website/issues/38 |
| REQ-WORKS-010 | https://github.com/dalini/nina-website/issues/39 |
| REQ-WORKS-030 | https://github.com/dalini/nina-website/issues/40 |
| REQ-WORKS-050 | https://github.com/dalini/nina-website/issues/41 |
| REQ-IMAGES-020 | https://github.com/dalini/nina-website/issues/42 |
| REQ-IMAGES-040 | https://github.com/dalini/nina-website/issues/43 |
| REQ-IMAGES-050 | https://github.com/dalini/nina-website/issues/44 |
| REQ-IMAGES-030 | https://github.com/dalini/nina-website/issues/45 |
| REQ-EXHIBITIONS-010 | https://github.com/dalini/nina-website/issues/46 |
| REQ-EXHIBITIONS-030 | https://github.com/dalini/nina-website/issues/47 |
| REQ-WORKS-040 | https://github.com/dalini/nina-website/issues/48 |
| REQ-EXHIBITIONS-020 | https://github.com/dalini/nina-website/issues/49 |
| REQ-HOME-020 | https://github.com/dalini/nina-website/issues/50 |
| REQ-NEWS-010 | https://github.com/dalini/nina-website/issues/51 |
| REQ-CV-010 | https://github.com/dalini/nina-website/issues/52 |
| REQ-CV-020 | https://github.com/dalini/nina-website/issues/53 |
| REQ-CV-030 | https://github.com/dalini/nina-website/issues/54 |
| REQ-TEXTS-010 | https://github.com/dalini/nina-website/issues/55 |
| REQ-DOWNLOADS-010 | https://github.com/dalini/nina-website/issues/56 |
| REQ-CONTACT-010 | https://github.com/dalini/nina-website/issues/57 |
| REQ-LEGAL-010 | https://github.com/dalini/nina-website/issues/58 |
| REQ-INSTAGRAM-010 | https://github.com/dalini/nina-website/issues/59 |
| REQ-INSTAGRAM-020 | https://github.com/dalini/nina-website/issues/60 |
| REQ-CONSENT-010 | https://github.com/dalini/nina-website/issues/61 |
| REQ-PRIV-030 | https://github.com/dalini/nina-website/issues/62 |
| REQ-HOST-010 | https://github.com/dalini/nina-website/issues/63 |
| REQ-HOST-020 | https://github.com/dalini/nina-website/issues/64 |
| REQ-HOST-030 | https://github.com/dalini/nina-website/issues/65 |
| REQ-HOST-040 | https://github.com/dalini/nina-website/issues/66 |
| REQ-PRIV-020 | https://github.com/dalini/nina-website/issues/67 |
| REQ-HTTP-010 | https://github.com/dalini/nina-website/issues/68 |
| REQ-HTTP-020 | https://github.com/dalini/nina-website/issues/69 |
| REQ-CONSENT-020 | https://github.com/dalini/nina-website/issues/70 |
| REQ-CONSENT-050 | https://github.com/dalini/nina-website/issues/71 |
| REQ-PERF-010 | https://github.com/dalini/nina-website/issues/72 |
| REQ-PRIV-010 | https://github.com/dalini/nina-website/issues/73 |
| REQ-CONSENT-030 | https://github.com/dalini/nina-website/issues/74 |
| REQ-HTTP-030 | https://github.com/dalini/nina-website/issues/75 |
| REQ-HTTP-040 | https://github.com/dalini/nina-website/issues/76 |
| REQ-HTTP-060 | https://github.com/dalini/nina-website/issues/77 |
| REQ-HTTP-070 | https://github.com/dalini/nina-website/issues/78 |
| REQ-STORE-010 | https://github.com/dalini/nina-website/issues/79 |
| REQ-STORE-020 | https://github.com/dalini/nina-website/issues/80 |
| REQ-STORE-030 | https://github.com/dalini/nina-website/issues/81 |
| REQ-STORE-040 | https://github.com/dalini/nina-website/issues/82 |
| REQ-STORE-050 | https://github.com/dalini/nina-website/issues/83 |
| REQ-STORE-060 | https://github.com/dalini/nina-website/issues/84 |
| REQ-PIPELINE-010 | https://github.com/dalini/nina-website/issues/85 |
| REQ-PIPELINE-020 | https://github.com/dalini/nina-website/issues/86 |
| REQ-PIPELINE-030 | https://github.com/dalini/nina-website/issues/87 |
| REQ-PIPELINE-040 | https://github.com/dalini/nina-website/issues/88 |
| REQ-PIPELINE-050 | https://github.com/dalini/nina-website/issues/89 |
| REQ-PIPELINE-060 | https://github.com/dalini/nina-website/issues/90 |
| REQ-PREVIEW-010 | https://github.com/dalini/nina-website/issues/91 |
| REQ-PIPELINE-070 | https://github.com/dalini/nina-website/issues/92 |
| REQ-PIPELINE-080 | https://github.com/dalini/nina-website/issues/93 |
| REQ-PIPELINE-130 | https://github.com/dalini/nina-website/issues/94 |
| REQ-PIPELINE-090 | https://github.com/dalini/nina-website/issues/95 |
| REQ-PIPELINE-140 | https://github.com/dalini/nina-website/issues/96 |
| REQ-INSTAGRAM-050 | https://github.com/dalini/nina-website/issues/97 |
| REQ-INSTAGRAM-030 | https://github.com/dalini/nina-website/issues/98 |
| REQ-INSTAGRAM-040 | https://github.com/dalini/nina-website/issues/99 |
| REQ-PIPELINE-100 | https://github.com/dalini/nina-website/issues/100 |
| REQ-PIPELINE-110 | https://github.com/dalini/nina-website/issues/101 |
| REQ-PIPELINE-120 | https://github.com/dalini/nina-website/issues/102 |
| REQ-EDIT-010 | https://github.com/dalini/nina-website/issues/103 |
| REQ-CONSENT-040 | https://github.com/dalini/nina-website/issues/104 |
| REQ-AUTHN-010 | https://github.com/dalini/nina-website/issues/105 |
| REQ-AUTHN-020 | https://github.com/dalini/nina-website/issues/106 |
| REQ-AUTHN-030 | https://github.com/dalini/nina-website/issues/107 |
| REQ-EDIT-100 | https://github.com/dalini/nina-website/issues/108 |
| REQ-AUTHN-040 | https://github.com/dalini/nina-website/issues/109 |
| REQ-AUTHN-050 | https://github.com/dalini/nina-website/issues/110 |
| REQ-HTTP-050 | https://github.com/dalini/nina-website/issues/111 |
| REQ-EDIT-020 | https://github.com/dalini/nina-website/issues/112 |
| REQ-EDIT-030 | https://github.com/dalini/nina-website/issues/113 |
| REQ-EDIT-110 | https://github.com/dalini/nina-website/issues/114 |
| REQ-EDIT-040 | https://github.com/dalini/nina-website/issues/115 |
| REQ-EDIT-050 | https://github.com/dalini/nina-website/issues/116 |
| REQ-EDIT-060 | https://github.com/dalini/nina-website/issues/117 |
| REQ-EDIT-070 | https://github.com/dalini/nina-website/issues/118 |
| REQ-EDIT-080 | https://github.com/dalini/nina-website/issues/119 |
| REQ-EDIT-090 | https://github.com/dalini/nina-website/issues/120 |
| REQ-MIGRATION-010 | https://github.com/dalini/nina-website/issues/121 |
| REQ-MIGRATION-040 | https://github.com/dalini/nina-website/issues/122 |
| REQ-MIGRATION-020 | https://github.com/dalini/nina-website/issues/123 |
| REQ-MIGRATION-030 | https://github.com/dalini/nina-website/issues/124 |
