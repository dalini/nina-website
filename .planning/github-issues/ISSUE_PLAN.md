# ISSUE_PLAN — nina-laaf.de

Step 9 draft (`prompts/09-github-epic.md`), 2026-10-31. Nothing on GitHub was changed.

## Run Inputs

- `Scope: ALL`
- `Execution mode: DRAFT_ONLY` — local files only; no mutating `gh` command was run.
- Sources read: `REQUIREMENTS.md`, `ARCHITECTURE.md`, `SECURITY.md`, `DESIGN.md`, `REQUIREMENT_TEMPLATE.md`; `CLAUDE.md` followed; `style-guide.html` cited for UI issues. `docs/` read only to check the shape of the legacy works (FR-11.1); it is not specification.
- Result: 36 draft issue bodies: 1 epic, 6 workstreams, 29 leaf issues.

## Planning Rules Applied

- **Blocking** means the answer changes observable behavior, roles or permissions, auth, an API contract, data semantics, a trust boundary, a security control, failure behavior, or what an acceptance test can assert (prompt definition). Scope that depends on a blocking question is marked under Blocked Scope and is **not** decomposed into issues.
- **Non-blocking** questions are technology selections, orderings, placements and content choices that leave every acceptance criterion pass/fail as written. They appear as `TO BE DECIDED` in the issues' Open Decisions. They still have to be answered before work on those issues starts, because an implementer must not make them alone (step 10).
- Where a source states something plainly, the drafts follow it as written. FR-1.4, for example, makes every text field bilingual except work titles (FR-1.5).
- Standards Alignment: SECURITY.md uses no ASVS identifiers until they are verified, so every issue carries `TO BE DECIDED` there. Only references SECURITY.md already cites (`REF-*`) or that define the mechanism (RFC 6797, WCAG 2.2, W3C CSP) are listed. Threat references are the STRIDE categories and T-IDs of SECURITY.md Threat Model v1, plus CWE identifiers where they match directly.
- Models were verified against Anthropic's models overview (https://platform.claude.com/docs/models/overview) on 2026-10-31: Claude Fable 5.1 `claude-fable-5-1`, Claude Opus 5.5 `claude-opus-5-5`, Claude Sonnet 5.5 `claude-sonnet-5-5`. No issue is assigned to Fable (no leaf is long-horizon or cross-boundary) or to OpenAI GPT (the project uses no OpenAI tooling).

## Open Questions

### Blocking (must be answered before a CREATE run)

| ID | Question | Source and conflict | Affected IDs |
|---|---|---|---|
| PQ-1 | Which editing system is used, and what are the Editing Interface ↔ Content Store write contract, the publish trigger to the Site Build, and the publish-status format the Editing Interface reads (DF-9)? | ARCHITECTURE.md AQ-3, Required Architecture Inputs (API style: TO BE DECIDED), DR-1, DR-8, DR-9 | FR-10.1 to FR-10.8; SEC-HTTP-4, SEC-OUT-4, SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5 (uploads), SEC-PRIV-4 (uploads), SEC-AUTHZ-1 to -3, SEC-AUDIT-1, SEC-CICD-4 |
| PQ-2 | How do editors authenticate, is a second factor required, and what are the session timeouts? | SECURITY.md SQ-1 | SEC-AUTHN-1, SEC-AUTHN-2, SEC-AUTHN-3; FR-10.5; editor-only previews (SEC-CICD-3 option) |
| PQ-3 | Who besides Nina may edit, and does Nina name another owner who may add or remove editors? | SECURITY.md SQ-2 | SEC-AUTHZ-1 (allow-list), SEC-AUTHZ-2; FR-10.5 |
| PQ-4 | Who is the maintainer, which access does that role hold, and how are templates and pipeline files kept outside editors' write access? | SECURITY.md SQ-10; ARCHITECTURE.md DR-8 | SEC-AUTHZ-3, SEC-AUTHZ-4, SEC-AVAIL-1 |
| PQ-5 | Where are image originals stored, are responsive sizes produced by the Site Build or by the host, what are the upload size and pixel limits, what is the performance budget, and are images protected (watermarks, limited size)? The answer decides which component processes untrusted images. | ARCHITECTURE.md AQ-5; REQUIREMENTS.md NFR-1, OQ-9; SECURITY.md SQ-8, SEC-INPUT-5, T-8, T-17, T-25 | NFR-1; SEC-INPUT-5; SEC-PRIV-4; FR-10.2 |
| PQ-6 | Which image and PDF metadata fields are kept when location and device data are removed? | SECURITY.md SQ-7, SEC-PRIV-4 | SEC-PRIV-4 |
| PQ-7 | Instagram feed: loaded in the browser after consent, or copied at build time? How is consent stored, how is Instagram script isolated, which access method and token lifetime, are posts shown without Nina's review, what happens when a build-time refresh fails (last posts or none), and on which page does the feed appear? | REQUIREMENTS.md OQ-6, FR-8.2, FR-8.5; SECURITY.md SQ-4, SQ-15; ARCHITECTURE.md Instagram Feed Integration | FR-8.2, FR-8.3, FR-8.4 (feed states), FR-8.5; SEC-PRIV-2, SEC-PRIV-3, SEC-OUT-3, SEC-OUT-5, SEC-OUT-6, SEC-SECRET-3, SEC-HTTP-2 (Instagram origins) |
| PQ-8 | Which language does a visitor see who has not chosen one, and what does the site root show? | REQUIREMENTS.md OQ-2, FR-1.6 | FR-1.6 |
| PQ-9 | What does the visitor see when one language version of a field is empty? This also decides how migrated works with German-only materials appear. | REQUIREMENTS.md OQ-10, FR-1.7 | FR-1.7; visible result of FR-11.1 |
| PQ-10 | Does the home page show one large featured image or a selection, and what home page content exists besides the artist's name? | REQUIREMENTS.md OQ-3, FR-2.1 | FR-2.1, FR-2.2 |
| PQ-11 | What dates does an Exhibition/News entry have (one date, start and end, optional end), are boundary days inclusive, and against which time zone is the current date taken for upcoming/current/past? How do News entries fit FR-4.2, whose type list (solo, two-person, group, fair) has no news type? | REQUIREMENTS.md FR-4.1, FR-4.2 (gap; no source defines the date fields or a news type); ARCHITECTURE.md Site Build (scheduled builds ASSUMPTION) | FR-4.1 to FR-4.5, FR-3.9; scheduled builds |
| PQ-12 | Who holds the registrar, DNS and hosting accounts for `nina-laaf.de` today, and who will? | SECURITY.md SQ-13; ARCHITECTURE.md AQ-4 | SEC-HOST-1 |
| PQ-13 | FR-3.2 says a work page MUST show materials and dimensions, but FR-10.6 does not require them on save. Are they required? Which fields are required for Texts, CV entries and Downloads, and must the Contact email address and the Impressum and Datenschutzerklärung Pages exist for a build to succeed? | REQUIREMENTS.md FR-3.2 vs FR-10.6 (conflict); DR-3 | FR-3.2 (empty materials or dimensions); required-field rules beyond FR-10.6; missing Contact email or legal Page (FR-7.1, FR-9.1) |
| PQ-14 | How often are backups made, where are they kept, for how long, and who tests restoring? Must content removed for legal reasons also be removed from history and backups, and who may do that? | SECURITY.md SQ-11, SQ-12, SEC-AVAIL-1, SEC-AUDIT-1 | SEC-AVAIL-1; SEC-AUDIT-1 exceptions |
| PQ-15 | Legacy migration: which of the 53 legacy works are selected, and how are their single-language values mapped? Legacy materials exist only in German, and legacy alt text is only the work title, while FR-1.4 needs both languages and FR-3.7/FR-10.6 need alt text. Who writes the English values and real alt text? | REQUIREMENTS.md OQ-1, FR-11.1 vs FR-1.4, FR-3.7, FR-10.6 (conflict); legacy format checked in `docs/legacy-content/works/` | FR-11.1, FR-11.2 (import part) |

### Non-blocking (TO BE DECIDED in the affected issues; answer before work starts)

| ID | Question | Source | Affected |
|---|---|---|---|
| PQ-16 | Which static site generator? (Some test inputs, e.g. template syntax for REQ-OUTPUT-020, depend on it.) | ARCHITECTURE.md AQ-1 | Every Site Build issue |
| PQ-17 | Which hosting provider and CI/CD platform? Does the host make previews? What are the HSTS max-age and exact CSP directives? Which secret store, and who rotates secrets? What does the host log about visitors? Does the provider absorb traffic floods, and is a cost limit needed? | ARCHITECTURE.md AQ-4; SECURITY.md SEC-HTTP-1, SEC-HTTP-2, SQ-3, SQ-6, SQ-14 | REQ-HTTP-*, REQ-PIPELINE-*, REQ-LEGAL-010 (logs in the Datenschutzerklärung) |
| PQ-18 | Content storage format and the form of the Content Store (version-controlled repository with large-file storage is an ASSUMPTION). | ARCHITECTURE.md Data model expectations, AQ-5, DR-4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-PIPELINE-010 |
| PQ-19 | Order of lists (works, texts, CV entries, downloads), and the pages that carry the downloads (FR-6.1) and the Instagram link (FR-8.1). No source fixes either. | REQUIREMENTS.md FR-3.1, FR-5.1, FR-5.2, FR-6.1, FR-8.1 | REQ-WORKS-010, REQ-TEXTS-010, REQ-CV-010, REQ-DOWNLOADS-010, REQ-INSTAGRAM-010 |
| PQ-20 | Which rich-text elements are allowed beyond the SEC-OUT-1 examples (headings, emphasis, links, lists), and which fields are rich text? | SECURITY.md SEC-OUT-1 | REQ-OUTPUT-010, REQ-CONTENT-020, REQ-TEXTS-010 |
| PQ-21 | Which browsers and versions count as "current" for NFR-4? | REQUIREMENTS.md NFR-4 | All UI issues |
| PQ-22 | Which portfolio PDFs are offered? | REQUIREMENTS.md OQ-4 | REQ-DOWNLOADS-010 (content only) |
| PQ-23 | Are the CV sections for solo exhibitions, group exhibitions and works in public space entered as their own CV entries (as the sources read today), or derived from Exhibition entries and public-space works? Is the short bio a CV entry? Are empty sections shown? | REQUIREMENTS.md FR-5.2, Business objects | REQ-CONTENT-020, REQ-CV-010 |
| PQ-24 | Is a work's year always a single year, or can it be a range? | REQUIREMENTS.md FR-3.2 | REQ-CONTENT-010, REQ-WORKS-020 |
| PQ-25 | Design open questions: brand direction, header mark or wordmark, custom typeface, dark mode. | DESIGN.md DQ-1 to DQ-4 | REQ-NAV-010 (header), all UI issues |
| PQ-26 | Is galerie burster named as the contact for sales? (This is Contact Page wording.) | REQUIREMENTS.md OQ-5, FR-7.2 | REQ-CONTACT-010 (content only) |
| PQ-27 | Does the Public Site need any client-side code beyond the consent-gated feed? No drafted issue needs any; SEC-OUT-5 applies as soon as some is added. | ARCHITECTURE.md AQ-2; SECURITY.md SEC-OUT-5 | SEC-OUT-5; REQ-NAV-020 |
| PQ-28 | What is the security assurance target (e.g. an ASVS 5.0.0 level)? This decides the Standards Alignment mappings. | SECURITY.md SQ-9 | Standards Alignment of every issue |

Recorded, not decomposed: REQUIREMENTS.md OQ-7 (newsletter) and OQ-8 (analytics) have no requirement yet. The default for OQ-8 (no analytics, SEC-PRIV-5) is implemented in REQ-PRIV-010. FR-10.4 maximum publish delay and FR-10.8 notification recipients are `TO BE DECIDED` in the sources and stay so in REQ-PIPELINE-010 and REQ-CONTENT-030.

## Blocked Scope

These requirements have no issues yet. After step 10 answers the listed questions, re-run step 9 to decompose them. The specs will have changed by then, so the prompt re-decomposes rather than creating from these drafts as-is.

| Capability | Blocking questions | Requirements |
|---|---|---|
| Content editing (Editing Interface) | PQ-1, PQ-2, PQ-3, PQ-4, PQ-5, PQ-6 | FR-10.1 to FR-10.8 (editor side), SEC-AUTHN-1 to -3, SEC-AUTHZ-1 to -3, SEC-HTTP-4, SEC-OUT-4, SEC-INPUT-1, SEC-INPUT-2, SEC-CICD-4, DR-9 |
| Content Store access, history and backups | PQ-1, PQ-4, PQ-14 | SEC-AUTHZ-4, SEC-AUDIT-1, SEC-AVAIL-1, FR-10.7 |
| Images: responsive sizes, processing limits, metadata | PQ-5, PQ-6 | NFR-1, SEC-INPUT-5, SEC-PRIV-4 (images and PDFs) |
| Home page | PQ-10 | FR-2.1, FR-2.2 |
| Exhibitions and News | PQ-11 | FR-4.1 to FR-4.5, FR-3.9, scheduled builds |
| Instagram feed | PQ-7 | FR-8.2, FR-8.3, FR-8.4 (feed states), FR-8.5, SEC-PRIV-2, SEC-PRIV-3, SEC-OUT-3, SEC-OUT-5, SEC-OUT-6, SEC-SECRET-3, SEC-HTTP-2 (Instagram origins) |
| Default language and empty-language fallback | PQ-8, PQ-9 | FR-1.6, FR-1.7 |
| Required fields beyond FR-10.6 | PQ-13 | FR-3.2 (empty materials/dimensions), required fields of Text, CV entry, Download, missing Contact email or legal Page |
| Domain, DNS and hosting accounts | PQ-12 | SEC-HOST-1 |
| Legacy migration | PQ-15, PQ-9 | FR-11.1, FR-11.2 (import) |

Release gate: until the Images row is unblocked and done, REQ-WORKS-030 and REQ-DOWNLOADS-010 would publish original images and unchecked PDFs (NFR-1, SEC-PRIV-4, SECURITY.md T-25). The epic stays unreleasable until then, and both issues say so in their Constraints.

## Coverage

| Requirement ID | Issue IDs | Boundary | Security rules | Design sections | Status |
|---|---|---|---|---|---|
| FR-1.1 | REQ-NAV-010 | Site Build, Public Site (TB-1) | — | Components (Links, Focus state) | COVERED (Exhibitions/News link target asserted once PQ-11 scope exists) |
| FR-1.2 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-1.3 | REQ-NAV-020 | Site Build, Public Site (TB-1) | SEC-OUT-5 (only with client-side code, PQ-27) | Components (Language switch) | COVERED |
| FR-1.4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-NAV-020 | Content Store, Site Build (TB-4) | SEC-OUT-1 | — | COVERED |
| FR-1.5 | REQ-CONTENT-010, REQ-NAV-020, REQ-WORKS-020 | Content Store, Site Build | — | Typography (original casing) | COVERED |
| FR-1.6 | — | Site Build, Public Site (TB-1) | — | Components (Language switch) | BLOCKED (PQ-8) |
| FR-1.7 | — | Site Build, Public Site (TB-1) | — | — | BLOCKED (PQ-9) |
| FR-2.1 | — | Site Build, Public Site (TB-1) | — | — | BLOCKED (PQ-10) |
| FR-2.2 | — | Site Build, Public Site (TB-1) | — | — | BLOCKED (PQ-10) |
| FR-3.1 | REQ-WORKS-010, REQ-CONTENT-040 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | Layout and Spacing (Grid) | COVERED |
| FR-3.2 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | PARTIALLY COVERED (empty materials/dimensions: PQ-13) |
| FR-3.3 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.4 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.5 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.6 | REQ-CONTENT-010, REQ-WORKS-030 | Site Build, Public Site (TB-1) | — | Layout and Spacing (no cropping) | COVERED (image sizes: NFR-1, blocked) |
| FR-3.7 | REQ-CONTENT-010, REQ-WORKS-030 | Site Build, Public Site (TB-1) | — | Components (Image with credit), Accessibility | COVERED |
| FR-3.8 | REQ-CONTENT-010, REQ-WORKS-010 | Site Build, Public Site (TB-1) | — | Layout and Spacing (Grid) | COVERED |
| FR-3.9 | — | Site Build, Public Site (TB-1) | — | — | BLOCKED (PQ-11) |
| FR-4.1 | — | Content Store, Site Build | — | — | BLOCKED (PQ-11) |
| FR-4.2 | — | Content Store, Site Build | — | — | BLOCKED (PQ-11) |
| FR-4.3 | — | Content Store, Site Build | SEC-OUT-2 (FR-4.3 links) | — | BLOCKED (PQ-11) |
| FR-4.4 | — | Content Store, Site Build | — | — | BLOCKED (PQ-11) |
| FR-4.5 | — | Content Store, Site Build | — | — | BLOCKED (PQ-11) |
| FR-5.1 | REQ-CONTENT-020, REQ-TEXTS-010 | Site Build, Public Site (TB-1) | SEC-OUT-1, SEC-INPUT-4 | Typography | COVERED |
| FR-5.2 | REQ-CONTENT-020, REQ-CV-010 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Typography | COVERED |
| FR-6.1 | REQ-CONTENT-020, REQ-DOWNLOADS-010 | Site Build, Public Site (TB-1) | SEC-INPUT-4; SEC-PRIV-4 (blocked) | Components (Links) | COVERED (placement PQ-19; PDF metadata release gate) |
| FR-6.2 | REQ-DOWNLOADS-010 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-7.1 | REQ-CONTENT-020, REQ-CONTACT-010 | Site Build, Public Site (TB-1) | SEC-OUT-2 | Components (Links) | COVERED |
| FR-7.2 | REQ-CONTENT-020, REQ-CONTACT-010 | Site Build, Public Site (TB-1) | SEC-OUT-2 | Components (Links) | COVERED |
| FR-7.3 | — | — | — | — | OUT OF SCOPE (not part of this release, FR-7.3) |
| FR-8.1 | REQ-INSTAGRAM-010 | Site Build, Public Site (TB-1) | SEC-PRIV-1, SEC-OUT-2 | Components (Links) | COVERED |
| FR-8.2 | — | Instagram Feed Integration (TB-5) | SEC-PRIV-2, SEC-PRIV-3, SEC-OUT-3 | Components (Consent placeholder) | BLOCKED (PQ-7) |
| FR-8.3 | — | Instagram Feed Integration (TB-5) | SEC-PRIV-2, SEC-PRIV-3, SEC-OUT-3 | Components (Consent placeholder) | BLOCKED (PQ-7) |
| FR-8.5 | — | Instagram Feed Integration (TB-5) | SEC-PRIV-2, SEC-PRIV-3, SEC-OUT-3 | Components (Consent placeholder) | BLOCKED (PQ-7) |
| FR-8.4 | REQ-INSTAGRAM-010 | Public Site (TB-1, TB-5) | SEC-PRIV-1 | Components (Consent placeholder) | PARTIALLY COVERED (link; feed states blocked by PQ-7) |
| FR-9.1 | REQ-LEGAL-010 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-10.1 | — | Editing Interface (TB-2, TB-3) | SEC-AUTHN-1, SEC-AUTHZ-1 | Components (Inputs and form feedback) | BLOCKED (PQ-1) |
| FR-10.2 | — | Editing Interface (TB-2) | SEC-INPUT-1, SEC-INPUT-2, SEC-PRIV-4 | Components (Inputs and form feedback) | BLOCKED (PQ-1, PQ-5, PQ-6) |
| FR-10.3 | REQ-CONTENT-010 | Content Store; Editing Interface | — | — | PARTIALLY COVERED (flags in the model; setting them: PQ-1) |
| FR-10.4 | REQ-PIPELINE-010 | Site Build → Static Hosting (TB-4) | SEC-CICD-1, SEC-CICD-4 | — | PARTIALLY COVERED (deploy of main; save/publish trigger: PQ-1) |
| FR-10.5 | — | Editing Interface, Content Store | SEC-AUTHN-1, SEC-AUTHZ-1, SEC-AUTHZ-2 | — | BLOCKED (PQ-1, PQ-2, PQ-3) |
| FR-10.6 | REQ-CONTENT-010, REQ-CONTENT-030 | Site Build (TB-4); Editing Interface | SEC-INPUT-3 | Components (Inputs and form feedback) | PARTIALLY COVERED (build recheck; editor rejection: PQ-1) |
| FR-10.7 | — | Editing Interface, Content Store | SEC-AUDIT-1 | — | BLOCKED (PQ-1, PQ-14) |
| FR-10.8 | REQ-CONTENT-030 | Site Build; Editing Interface (DF-9) | SEC-INPUT-3 | — | PARTIALLY COVERED (failing item and field in the build report; display: PQ-1) |
| FR-11.1 | — | Content Store (DF-3) | SEC-OUT-1, SEC-PRIV-4 | — | BLOCKED (PQ-15, PQ-9) |
| FR-11.2 | REQ-CONTENT-040 | Site Build | SEC-INPUT-4 | — | PARTIALLY COVERED (unpublished items never reach the output; import: PQ-15) |
| NFR-1 | — | Site Build or Static Hosting | SEC-INPUT-5 | Layout and Spacing | BLOCKED (PQ-5) |
| NFR-2 | REQ-NAV-010, REQ-NAV-020 | Site Build, Public Site (TB-1) | — | — | COVERED |
| NFR-3 | REQ-PRIV-010, REQ-HTTP-020 | Site Build, Public Site (TB-1) | SEC-PRIV-1, SEC-HTTP-2 | Typography (system fonts) | COVERED |
| NFR-4 | REQ-NAV-010, REQ-NAV-020, REQ-LEGAL-010, REQ-WORKS-010, REQ-WORKS-020, REQ-WORKS-030, REQ-TEXTS-010, REQ-CV-010, REQ-DOWNLOADS-010, REQ-CONTACT-010, REQ-INSTAGRAM-010, REQ-HTTP-010 | Site Build, Public Site (TB-1) | SEC-HTTP-1 | Layout and Spacing (Breakpoints) | COVERED (browser list PQ-21) |
| SEC-HTTP-1 | REQ-HTTP-010 | Static Hosting (TB-1) | SEC-HTTP-1 | N/A | COVERED |
| SEC-HTTP-2 | REQ-HTTP-020 | Static Hosting, Public Site (TB-1) | SEC-HTTP-2 | N/A | PARTIALLY COVERED (Instagram origins: PQ-7) |
| SEC-HTTP-3 | REQ-HTTP-030 | Static Hosting (TB-1) | SEC-HTTP-3 | N/A | COVERED |
| SEC-HTTP-4 | — | Editing Interface (TB-2) | SEC-HTTP-4 | N/A | BLOCKED (PQ-1) |
| SEC-HOST-1 | — | Domain, DNS, Static Hosting (TB-1) | SEC-HOST-1 | N/A | BLOCKED (PQ-12) |
| SEC-PRIV-1 | REQ-PRIV-010, REQ-INSTAGRAM-010 | Public Site, Site Build (TB-1) | SEC-PRIV-1 | N/A | COVERED |
| SEC-PRIV-2 | — | Instagram Feed Integration (TB-5) | SEC-PRIV-2 | N/A | BLOCKED (PQ-7) |
| SEC-PRIV-3 | — | Instagram Feed Integration, Site Build (TB-5) | SEC-PRIV-3 | N/A | BLOCKED (PQ-7) |
| SEC-PRIV-4 | — | Editing Interface, Site Build | SEC-PRIV-4 | N/A | BLOCKED (PQ-5, PQ-6; uploads PQ-1) |
| SEC-PRIV-5 | REQ-PRIV-010 | Public Site | SEC-PRIV-5 | N/A | COVERED (default: none, OQ-8) |
| SEC-OUT-1 | REQ-OUTPUT-010 | Site Build (TB-4) | SEC-OUT-1 | N/A | COVERED |
| SEC-OUT-2 | REQ-OUTPUT-030 | Site Build | SEC-OUT-2 | N/A | COVERED |
| SEC-OUT-3 | — | Instagram Feed Integration, Site Build (TB-5) | SEC-OUT-3 | N/A | BLOCKED (PQ-7) |
| SEC-OUT-4 | — | Editing Interface (TB-2) | SEC-OUT-4 | N/A | BLOCKED (PQ-1) |
| SEC-OUT-5 | — | Public Site client-side code | SEC-OUT-5 | N/A | BLOCKED (PQ-7; applies only with client-side code, PQ-27) |
| SEC-OUT-6 | — | Instagram Feed Integration, Public Site (TB-5) | SEC-OUT-6 | N/A | BLOCKED (PQ-7) |
| SEC-AUTHN-1 | — | Editing Interface (TB-2) | SEC-AUTHN-1 | N/A | BLOCKED (PQ-1, PQ-2) |
| SEC-AUTHN-2 | — | Editing Interface (TB-2) | SEC-AUTHN-2 | N/A | BLOCKED (PQ-1, PQ-2) |
| SEC-AUTHN-3 | — | Editing Interface (TB-2) | SEC-AUTHN-3 | N/A | BLOCKED (PQ-1, PQ-2) |
| SEC-AUTHZ-1 | — | Content Store, Editing Interface (TB-3) | SEC-AUTHZ-1 | N/A | BLOCKED (PQ-1, PQ-3) |
| SEC-AUTHZ-2 | — | Editing Interface, Content Store | SEC-AUTHZ-2 | N/A | BLOCKED (PQ-1, PQ-3) |
| SEC-AUTHZ-3 | — | Content Store, Editing Interface, Site Build (TB-3, TB-4) | SEC-AUTHZ-3 | N/A | BLOCKED (PQ-1, PQ-4) |
| SEC-AUTHZ-4 | — | Content Store (TB-3) | SEC-AUTHZ-4 | N/A | BLOCKED (PQ-4) |
| SEC-AUDIT-1 | — | Content Store, Editing Interface | SEC-AUDIT-1 | N/A | BLOCKED (PQ-1, PQ-14) |
| SEC-AUDIT-2 | REQ-PIPELINE-070 | Site Build, Static Hosting (TB-4) | SEC-AUDIT-2 | N/A | COVERED |
| SEC-AVAIL-1 | — | Content Store | SEC-AVAIL-1 | N/A | BLOCKED (PQ-4, PQ-14) |
| SEC-INPUT-1 | — | Editing Interface | SEC-INPUT-1 | N/A | BLOCKED (PQ-1, PQ-5) |
| SEC-INPUT-2 | — | Editing Interface | SEC-INPUT-2 | N/A | BLOCKED (PQ-1) |
| SEC-INPUT-3 | REQ-CONTENT-030, REQ-PIPELINE-010 | Site Build, Static Hosting (TB-4) | SEC-INPUT-3 | N/A | COVERED |
| SEC-INPUT-4 | REQ-CONTENT-040 | Site Build | SEC-INPUT-4 | N/A | COVERED |
| SEC-INPUT-5 | — | Editing Interface, Site Build | SEC-INPUT-5 | N/A | BLOCKED (PQ-5; uploads PQ-1) |
| SEC-INPUT-6 | REQ-OUTPUT-020 | Site Build (TB-4) | SEC-INPUT-6 | N/A | COVERED |
| SEC-SECRET-1 | REQ-PIPELINE-040 | Site Build, Static Hosting (TB-4) | SEC-SECRET-1 | N/A | COVERED |
| SEC-SECRET-2 | REQ-PIPELINE-050 | Site Build pipeline (TB-4) | SEC-SECRET-2 | N/A | COVERED |
| SEC-SECRET-3 | — | Instagram Feed Integration (TB-5) | SEC-SECRET-3 | N/A | BLOCKED (PQ-7) |
| SEC-CICD-1 | REQ-PIPELINE-010 | Site Build, Static Hosting (TB-4) | SEC-CICD-1 | N/A | COVERED |
| SEC-CICD-2 | REQ-PIPELINE-030 | Site Build pipeline (TB-4) | SEC-CICD-2 | N/A | COVERED |
| SEC-CICD-3 | REQ-PIPELINE-060 | Site Build pipeline, Static Hosting | SEC-CICD-3 | N/A | COVERED (previews off until PQ-2) |
| SEC-CICD-4 | — | Editing Interface, Site Build pipeline | SEC-CICD-4 | N/A | BLOCKED (PQ-1) |
| SEC-CICD-5 | REQ-PIPELINE-020 | Site Build pipeline, Static Hosting | SEC-CICD-5 | N/A | COVERED |
| DEP-1 to DEP-6, DEP-8 | All leaf issues (Constraints) | All components | DEP-1 to DEP-6, DEP-8 | N/A | COVERED (applied whenever a dependency is added) |
| DEP-7 | REQ-PIPELINE-030 | Site Build pipeline | DEP-7 | N/A | COVERED |

Totals: BLOCKED 42, COVERED 41, OUT OF SCOPE 1, PARTIALLY COVERED 8 (92 rows).

## Hierarchy

```text
REQ-EPIC-001 Build the bilingual nina-laaf.de website from the specification
├── REQ-CONTENT-000 Content model and build-time content rules
│   ├── REQ-CONTENT-010 Define the Work and Image content model with its required-field rules
│   ├── REQ-CONTENT-020 Define the Text, CV entry, Download and Page content models
│   ├── REQ-CONTENT-030 Fail the Site Build and publish nothing when content violates the content model
│   ├── REQ-CONTENT-040 Exclude unpublished content from the static output
├── REQ-OUTPUT-000 Output encoding and safe rendering
│   ├── REQ-OUTPUT-010 HTML-encode editor text and render rich text from a restricted format
│   ├── REQ-OUTPUT-020 Treat content fields as data and never evaluate generator syntax inside them
│   ├── REQ-OUTPUT-030 Allow only https: and mailto: links in content and open external links with noopener
├── REQ-NAV-000 Navigation and languages
│   ├── REQ-NAV-020 Generate every page in German and English with a language switch to the same page
│   ├── REQ-NAV-010 Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page
├── REQ-WORKS-000 Works
│   ├── REQ-WORKS-010 List all published works and show works in public space as a group
│   ├── REQ-WORKS-020 Show each work on its own page with title, year, materials, dimensions and optional edition, series and text
│   ├── REQ-WORKS-030 Show a work's images in Nina's order with alt text and photo credit
├── REQ-HTTP-000 Public site and HTTP boundary
│   ├── REQ-HTTP-010 Serve nina-laaf.de over HTTPS only, redirect HTTP and send HSTS
│   ├── REQ-HTTP-020 Send a Content Security Policy that allows only the site's own origin
│   ├── REQ-HTTP-030 Send nosniff and forbid framing by other origins
├── REQ-PIPELINE-000 Secrets and build/deploy pipeline
│   ├── REQ-PIPELINE-010 Deploy to production only from the Content Store main branch after a successful build, with a least-privilege deploy identity
│   ├── REQ-PIPELINE-020 Deploy only the Site Build's generated output
│   ├── REQ-PIPELINE-030 Pin CI steps and build tools to exact versions and verify downloaded tools
│   ├── REQ-PIPELINE-040 Keep deploy and editing-system credentials out of the repository, output, client code and logs
│   ├── REQ-PIPELINE-050 Make deploy and Instagram secrets available only to production builds of the main branch
│   ├── REQ-PIPELINE-060 Keep previews of unpublished content from being publicly reachable
│   ├── REQ-PIPELINE-070 Record what each production deploy was built from and restore the previous version without a content change
├── REQ-LEGAL-010 Publish the Impressum and Datenschutzerklärung pages and link them from every page
├── REQ-TEXTS-010 List published texts and give each its own page
├── REQ-CV-010 Show the CV page with its six sections
├── REQ-DOWNLOADS-010 Offer published portfolio PDFs for download, each labeled with its title and as a PDF
├── REQ-CONTACT-010 Show Nina's email address as a mailto link and link to galerie burster on the Contact page
├── REQ-INSTAGRAM-010 Link to Instagram account @ninalaaf without loading any Instagram content
├── REQ-PRIV-010 Request no third-party resource before consent and include no analytics
```

## Dependency Graph

Acyclic (checked by topological sort). Arrows point from an issue to the issues it depends on.

```text
REQ-CONTENT-010 → (none)
REQ-CONTENT-020 → REQ-CONTENT-010
REQ-CONTENT-030 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-CONTENT-040 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-OUTPUT-010 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-OUTPUT-020 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-OUTPUT-030 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-NAV-020 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-NAV-010 → REQ-NAV-020
REQ-LEGAL-010 → REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010
REQ-WORKS-010 → REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020
REQ-WORKS-020 → REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010
REQ-WORKS-030 → REQ-WORKS-020
REQ-TEXTS-010 → REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010
REQ-CV-010 → REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010
REQ-DOWNLOADS-010 → REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020
REQ-CONTACT-010 → REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-030
REQ-INSTAGRAM-010 → REQ-NAV-020, REQ-OUTPUT-030
REQ-PRIV-010 → REQ-NAV-020
REQ-PIPELINE-010 → REQ-CONTENT-030
REQ-PIPELINE-020 → REQ-PIPELINE-010
REQ-PIPELINE-030 → REQ-PIPELINE-010
REQ-PIPELINE-040 → REQ-PIPELINE-010
REQ-PIPELINE-050 → REQ-PIPELINE-010
REQ-PIPELINE-060 → REQ-PIPELINE-010
REQ-PIPELINE-070 → REQ-PIPELINE-010
REQ-HTTP-010 → REQ-PIPELINE-010
REQ-HTTP-020 → REQ-PIPELINE-010
REQ-HTTP-030 → REQ-PIPELINE-010
```

## Manifest

File names are `NNN-<ID>.md`, where `NNN` is the zero-based position in the Topological Creation Order (`000` is the epic, item 1).

Effort is engineer-days; LOC is changed human-authored lines, excluding generated files and lockfiles.

| # | ID | Title | Parent | Depends on | Effort | LOC | Model | Model rationale |
|---|---|---|---|---|---|---|---|---|
| 000 | REQ-EPIC-001 | Build the bilingual nina-laaf.de website from the specification | — | — | — | — | — | Parent issue, no implementation |
| 001 | REQ-CONTENT-000 | Content model and build-time content rules | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 002 | REQ-OUTPUT-000 | Output encoding and safe rendering | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 003 | REQ-NAV-000 | Navigation and languages | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 004 | REQ-WORKS-000 | Works | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 005 | REQ-HTTP-000 | Public site and HTTP boundary | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 006 | REQ-PIPELINE-000 | Secrets and build/deploy pipeline | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 007 | REQ-CONTENT-010 | Define the Work and Image content model with its required-field rules | REQ-CONTENT-000 | — | 1–1.5 days | 250–500 | Claude Opus 5.5 (`claude-opus-5-5`) | Sets the shared rule set and portable format every other issue reads; a modelling mistake here spreads to all page and validation issues. |
| 008 | REQ-CONTENT-020 | Define the Text, CV entry, Download and Page content models | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Follows the pattern REQ-CONTENT-010 sets; field lists come straight from FR-5 to FR-7. |
| 009 | REQ-CONTENT-030 | Fail the Site Build and publish nothing when content violates the content model | REQ-CONTENT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Wires the existing rule set into a fail-closed first build step; behavior is fully specified by SEC-INPUT-3. |
| 010 | REQ-CONTENT-040 | Exclude unpublished content from the static output | REQ-CONTENT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5–1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`) | Confidentiality control with a subtle failure mode: generators copy page resources and sitemaps on their own (SECURITY.md T-25, T-29). |
| 011 | REQ-OUTPUT-010 | HTML-encode editor text and render rich text from a restricted format | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`) | Security-critical sanitizing and escaping; needs careful use of a vetted sanitizer and a hostile test corpus. |
| 012 | REQ-OUTPUT-020 | Treat content fields as data and never evaluate generator syntax inside them | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`) | Template-injection prevention depends on generator internals and build-time secret exposure (T-18). |
| 013 | REQ-OUTPUT-030 | Allow only https: and mailto: links in content and open external links with noopener | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Small allow-list with a known bypass corpus; well specified by SEC-OUT-2. |
| 014 | REQ-NAV-020 | Generate every page in German and English with a language switch to the same page | REQ-NAV-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 1–2 days | 300–700 | Claude Opus 5.5 (`claude-opus-5-5`) | Defines the page pairing and URL structure every page issue builds on; the largest UI piece in the plan. |
| 015 | REQ-NAV-010 | Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page | REQ-NAV-000 | REQ-NAV-020 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine shared-layout work with fixed labels and DESIGN.md states. |
| 016 | REQ-LEGAL-010 | Publish the Impressum and Datenschutzerklärung pages and link them from every page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two Page renders plus footer links; wording is content. |
| 017 | REQ-WORKS-010 | List all published works and show works in public space as a group | REQ-WORKS-000 | REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | List and filtered view from flags already in the model; layout fixed by DESIGN.md. |
| 018 | REQ-WORKS-020 | Show each work on its own page with title, year, materials, dimensions and optional edition, series and text | REQ-WORKS-000 | REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Template rendering of defined fields with optional-field handling. |
| 019 | REQ-WORKS-030 | Show a work's images in Nina's order with alt text and photo credit | REQ-WORKS-000 | REQ-WORKS-020 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Ordered image rendering with alt text and credit labels; no image processing in scope. |
| 020 | REQ-TEXTS-010 | List published texts and give each its own page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine list and detail pages. |
| 021 | REQ-CV-010 | Show the CV page with its six sections | REQ-EPIC-001 | REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine grouping of entries into six fixed sections. |
| 022 | REQ-DOWNLOADS-010 | Offer published portfolio PDFs for download, each labeled with its title and as a PDF | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Routine link list with a text label. |
| 023 | REQ-CONTACT-010 | Show Nina's email address as a mailto link and link to galerie burster on the Contact page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Single page with two content-driven links. |
| 024 | REQ-INSTAGRAM-010 | Link to Instagram account @ninalaaf without loading any Instagram content | REQ-EPIC-001 | REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | A plain link plus a network check; no feed logic in scope. |
| 025 | REQ-PRIV-010 | Request no third-party resource before consent and include no analytics | REQ-EPIC-001 | REQ-NAV-020 | 1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Browser network-recording test over a known list of page types; the rule is unambiguous. |
| 026 | REQ-PIPELINE-010 | Deploy to production only from the Content Store main branch after a successful build, with a least-privilege deploy identity | REQ-PIPELINE-000 | REQ-CONTENT-030 | 1–2 days | 100–300 | Claude Opus 5.5 (`claude-opus-5-5`) | Least-privilege deploy identity, branch protection and fail-closed deploy gating across the CI platform and host. |
| 027 | REQ-PIPELINE-020 | Deploy only the Site Build's generated output | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Point the upload at the output directory and add a post-deploy path check. |
| 028 | REQ-PIPELINE-030 | Pin CI steps and build tools to exact versions and verify downloaded tools | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Mechanical pinning and checksum verification with a lint check. |
| 029 | REQ-PIPELINE-040 | Keep deploy and editing-system credentials out of the repository, output, client code and logs | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 30–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Platform secret store, masking and scanning configuration. |
| 030 | REQ-PIPELINE-050 | Make deploy and Instagram secrets available only to production builds of the main branch | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 20–80 | Claude Opus 5.5 (`claude-opus-5-5`) | Secret scoping against fork and branch triggers is easy to get subtly wrong on CI platforms (T-20). |
| 031 | REQ-PIPELINE-060 | Keep previews of unpublished content from being publicly reachable | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 10–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Turn previews off and verify; one configuration change. |
| 032 | REQ-PIPELINE-070 | Record what each production deploy was built from and restore the previous version without a content change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Deploy record and restore drill using platform features. |
| 033 | REQ-HTTP-010 | Serve nina-laaf.de over HTTPS only, redirect HTTP and send HSTS | REQ-HTTP-000 | REQ-PIPELINE-010 | 0.5 day | 20–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Host configuration and header checks fully specified by SEC-HTTP-1. |
| 034 | REQ-HTTP-020 | Send a Content Security Policy that allows only the site's own origin | REQ-HTTP-000 | REQ-PIPELINE-010 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`) | A strict CSP must be fitted to the generator's output (inline styles/scripts) without weakening it. |
| 035 | REQ-HTTP-030 | Send nosniff and forbid framing by other origins | REQ-HTTP-000 | REQ-PIPELINE-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`) | Two headers and a framing test. |

Total for the drafted leaves: 17–26.5 engineer-days.

## Topological Creation Order

Parents come before children and upstream issues before downstream ones. In CREATE mode each issue is created with `--parent` set to its parent's issue, then every `{{ISSUE_URL:<ID>}}` placeholder is replaced with the captured URL.

1. REQ-EPIC-001
2. REQ-CONTENT-000
3. REQ-OUTPUT-000
4. REQ-NAV-000
5. REQ-WORKS-000
6. REQ-HTTP-000
7. REQ-PIPELINE-000
8. REQ-CONTENT-010
9. REQ-CONTENT-020
10. REQ-CONTENT-030
11. REQ-CONTENT-040
12. REQ-OUTPUT-010
13. REQ-OUTPUT-020
14. REQ-OUTPUT-030
15. REQ-NAV-020
16. REQ-NAV-010
17. REQ-LEGAL-010
18. REQ-WORKS-010
19. REQ-WORKS-020
20. REQ-WORKS-030
21. REQ-TEXTS-010
22. REQ-CV-010
23. REQ-DOWNLOADS-010
24. REQ-CONTACT-010
25. REQ-INSTAGRAM-010
26. REQ-PRIV-010
27. REQ-PIPELINE-010
28. REQ-PIPELINE-020
29. REQ-PIPELINE-030
30. REQ-PIPELINE-040
31. REQ-PIPELINE-050
32. REQ-PIPELINE-060
33. REQ-PIPELINE-070
34. REQ-HTTP-010
35. REQ-HTTP-020
36. REQ-HTTP-030

## Proposed Commands (not run)

Pre-flight checks for CREATE mode: `gh auth status`; confirm the target repository is `dalini/nina-website`; confirm `gh issue create` supports `--body-file` and `--parent` in the installed version (otherwise link sub-issues through the GitHub API); confirm no existing issue reuses a planning ID for different scope.

```sh
gh issue create --title "[REQ-EPIC-001] Build the bilingual nina-laaf.de website from the specification" --body-file ".planning/github-issues/000-REQ-EPIC-001.md"
gh issue create --title "[REQ-CONTENT-000] Content model and build-time content rules" --body-file ".planning/github-issues/001-REQ-CONTENT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-OUTPUT-000] Output encoding and safe rendering" --body-file ".planning/github-issues/002-REQ-OUTPUT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NAV-000] Navigation and languages" --body-file ".planning/github-issues/003-REQ-NAV-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-WORKS-000] Works" --body-file ".planning/github-issues/004-REQ-WORKS-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HTTP-000] Public site and HTTP boundary" --body-file ".planning/github-issues/005-REQ-HTTP-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PIPELINE-000] Secrets and build/deploy pipeline" --body-file ".planning/github-issues/006-REQ-PIPELINE-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTENT-010] Define the Work and Image content model with its required-field rules" --body-file ".planning/github-issues/007-REQ-CONTENT-010.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-020] Define the Text, CV entry, Download and Page content models" --body-file ".planning/github-issues/008-REQ-CONTENT-020.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-030] Fail the Site Build and publish nothing when content violates the content model" --body-file ".planning/github-issues/009-REQ-CONTENT-030.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-040] Exclude unpublished content from the static output" --body-file ".planning/github-issues/010-REQ-CONTENT-040.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-OUTPUT-010] HTML-encode editor text and render rich text from a restricted format" --body-file ".planning/github-issues/011-REQ-OUTPUT-010.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-020] Treat content fields as data and never evaluate generator syntax inside them" --body-file ".planning/github-issues/012-REQ-OUTPUT-020.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-030] Allow only https: and mailto: links in content and open external links with noopener" --body-file ".planning/github-issues/013-REQ-OUTPUT-030.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-NAV-020] Generate every page in German and English with a language switch to the same page" --body-file ".planning/github-issues/014-REQ-NAV-020.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-010] Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page" --body-file ".planning/github-issues/015-REQ-NAV-010.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-LEGAL-010] Publish the Impressum and Datenschutzerklärung pages and link them from every page" --body-file ".planning/github-issues/016-REQ-LEGAL-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-WORKS-010] List all published works and show works in public space as a group" --body-file ".planning/github-issues/017-REQ-WORKS-010.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-020] Show each work on its own page with title, year, materials, dimensions and optional edition, series and text" --body-file ".planning/github-issues/018-REQ-WORKS-020.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-030] Show a work's images in Nina's order with alt text and photo credit" --body-file ".planning/github-issues/019-REQ-WORKS-030.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-TEXTS-010] List published texts and give each its own page" --body-file ".planning/github-issues/020-REQ-TEXTS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CV-010] Show the CV page with its six sections" --body-file ".planning/github-issues/021-REQ-CV-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-DOWNLOADS-010] Offer published portfolio PDFs for download, each labeled with its title and as a PDF" --body-file ".planning/github-issues/022-REQ-DOWNLOADS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTACT-010] Show Nina's email address as a mailto link and link to galerie burster on the Contact page" --body-file ".planning/github-issues/023-REQ-CONTACT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-INSTAGRAM-010] Link to Instagram account @ninalaaf without loading any Instagram content" --body-file ".planning/github-issues/024-REQ-INSTAGRAM-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-010] Request no third-party resource before consent and include no analytics" --body-file ".planning/github-issues/025-REQ-PRIV-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PIPELINE-010] Deploy to production only from the Content Store main branch after a successful build, with a least-privilege deploy identity" --body-file ".planning/github-issues/026-REQ-PIPELINE-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-020] Deploy only the Site Build's generated output" --body-file ".planning/github-issues/027-REQ-PIPELINE-020.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-030] Pin CI steps and build tools to exact versions and verify downloaded tools" --body-file ".planning/github-issues/028-REQ-PIPELINE-030.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-040] Keep deploy and editing-system credentials out of the repository, output, client code and logs" --body-file ".planning/github-issues/029-REQ-PIPELINE-040.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-050] Make deploy and Instagram secrets available only to production builds of the main branch" --body-file ".planning/github-issues/030-REQ-PIPELINE-050.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-060] Keep previews of unpublished content from being publicly reachable" --body-file ".planning/github-issues/031-REQ-PIPELINE-060.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-070] Record what each production deploy was built from and restore the previous version without a content change" --body-file ".planning/github-issues/032-REQ-PIPELINE-070.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-HTTP-010] Serve nina-laaf.de over HTTPS only, redirect HTTP and send HSTS" --body-file ".planning/github-issues/033-REQ-HTTP-010.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-020] Send a Content Security Policy that allows only the site's own origin" --body-file ".planning/github-issues/034-REQ-HTTP-020.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-030] Send nosniff and forbid framing by other origins" --body-file ".planning/github-issues/035-REQ-HTTP-030.md"  # parent: REQ-HTTP-000
```

## Before CREATE

1. Run step 10 (`prompts/10-resolve-open-questions.md`) to answer PQ-1 to PQ-15 and, where possible, PQ-16 to PQ-28 in the spec files.
2. Re-run step 9 in DRAFT_ONLY. Its answers change the specs, so the plan is re-decomposed and the blocked scope gets issues.
3. Run step 9 with `Execution mode: CREATE` once no blocking question remains.
