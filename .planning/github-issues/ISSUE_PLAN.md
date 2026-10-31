# ISSUE_PLAN — nina-laaf.de

Step 9 re-run (`prompts/09-github-epic.md`), 2026-10-31, after step 10 (answers in `.planning/OPEN_QUESTIONS.md`) and the step 8 re-run (SECURITY.md threat model v2). The specs changed after the first step 9 drafts, so the scope was decomposed again; the earlier drafts were replaced. Nothing on GitHub was changed.

## Run Inputs

- `Scope: ALL`
- `Execution mode: DRAFT_ONLY` — local files only; no mutating `gh` command was run.
- Sources read: `REQUIREMENTS.md`, `ARCHITECTURE.md`, `SECURITY.md`, `DESIGN.md`, `REQUIREMENT_TEMPLATE.md`; `CLAUDE.md` followed; `style-guide.html` cited for UI issues. `.planning/OPEN_QUESTIONS.md` read as the record of step 10 decisions. `docs/legacy-content/works/` read only to check the shape of the legacy works that FR-11.1 points to; it is not specification.
- Result: 90 draft issue bodies: 1 epic, 13 workstreams, 76 leaf issues.

## Planning Rules Applied

- **Blocking** means the answer changes observable behavior, roles or permissions, auth, an API contract, data semantics, a trust boundary, a security control, failure behavior, or what an acceptance test can assert (prompt definition). Scope that depends on a blocking question is listed under Blocked Scope and is **not** decomposed. Where only part of a requirement depends on one, the rest is decomposed and the issue names the excluded part in Out of Scope.
- **Non-blocking** questions are selections, values, wordings and orderings that leave every acceptance criterion pass/fail as written. They appear as `TO BE DECIDED` or as a PQ reference in the issues' Open Decisions. They still have to be answered before work on those issues starts, because an implementer must not make them alone.
- **Question numbering** continues after the first run's PQ-1 to PQ-28, which step 10 answered and `.planning/OPEN_QUESTIONS.md` cites; new questions start at PQ-29 so that no ID means two things.
- **IDs** of the first run are kept where the scope is the same (version 0.2.0); new issues start at 0.1.0. REQ-PIPELINE-050 (secrets limited to `main`) is not drafted again: it is blocked scope (PQ-31). REQ-PIPELINE-060 keeps its ID for the preview that step 10 decided (it was "keep previews off"). REQ-IMAGES-030 is left free for the PDF metadata check (PQ-39).
- **Standards Alignment**: SECURITY.md uses no ASVS identifiers until they are verified; every issue states the target (ASVS 5.0.0 Level 1) and marks NIST SP 800-53 and Zero Trust as not applicable (SECURITY.md). Only references SECURITY.md cites (`REF-*`) or that define the mechanism (RFC 6797, W3C CSP, WCAG 2.2) are listed. Threat references are STRIDE categories and T-IDs of SECURITY.md Threat Model v2, plus CWE identifiers where they match directly.
- **Models**: Anthropic's models overview (platform.claude.com/docs/models/overview) could not be reached from this session on 2026-10-31, so every model name is marked **NOT VERIFIED**: Claude Opus 5.5 `claude-opus-5-5` and Claude Sonnet 5.5 `claude-sonnet-5-5` (Claude Fable 5.1 `claude-fable-5-1` is not assigned). The names match the model identity of the drafting session and third-party listings. Verify them before the CREATE run.
- **Sveltia CMS capabilities** (entry history and restore, content-type and pixel checks at upload, a single featured work) could not be verified from this session; the affected issues say so and require a gap to be raised as a question rather than filled by the implementer (PQ-44).

## Open Questions

### Blocking

| ID | Question | Source and conflict | Affected IDs |
|---|---|---|---|
| PQ-29 | How is the session rule met for the Editing Interface: sign-out, 30-minute inactivity and 8-hour maximum, and clearing cached content? Sveltia CMS signs out only in the browser, has no inactivity timeout, and GitHub OAuth App tokens do not expire. | SECURITY.md SQ-16; SEC-AUTHN-3 (not enforceable as decided); T-2, T-47, T-48 | SEC-AUTHN-3; SEC-AUTHN-5 (token type); FR-10.5 |
| PQ-30 | Which scope may the Editing Interface credential have? The default `repo,user` reaches every repository of each editor and, for Ives, his admin rights. | SECURITY.md SQ-17; SEC-AUTHZ-3, SEC-AUTHN-4 (requested scopes); T-11, T-44 | SEC-AUTHZ-3 (credential part), SEC-AUTHN-4, SEC-AUTHN-5; REQ-STORE-030 (excluded part) |
| PQ-31 | Which account type and GitHub plan hold `dalini/nina-content` and `dalini/nina-site`? Branch protection or rulesets, required verified commits, and environments limited to `main` need a paid plan for private repositories. | SECURITY.md SQ-18; SEC-AUDIT-1, SEC-SECRET-1 (storage in the `production` environment), SEC-SECRET-2; T-20, T-52, T-53 | SEC-AUDIT-1; SEC-SECRET-1, SEC-SECRET-2 (storage and scoping); FR-10.7 (trustworthy attribution); every REQ-PIPELINE-* issue (Constraints) |
| PQ-32 | Must the publish status file be readable only by editors, or is a public file with only the SEC-CICD-9 fields acceptable? This also decides how the Editing Interface reads it and shows the banner. | SECURITY.md SQ-19; SEC-CICD-9, SEC-OUT-4 (status as text); T-59 | FR-10.8 (banner, not-live-after-15-minutes), SEC-CICD-9 (access), SEC-OUT-4 (status display); REQ-PIPELINE-120 (excluded part) |
| PQ-33 | How do processes that must not run as root (deploy account, OAuth helper, content check, token refresh, backup) read the secrets that SEC-SECRET-1 keeps in root-only files? | SECURITY.md SQ-20; SEC-SECRET-1, SEC-CICD-1, SEC-HOST-2; T-37, T-43, T-62 | VPS content check (FR-10.4 15-minute limit, SEC-CICD-4 VPS side and alert); Instagram token storage, monthly refresh and delivery (SEC-SECRET-3, SEC-CICD-1); Content Store backup (SEC-AVAIL-1); OAuth helper client secret (SEC-AUTHN-4) |
| PQ-34 | Which runtime runs the OAuth helper on the VPS? The Sveltia CMS Authenticator is written for Cloudflare Workers. | SECURITY.md SQ-21; ARCHITECTURE.md Editing Interface (TO BE DECIDED); SEC-AUTHN-4, SEC-CICD-2, SEC-HOST-2; T-43, T-46, T-61 | Editing Interface sign-in (SEC-AUTHN-1, SEC-AUTHN-4), helper code supply (SEC-CICD-2), helper process (SEC-HOST-2); FR-10.5 |
| PQ-35 | How does the statistics tool read access logs that SEC-PRIV-6 makes readable only by the maintainer, and write reports readable only by the maintainer, while SEC-HOST-2 requires it to run under an unprivileged account (SEC-AVAIL-1 names a separate statistics account)? How does Ives view the reports (SEC-PRIV-5: TO BE DECIDED)? | Conflict: SECURITY.md SEC-PRIV-5, SEC-PRIV-6 (maintainer-only) vs SEC-HOST-2 (unprivileged statistics process), SEC-AVAIL-1; T-63 | SEC-PRIV-5 (statistics part) |
| PQ-36 | Exhibition entries: FR-4.2 says each entry MUST show title, type, venue and city, but FR-10.6 requires only start and end dates. Are the four fields required? What does an entry whose end date is before its start date mean? What does the home page show when no published Exhibition entry exists at all (FR-2.3 assumes one)? The type also decides the CV section (FR-5.3). | Conflict: REQUIREMENTS.md FR-4.2 vs FR-10.6; gaps in FR-4.1, FR-2.3, FR-5.3 | FR-4.1, FR-4.2, FR-2.3, FR-5.3 (excluded parts); REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-020, REQ-HOME-020, REQ-CV-020, REQ-EDIT-030 (Out of Scope) |
| PQ-37 | Legacy works hold materials and dimensions in each image's caption (e.g. `faltung`: three images with different materials, dimensions and years), but a Work has one materials and one dimensions value. How are they mapped? | Conflict: REQUIREMENTS.md FR-11.1 (import materials and dimensions) vs FR-3.3 and the Work model; shape checked in `docs/legacy-content/works/` | FR-11.1 (materials and dimensions); REQ-MIGRATION-010 (Out of Scope) |
| PQ-38 | DESIGN.md Accessibility requires keyboard use of "image sequences and the image viewer" and no auto-advancing images, but no requirement or component defines an image viewer (ARCHITECTURE.md mentions one only as an example). Is there an image viewer on work pages, and what does it do? | Gap: DESIGN.md Accessibility; ARCHITECTURE.md Client framework ("e.g."); SECURITY.md SEC-OUT-5 | Image viewer scope; SEC-OUT-5 (the only client-side code it would add) |
| PQ-39 | Published PDFs with embedded metadata: clean them automatically or reject them? SEC-PRIV-4 allows either; the choice decides whether Nina's publish fails. | SECURITY.md SEC-PRIV-4 ("clean or reject"); T-25 | SEC-PRIV-4 (PDFs); FR-6.1 release gate (REQ-DOWNLOADS-010 Constraints) |

### Non-blocking

| ID | Question | Source | Affected |
|---|---|---|---|
| PQ-40 | Certificate source and the alert mechanism to Ives (shared by certificate renewal, token refresh and content check). | SECURITY.md SEC-HTTP-5 (TO BE DECIDED), SEC-SECRET-3, SEC-CICD-4 | REQ-HTTP-060 |
| PQ-41 | Schedule of VPS operating-system and package security updates. | SECURITY.md SEC-HOST-2 (TO BE DECIDED) | REQ-HOST-020 |
| PQ-42 | Values of the nginx per-IP request and connection limits, and of the failed-sign-in limit on the preview site. | ARCHITECTURE.md Static Hosting; SECURITY.md T-31, SEC-AUTHN-2 | REQ-HTTP-070, REQ-PREVIEW-010 |
| PQ-43 | Build job time limit. | SECURITY.md SEC-CICD-8 (TO BE DECIDED) | REQ-PIPELINE-100 |
| PQ-44 | Verify Sveltia CMS's support for entry history and restore (FR-10.7), checking the actual file type and pixel dimensions before upload (SEC-INPUT-1, SEC-INPUT-5) and a single featured work (FR-10.3); not verifiable from this session. A missing capability needs a decision before the issue starts. | ARCHITECTURE.md Editing Interface | REQ-EDIT-020, REQ-EDIT-050, REQ-EDIT-080, REQ-CONTENT-010 |
| PQ-45 | Test command (deferred by Ives to step 11; the first coding PR sets it). Test identifiers in the issues are placeholders for that runner. | CLAUDE.md Commands; `.planning/OPEN_QUESTIONS.md` Q-50 | Acceptance-Criteria Traceability of every leaf |
| PQ-46 | German (and English) wording of fixed interface labels: navigation, CV section headings, exhibition group headings and types, the "PDF" label. Only "Foto" / "Photo" is given. | DESIGN.md Components; REQUIREMENTS.md FR-1.1, FR-4.1, FR-4.2, FR-5.2 | REQ-NAV-010, REQ-NAV-020, REQ-WORKS-030, REQ-EXHIBITIONS-010, REQ-EXHIBITIONS-020, REQ-CV-010 |
| PQ-47 | Link target of an Exhibition entry from a work page: no source defines a page per entry. | REQUIREMENTS.md FR-3.9, FR-4 | REQ-WORKS-040 |
| PQ-48 | Generated wording of a derived CV line (which fields, in which order) and the year used for an entry spanning two years. | REQUIREMENTS.md FR-5.3, FR-5.4 | REQ-CV-020 |
| PQ-49 | Orders the sources leave open: works of the same year (FR-3.1), CV lines of the same year (FR-5.2), News items of the same date (FR-4.6), Downloads on the CV page (FR-6.1). | REQUIREMENTS.md | REQ-WORKS-010, REQ-CV-010, REQ-NEWS-010, REQ-DOWNLOADS-010 |
| PQ-50 | Source of the alt text of Instagram post images (FR-3.7 requires alt text for every image). | REQUIREMENTS.md FR-3.7, FR-8.2 | REQ-INSTAGRAM-020 |
| PQ-51 | Where operational runbooks and review records are kept (SEC-HOST-1 yearly review, restore drills, SEC-AUTHZ-2 checks). | SECURITY.md SEC-HOST-1, SEC-AUDIT-2 | REQ-HOST-010 |
| PQ-52 | DESIGN.md Inputs and form feedback names the editing interface, but the decided Editing Interface is Sveltia CMS with its own UI. Is restyling expected? | DESIGN.md Components; ARCHITECTURE.md Editing Interface | REQ-EDIT-020 (and the other REQ-EDIT form issues) |
| PQ-53 | Which identifier Nina uses to name an Instagram post to hide (its link or its ID). | REQUIREMENTS.md FR-8.6 | REQ-CONTENT-060, REQ-EDIT-070 |

## Blocked Scope

Not decomposed into issues; each row needs the listed answers (step 10) and a step 9 re-run.

| Capability | Questions | Requirements |
|---|---|---|
| Editing Interface sign-in through the OAuth helper on `edit.nina-laaf.de` (allowed domains, `state`, scopes, client secret, runtime, pinned helper code, helper process account) | PQ-34, PQ-30, PQ-33 | SEC-AUTHN-1 (Editing Interface), SEC-AUTHN-4, SEC-CICD-2 (helper), SEC-HOST-2 (helper), SEC-SECRET-1 (client secret); FR-10.5 |
| Editing session limits and clearing cached content on sign-out | PQ-29 | SEC-AUTHN-3 |
| Revoking all Editing Interface tokens of an editor, and the revocation drill | PQ-29, PQ-30, PQ-34 | SEC-AUTHN-5 |
| Narrowing the Editing Interface credential | PQ-30 | SEC-AUTHZ-3 (credential part) |
| History protection on `main` of `dalini/nina-content` and required verified commits | PQ-31 | SEC-AUDIT-1; FR-10.7 (trustworthy attribution) |
| `production` environment limited to `main`, holding the deploy, preview-deploy and content-read secrets | PQ-31 | SEC-SECRET-1 (GitHub storage), SEC-SECRET-2 |
| Publish status access and the banner in the Editing Interface, including "not live 15 minutes after saving" | PQ-32 (and PQ-33 for changes whose build never starts) | FR-10.8 (editor side), SEC-CICD-9 (access), SEC-OUT-4 (status as text) |
| VPS content check that starts builds within 15 minutes, its tokens and its alert | PQ-33 | FR-10.4 (15-minute limit), SEC-CICD-4 (VPS side), SEC-HOST-2 |
| Instagram token: root-only storage, monthly refresh with alert, delivery to the build | PQ-33 | SEC-SECRET-3 (refresh), SEC-SECRET-1 (token), SEC-CICD-1 (token read); FR-8.2 (live refresh) |
| Weekly Content Store backup to the VPS and the yearly restore test | PQ-33 | SEC-AVAIL-1 |
| Visitor statistics from the access logs | PQ-35 | SEC-PRIV-5 (statistics part) |
| Exhibition entries with empty title, type, venue or city, or end before start; home page without any entry; CV placement of entries without type | PQ-36 | FR-4.1, FR-4.2, FR-2.3, FR-5.3 (excluded parts) |
| Legacy materials and dimensions | PQ-37 | FR-11.1 (materials, dimensions) |
| Image viewer on work pages | PQ-38 | DESIGN.md Accessibility (image viewer) |
| PDF metadata check | PQ-39 | SEC-PRIV-4 (PDFs) |

**Release gate.** The epic is not releasable while any of these is open: the Editing Interface sign-in (Nina cannot edit), the 15-minute publish trigger (FR-10.4), the `production` environment and branch protection (SEC-SECRET-2, SEC-AUDIT-1), the publish status shown to Nina (FR-10.8), the Instagram token refresh, the backup (SEC-AVAIL-1), and the PDF metadata check (REQ-DOWNLOADS-010 would publish PDFs unchecked, SEC-PRIV-4, T-25). SECURITY.md also requires SQ-16 to SQ-21 (PQ-29 to PQ-34) to be resolved before the first release.

## Coverage

Every FR, NFR, SEC and DEP rule of the sources, plus the one architecture decision (T-31 limits) that an issue implements without a rule ID. Status is `COVERED`, `PARTIALLY COVERED`, `BLOCKED` or `OUT OF SCOPE`; the note names the open question for the rest.

| Requirement ID | Issue IDs | Boundary | Security rules | Design sections | Status |
|---|---|---|---|---|---|
| FR-1.1 | REQ-NAV-010 | Site Build, Public Site (TB-1) | — | Components (Links, Focus state) | COVERED |
| FR-1.2 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-1.3 | REQ-NAV-020 | Site Build, Public Site (TB-1) | SEC-OUT-5 (only with client code) | Components (Language switch) | COVERED |
| FR-1.4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-NAV-020, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Content Store, Site Build, Editing Interface | SEC-OUT-1 | — | COVERED |
| FR-1.5 | REQ-CONTENT-010, REQ-NAV-020, REQ-WORKS-020, REQ-EDIT-020 | Content Store, Site Build | — | Typography | COVERED |
| FR-1.6 | REQ-NAV-020 | Site Build, Public Site (TB-1) | — | Components (Language switch) | COVERED |
| FR-1.7 | REQ-NAV-030 | Site Build, Public Site (TB-1) | — | Accessibility (`lang`) | COVERED |
| FR-2.1 | REQ-HOME-010 | Site Build, Public Site (TB-1) | — | Layout and Spacing | COVERED |
| FR-2.2 | REQ-HOME-010 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-2.3 | REQ-HOME-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing | PARTIALLY COVERED (no entry at all: PQ-36) |
| FR-2.4 | REQ-INSTAGRAM-020 | Site Build, Public Site (TB-1) | SEC-PRIV-3 | Components (Instagram posts) | COVERED |
| FR-3.1 | REQ-CONTENT-010, REQ-WORKS-010, REQ-EDIT-020 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | Layout and Spacing (Grid) | COVERED (same-year order: PQ-49) |
| FR-3.2 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.3 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.4 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | — | Components (Work metadata) | COVERED |
| FR-3.5 | REQ-CONTENT-010, REQ-WORKS-020 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Components (Work metadata) | COVERED |
| FR-3.6 | REQ-CONTENT-010, REQ-WORKS-030, REQ-EDIT-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing (no cropping) | COVERED (image viewer: PQ-38) |
| FR-3.7 | REQ-CONTENT-010, REQ-WORKS-030, REQ-TEXTS-010, REQ-NEWS-010, REQ-INSTAGRAM-020, REQ-MIGRATION-030, REQ-EDIT-020 | Site Build, Editing Interface | — | Components (Image with credit), Accessibility | COVERED (Instagram alt source: PQ-50) |
| FR-3.8 | REQ-CONTENT-010, REQ-WORKS-010, REQ-EDIT-020 | Site Build, Public Site (TB-1) | — | Layout and Spacing (Grid) | COVERED |
| FR-3.9 | REQ-WORKS-040 | Site Build, Public Site (TB-1) | SEC-INPUT-4 | — | COVERED (link target: PQ-47) |
| FR-3.10 | REQ-IMAGES-010, REQ-WORKS-030 | Site Build (TB-4) | SEC-PRIV-4 | — | COVERED |
| FR-4.1 | REQ-CONTENT-050, REQ-EXHIBITIONS-020 | Site Build | — | Layout and Spacing | PARTIALLY COVERED (end before start: PQ-36) |
| FR-4.2 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EDIT-030 | Content Store, Site Build | — | Components | PARTIALLY COVERED (empty title, type, venue, city: PQ-36) |
| FR-4.3 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-OUTPUT-030 | Content Store, Site Build | SEC-OUT-2 | Components (Links) | COVERED |
| FR-4.4 | REQ-EXHIBITIONS-020 | Site Build | — | — | COVERED |
| FR-4.5 | REQ-CONTENT-050, REQ-EXHIBITIONS-010, REQ-EDIT-030 | Content Store, Site Build | SEC-INPUT-4 | — | COVERED |
| FR-4.6 | REQ-CONTENT-050, REQ-NEWS-010, REQ-EDIT-030 | Content Store, Site Build | SEC-OUT-1, SEC-OUT-2 | Components (Image with credit) | COVERED |
| FR-5.1 | REQ-CONTENT-020, REQ-TEXTS-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-1, SEC-INPUT-4 | Typography, Layout and Spacing | COVERED |
| FR-5.2 | REQ-CONTENT-020, REQ-CV-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-1 | Typography | COVERED (labels: PQ-46) |
| FR-5.3 | REQ-CV-020 | Site Build | SEC-INPUT-4 | — | PARTIALLY COVERED (entries without type: PQ-36) |
| FR-5.4 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CV-010, REQ-CV-020, REQ-CV-030, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Content Store, Site Build, Editing Interface | SEC-OUT-1 | — | COVERED (line wording: PQ-48) |
| FR-6.1 | REQ-CONTENT-020, REQ-DOWNLOADS-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-INPUT-4; SEC-PRIV-4 (PDFs: PQ-39) | Components (Links) | COVERED (release gate: PQ-39) |
| FR-6.2 | REQ-DOWNLOADS-010 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-7.1 | REQ-CONTENT-020, REQ-CONTACT-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-2 | Components (Links) | COVERED |
| FR-7.2 | REQ-CONTENT-020, REQ-CONTACT-010, REQ-EDIT-040 | Site Build, Public Site (TB-1) | SEC-OUT-2 | Components (Links) | COVERED |
| FR-7.3 | — | — | — | — | OUT OF SCOPE (not part of this release) |
| FR-7.4 | REQ-CONTACT-010 | Site Build, Public Site (TB-1) | — | — | COVERED |
| FR-8.1 | REQ-INSTAGRAM-010 | Site Build, Public Site (TB-1) | SEC-PRIV-1, SEC-OUT-2 | Components (Links) | COVERED |
| FR-8.2 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-PRIV-3, SEC-OUT-3 | Components (Instagram posts) | PARTIALLY COVERED (token storage, refresh, delivery: PQ-33) |
| FR-8.3 | REQ-PRIV-010, REQ-INSTAGRAM-030 | Public Site, Instagram Feed Integration | SEC-PRIV-1, SEC-PRIV-3 | — | COVERED |
| FR-8.4 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-040 | Site Build, Public Site | SEC-PRIV-1 | Components (Instagram posts) | COVERED |
| FR-8.5 | REQ-INSTAGRAM-030, REQ-INSTAGRAM-040 | Instagram Feed Integration, Site Build (TB-4, TB-5) | SEC-OUT-3, SEC-CICD-1 | — | COVERED |
| FR-8.6 | REQ-CONTENT-060, REQ-INSTAGRAM-020, REQ-EDIT-070 | Content Store, Editing Interface, Site Build | — | — | COVERED (post identifier: PQ-53) |
| FR-9.1 | REQ-LEGAL-010, REQ-CONTENT-020 | Site Build, Public Site (TB-1) | — | Components (Links) | COVERED |
| FR-10.1 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Editing Interface, Content Store (TB-2, TB-3) | SEC-AUTHZ-1 | Components (Inputs and form feedback) | COVERED (sign-in: FR-10.5) |
| FR-10.2 | REQ-EDIT-020, REQ-EDIT-040, REQ-EDIT-050 | Editing Interface (TB-2) | SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5 | Components (Inputs and form feedback) | COVERED |
| FR-10.3 | REQ-CONTENT-010, REQ-EDIT-020, REQ-EDIT-040 | Content Store, Editing Interface | — | — | COVERED (Sveltia check: PQ-44) |
| FR-10.4 | REQ-PIPELINE-010, REQ-PIPELINE-080 | Site Build → Static Hosting (TB-4) | SEC-CICD-1, SEC-CICD-4 | — | PARTIALLY COVERED (15-minute trigger: PQ-33) |
| FR-10.5 | REQ-STORE-010, REQ-STORE-030, REQ-PREVIEW-010 | Editing Interface, Content Store, preview site (TB-2, TB-3) | SEC-AUTHN-1, SEC-AUTHZ-1, SEC-AUTHZ-2 | — | PARTIALLY COVERED (Editing Interface sign-in, session, scope: PQ-29, PQ-30, PQ-34) |
| FR-10.6 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030, REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | Site Build (TB-4), Editing Interface | SEC-INPUT-3 | Components (Inputs and form feedback) | COVERED |
| FR-10.7 | REQ-EDIT-080 | Editing Interface, Content Store | SEC-AUDIT-1 | — | PARTIALLY COVERED (history protection, verified commits: PQ-31) |
| FR-10.8 | REQ-CONTENT-030, REQ-PIPELINE-110, REQ-PIPELINE-120 | Site Build, Editing Interface (DF-9) | SEC-CICD-9 | — | PARTIALLY COVERED (banner, not-live detection: PQ-32, PQ-33) |
| FR-10.9 | REQ-PIPELINE-060, REQ-PREVIEW-010 | Site Build, preview site (TB-2, TB-4) | SEC-CICD-3, SEC-AUTHN-1 | — | COVERED |
| FR-11.1 | REQ-MIGRATION-010 | Content Store (DF-3) | SEC-OUT-1, SEC-PRIV-4 | — | PARTIALLY COVERED (materials, dimensions: PQ-37) |
| FR-11.2 | REQ-MIGRATION-020, REQ-CONTENT-040 | Content Store, Site Build | SEC-INPUT-4 | — | COVERED |
| FR-11.3 | REQ-MIGRATION-030 | Content Store | — | — | COVERED |
| FR-11.4 | REQ-MIGRATION-020 | Content Store | — | — | COVERED |
| NFR-1 | REQ-IMAGES-010, REQ-PERF-010 | Site Build, Static Hosting | SEC-INPUT-5 | Layout and Spacing | COVERED |
| NFR-2 | REQ-NAV-010, REQ-NAV-020 | Site Build, Public Site (TB-1) | — | — | COVERED |
| NFR-3 | REQ-PRIV-010, REQ-HTTP-020 | Public Site, Static Hosting (TB-1) | SEC-PRIV-1, SEC-HTTP-2 | Typography (system fonts) | COVERED |
| NFR-4 | REQ-LAYOUT-010 and every Public Site leaf (Required Test Environment), REQ-HTTP-010 | Site Build, Public Site (TB-1) | SEC-HTTP-1 | Layout and Spacing (Breakpoints) | COVERED |
| SEC-HTTP-1 | REQ-HTTP-010 | Static Hosting (TB-1) | SEC-HTTP-1 | N/A | COVERED |
| SEC-HTTP-2 | REQ-HTTP-020 | Static Hosting, Public Site (TB-1) | SEC-HTTP-2 | N/A | COVERED |
| SEC-HTTP-3 | REQ-HTTP-030 | Static Hosting (TB-1) | SEC-HTTP-3 | N/A | COVERED |
| SEC-HTTP-4 | REQ-HTTP-040, REQ-HTTP-050 | Editing Interface, preview site (TB-2) | SEC-HTTP-4 | N/A | COVERED |
| SEC-HTTP-5 | REQ-HTTP-060 | Static Hosting (TB-1, TB-2) | SEC-HTTP-5 | N/A | COVERED (source, alert: PQ-40) |
| SEC-HOST-1 | REQ-HOST-010 | Domain, DNS, Static Hosting (TB-1) | SEC-HOST-1 | N/A | COVERED |
| SEC-HOST-2 | REQ-HOST-020, REQ-PIPELINE-010 | Static Hosting (VPS) | SEC-HOST-2 | N/A | PARTIALLY COVERED (secret reading, helper, statistics: PQ-33, PQ-34, PQ-35) |
| SEC-PRIV-1 | REQ-PRIV-010, REQ-LAYOUT-010, REQ-INSTAGRAM-010 | Public Site, Site Build (TB-1) | SEC-PRIV-1 | Typography | COVERED |
| SEC-PRIV-2 | — | — | — | N/A | OUT OF SCOPE (withdrawn in step 10) |
| SEC-PRIV-3 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-PRIV-3 | N/A | COVERED |
| SEC-PRIV-4 | REQ-IMAGES-010, REQ-IMAGES-020 | Site Build | SEC-PRIV-4 | N/A | PARTIALLY COVERED (PDFs: PQ-39) |
| SEC-PRIV-5 | REQ-PRIV-010 | Public Site, Static Hosting | SEC-PRIV-5 | N/A | PARTIALLY COVERED (statistics: PQ-35) |
| SEC-PRIV-6 | REQ-PRIV-020 | Static Hosting (TB-1) | SEC-PRIV-6 | N/A | COVERED |
| SEC-OUT-1 | REQ-OUTPUT-010, REQ-EDIT-060 | Site Build, Editing Interface | SEC-OUT-1 | N/A | COVERED |
| SEC-OUT-2 | REQ-OUTPUT-030 | Site Build | SEC-OUT-2 | N/A | COVERED |
| SEC-OUT-3 | REQ-INSTAGRAM-020, REQ-INSTAGRAM-030 | Instagram Feed Integration, Site Build (TB-5) | SEC-OUT-3 | N/A | COVERED |
| SEC-OUT-4 | REQ-EDIT-060 | Editing Interface (TB-2) | SEC-OUT-4 | N/A | PARTIALLY COVERED (status file display: PQ-32) |
| SEC-OUT-5 | Constraint of every Public Site leaf | Public Site client-side code | SEC-OUT-5 | N/A | COVERED (no leaf adds client code; image viewer: PQ-38) |
| SEC-OUT-6 | — | — | — | N/A | OUT OF SCOPE (withdrawn in step 10) |
| SEC-AUTHN-1 | REQ-PREVIEW-010 | Editing Interface, preview site (TB-2) | SEC-AUTHN-1 | N/A | PARTIALLY COVERED (Editing Interface sign-in: PQ-34) |
| SEC-AUTHN-2 | REQ-STORE-010, REQ-PREVIEW-010 | Editing Interface, preview site (TB-2) | SEC-AUTHN-2 | N/A | COVERED |
| SEC-AUTHN-3 | — | Editing Interface (TB-2) | SEC-AUTHN-3 | N/A | BLOCKED (PQ-29) |
| SEC-AUTHN-4 | — | OAuth helper (TB-2, TB-3) | SEC-AUTHN-4 | N/A | BLOCKED (PQ-34, PQ-30, PQ-33) |
| SEC-AUTHN-5 | — | Editing Interface, Content Store (TB-3) | SEC-AUTHN-5 | N/A | BLOCKED (PQ-29, PQ-30, PQ-34) |
| SEC-AUTHZ-1 | REQ-STORE-010 | Content Store (TB-3) | SEC-AUTHZ-1 | N/A | COVERED |
| SEC-AUTHZ-2 | REQ-STORE-010 | Content Store | SEC-AUTHZ-2 | N/A | COVERED |
| SEC-AUTHZ-3 | REQ-STORE-030 | Content Store, Editing Interface, Site Build (TB-3, TB-4) | SEC-AUTHZ-3 | N/A | PARTIALLY COVERED (credential scope: PQ-30) |
| SEC-AUTHZ-4 | REQ-STORE-010 | Content Store (TB-3) | SEC-AUTHZ-4 | N/A | COVERED |
| SEC-AUDIT-1 | — | Content Store, Editing Interface | SEC-AUDIT-1 | N/A | BLOCKED (PQ-31; history view in REQ-EDIT-080) |
| SEC-AUDIT-2 | REQ-PIPELINE-070 | Site Build, Static Hosting (TB-4) | SEC-AUDIT-2 | N/A | COVERED |
| SEC-AVAIL-1 | — | Content Store, Static Hosting | SEC-AVAIL-1 | N/A | BLOCKED (PQ-33) |
| SEC-INPUT-1 | REQ-EDIT-050, REQ-IMAGES-050, REQ-INSTAGRAM-030 | Editing Interface, Site Build | SEC-INPUT-1 | N/A | COVERED |
| SEC-INPUT-2 | REQ-EDIT-050, REQ-IMAGES-050 | Editing Interface, Site Build | SEC-INPUT-2 | N/A | COVERED |
| SEC-INPUT-3 | REQ-CONTENT-030, REQ-PIPELINE-010 | Site Build, Static Hosting (TB-4) | SEC-INPUT-3 | N/A | COVERED |
| SEC-INPUT-4 | REQ-CONTENT-040, REQ-PIPELINE-060 | Site Build | SEC-INPUT-4 | N/A | COVERED |
| SEC-INPUT-5 | REQ-EDIT-050, REQ-IMAGES-040, REQ-INSTAGRAM-030 | Editing Interface, Site Build | SEC-INPUT-5 | N/A | COVERED |
| SEC-INPUT-6 | REQ-OUTPUT-020 | Site Build (TB-4) | SEC-INPUT-6 | N/A | COVERED |
| SEC-INPUT-7 | REQ-CONTENT-070 | Site Build (TB-4) | SEC-INPUT-7 | N/A | COVERED |
| SEC-SECRET-1 | REQ-PIPELINE-040 | Site Build, Static Hosting (TB-4) | SEC-SECRET-1 | N/A | PARTIALLY COVERED (storage: PQ-31, PQ-33) |
| SEC-SECRET-2 | REQ-PIPELINE-060, REQ-PIPELINE-090 | Site Build pipeline (TB-4) | SEC-SECRET-2 | N/A | PARTIALLY COVERED (secrets limited to `main`: PQ-31) |
| SEC-SECRET-3 | REQ-INSTAGRAM-030 | Instagram Feed Integration (TB-5), Static Hosting | SEC-SECRET-3 | N/A | PARTIALLY COVERED (monthly refresh, alert: PQ-33) |
| SEC-CICD-1 | REQ-PIPELINE-010, REQ-INSTAGRAM-040 | Site Build, Static Hosting (TB-4) | SEC-CICD-1 | N/A | PARTIALLY COVERED (Instagram token read: PQ-33) |
| SEC-CICD-2 | REQ-PIPELINE-030, REQ-EDIT-010 | Site Build pipeline, Editing Interface | SEC-CICD-2 | N/A | PARTIALLY COVERED (OAuth helper code: PQ-34) |
| SEC-CICD-3 | REQ-PIPELINE-060, REQ-PREVIEW-010 | Site Build pipeline, Static Hosting | SEC-CICD-3 | N/A | COVERED |
| SEC-CICD-4 | REQ-PIPELINE-080 | Site Build pipeline, Static Hosting (VPS) | SEC-CICD-4 | N/A | PARTIALLY COVERED (VPS content check: PQ-33) |
| SEC-CICD-5 | REQ-PIPELINE-020 | Site Build pipeline, Static Hosting | SEC-CICD-5 | N/A | COVERED |
| SEC-CICD-6 | REQ-PIPELINE-090, REQ-INSTAGRAM-030 | Site Build pipeline (TB-4) | SEC-CICD-6 | N/A | COVERED |
| SEC-CICD-7 | REQ-STORE-020 | Content Store (TB-3) | SEC-CICD-7 | N/A | COVERED |
| SEC-CICD-8 | REQ-PIPELINE-100 | Site Build pipeline, GitHub account | SEC-CICD-8 | N/A | COVERED (time limit: PQ-43) |
| SEC-CICD-9 | REQ-PIPELINE-120 | Site Build, Editing Interface (DF-9) | SEC-CICD-9 | N/A | PARTIALLY COVERED (access: PQ-32) |
| T-31 (ARCHITECTURE.md Static Hosting: per-IP limits) | REQ-HTTP-070 | Static Hosting (TB-1, TB-2) | — | N/A | COVERED (limit values: PQ-42) |
| DEP-1 to DEP-6, DEP-8 | All leaf issues (Constraints) | All components | DEP-1 to DEP-6, DEP-8 | N/A | COVERED (applied whenever a dependency is added) |
| DEP-7 | REQ-PIPELINE-030, REQ-EDIT-010 | Site Build pipeline, Editing Interface | DEP-7 | N/A | COVERED |

Totals: COVERED 83, PARTIALLY COVERED 23, BLOCKED 5, OUT OF SCOPE 3 (114 rows).

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
├── REQ-OUTPUT-000 Output encoding and safe rendering
│   ├── REQ-OUTPUT-010 HTML-encode editor text and render rich text only from the restricted format
│   ├── REQ-OUTPUT-020 Treat content fields as data and never evaluate generator syntax inside them
│   ├── REQ-OUTPUT-030 Allow only https: and mailto: links in content and open external links with noopener
├── REQ-NAV-000 Navigation and languages
│   ├── REQ-NAV-020 Generate every page in English at / and German under /de/ with a language switch to the same page
│   ├── REQ-NAV-030 Show the other language's value, marked with its lang attribute, when a field's value is empty
│   ├── REQ-NAV-010 Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page
├── REQ-WORKS-000 Works
│   ├── REQ-WORKS-010 List all published works newest year first or in Nina's order, and show works in public space as a group
│   ├── REQ-WORKS-020 Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text
│   ├── REQ-WORKS-030 Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking
│   ├── REQ-WORKS-040 Link each work page to the published Exhibition entries linked to that work
├── REQ-IMAGES-000 Image processing in the Site Build
│   ├── REQ-IMAGES-010 Publish images only as responsive sizes of at most 2400 px on the long side, never as originals
│   ├── REQ-IMAGES-020 Remove embedded metadata from published images except the copyright notice and the creator
│   ├── REQ-IMAGES-040 Reject images larger than 40 megapixels before decoding them fully
│   ├── REQ-IMAGES-050 Recheck the type and size of every file the Site Build publishes and reject SVGs with active content
├── REQ-EXHIBITIONS-000 Exhibitions and News
│   ├── REQ-EXHIBITIONS-010 Show each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors, external link and linked works
│   ├── REQ-EXHIBITIONS-020 Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive
│   ├── REQ-NEWS-010 List News items in their own section of the Exhibitions/News page, newest date first
├── REQ-CV-000 CV
│   ├── REQ-CV-010 Show the CV page with its six sections in order, hide empty sections and list entries newest year first
│   ├── REQ-CV-020 Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works
│   ├── REQ-CV-030 Let Nina hide a derived CV line or replace its generated wording
├── REQ-INSTAGRAM-000 Instagram
│   ├── REQ-INSTAGRAM-010 Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram
│   ├── REQ-INSTAGRAM-020 Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link
│   ├── REQ-INSTAGRAM-030 Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks
│   ├── REQ-INSTAGRAM-040 Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails
├── REQ-HTTP-000 Public site and HTTP boundary
│   ├── REQ-HTTP-010 Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS
│   ├── REQ-HTTP-020 Send the self-only Content Security Policy on every public response
│   ├── REQ-HTTP-030 Send nosniff and forbid framing of nina-laaf.de by other origins
│   ├── REQ-HTTP-040 Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP on the preview site
│   ├── REQ-HTTP-050 Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub
│   ├── REQ-HTTP-060 Renew the TLS certificates of all three hosts automatically and alert Ives when a renewal fails
│   ├── REQ-HTTP-070 Limit requests and connections per IP address on all three hosts
├── REQ-STORE-000 Content Store and code repository access
│   ├── REQ-STORE-010 Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record
│   ├── REQ-STORE-020 Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there
│   ├── REQ-STORE-030 Limit write access to dalini/nina-site to the maintainer
├── REQ-PIPELINE-000 Secrets and build/deploy pipeline
│   ├── REQ-PIPELINE-010 Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key
│   ├── REQ-PIPELINE-020 Deploy only the Site Build's generated output
│   ├── REQ-PIPELINE-030 Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile
│   ├── REQ-PIPELINE-040 Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change
│   ├── REQ-PIPELINE-060 Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key
│   ├── REQ-PREVIEW-010 Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins
│   ├── REQ-PIPELINE-070 Record what each production deploy was built from and restore the previous version without a content change
│   ├── REQ-PIPELINE-080 Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group
│   ├── REQ-PIPELINE-090 Process Content Store files without access to the deploy keys or the Instagram token
│   ├── REQ-PIPELINE-100 Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives
│   ├── REQ-PIPELINE-110 Email Ives when a build fails
│   ├── REQ-PIPELINE-120 Write the publish status file with only the content version, the result and the failing item and field
├── REQ-EDIT-000 Editing Interface (Sveltia CMS)
│   ├── REQ-EDIT-010 Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN
│   ├── REQ-EDIT-020 Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings
│   ├── REQ-EDIT-030 Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks
│   ├── REQ-EDIT-040 Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order
│   ├── REQ-EDIT-050 Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload
│   ├── REQ-EDIT-060 Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup
│   ├── REQ-EDIT-070 Let Nina hide a single Instagram post and switch the feed off in the Editing Interface
│   ├── REQ-EDIT-080 Show who changed each content item and when, and restore an earlier version
├── REQ-MIGRATION-000 Legacy migration
│   ├── REQ-MIGRATION-010 Import the 53 legacy works with their German title, year and images into the Content Store
│   ├── REQ-MIGRATION-020 Import legacy works without a year as unpublished, publish the rest, and feature the newest work
│   ├── REQ-MIGRATION-030 Give every migrated image interim alt text made of the work title and the view number
├── REQ-LAYOUT-010 Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon
├── REQ-HOME-010 Show the artist's name and the featured work's image, linked to its work page, on the home page
├── REQ-HOME-020 Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page
├── REQ-PERF-010 Meet Core Web Vitals "good" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run
├── REQ-TEXTS-010 List published Texts in Nina's order and give each its own page
├── REQ-DOWNLOADS-010 Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF
├── REQ-CONTACT-010 Show Nina's email address as a mailto link and link to galerie burster on the Contact page
├── REQ-LEGAL-010 Publish the Impressum and Datenschutzerklärung pages and link them from every page
├── REQ-PRIV-010 Request no third-party resource and include no analytics, tracking, cookies or pixels in any page
├── REQ-PRIV-020 Delete nginx access logs after 14 days and keep them readable only by the maintainer
├── REQ-HOST-010 Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly
├── REQ-HOST-020 Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root
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
REQ-OUTPUT-010 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050
REQ-OUTPUT-020 → REQ-CONTENT-010, REQ-CONTENT-020
REQ-OUTPUT-030 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030
REQ-LAYOUT-010 → (none)
REQ-NAV-020 → REQ-CONTENT-010, REQ-CONTENT-020, REQ-LAYOUT-010
REQ-NAV-030 → REQ-NAV-020, REQ-OUTPUT-010
REQ-NAV-010 → REQ-NAV-020
REQ-WORKS-020 → REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010
REQ-IMAGES-010 → REQ-CONTENT-010
REQ-HOME-010 → REQ-CONTENT-010, REQ-CONTENT-030, REQ-NAV-020, REQ-IMAGES-010
REQ-WORKS-010 → REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020, REQ-IMAGES-010
REQ-WORKS-030 → REQ-WORKS-020, REQ-IMAGES-010
REQ-IMAGES-020 → REQ-IMAGES-010
REQ-IMAGES-040 → REQ-IMAGES-010, REQ-CONTENT-030
REQ-IMAGES-050 → REQ-CONTENT-030
REQ-EXHIBITIONS-010 → REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-WORKS-020
REQ-WORKS-040 → REQ-WORKS-020, REQ-EXHIBITIONS-010
REQ-EXHIBITIONS-020 → REQ-EXHIBITIONS-010, REQ-CONTENT-040
REQ-HOME-020 → REQ-HOME-010, REQ-EXHIBITIONS-020
REQ-NEWS-010 → REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010
REQ-CV-010 → REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010
REQ-CV-020 → REQ-CV-010, REQ-EXHIBITIONS-020, REQ-CONTENT-040
REQ-CV-030 → REQ-CV-020
REQ-TEXTS-010 → REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010, REQ-IMAGES-010
REQ-DOWNLOADS-010 → REQ-CV-010, REQ-CONTENT-040, REQ-IMAGES-050
REQ-CONTACT-010 → REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-030
REQ-LEGAL-010 → REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010
REQ-INSTAGRAM-010 → REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030
REQ-INSTAGRAM-020 → REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010
REQ-PERF-010 → REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010
REQ-PRIV-010 → REQ-LAYOUT-010, REQ-NAV-020
REQ-HOST-010 → (none)
REQ-HOST-020 → (none)
REQ-PRIV-020 → REQ-HOST-020
REQ-HTTP-010 → REQ-HOST-020
REQ-HTTP-020 → REQ-HTTP-010, REQ-NAV-020
REQ-HTTP-030 → REQ-HTTP-010
REQ-HTTP-040 → REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030
REQ-HTTP-060 → REQ-HTTP-010, REQ-HTTP-040
REQ-HTTP-070 → REQ-HTTP-010, REQ-HTTP-040
REQ-STORE-010 → (none)
REQ-STORE-020 → (none)
REQ-STORE-030 → (none)
REQ-PIPELINE-010 → REQ-CONTENT-030, REQ-HOST-020, REQ-STORE-010
REQ-PIPELINE-020 → REQ-PIPELINE-010
REQ-PIPELINE-030 → REQ-PIPELINE-010
REQ-PIPELINE-040 → REQ-PIPELINE-010
REQ-PIPELINE-060 → REQ-PIPELINE-010, REQ-CONTENT-040
REQ-PREVIEW-010 → REQ-HTTP-040, REQ-PIPELINE-060
REQ-PIPELINE-070 → REQ-PIPELINE-010
REQ-PIPELINE-080 → REQ-PIPELINE-010
REQ-PIPELINE-090 → REQ-PIPELINE-010
REQ-INSTAGRAM-030 → REQ-INSTAGRAM-020, REQ-IMAGES-020, REQ-IMAGES-040, REQ-PIPELINE-090
REQ-INSTAGRAM-040 → REQ-INSTAGRAM-030, REQ-PIPELINE-010
REQ-PIPELINE-100 → REQ-PIPELINE-010
REQ-PIPELINE-110 → REQ-PIPELINE-010
REQ-PIPELINE-120 → REQ-PIPELINE-010, REQ-CONTENT-030
REQ-EDIT-010 → REQ-HTTP-040, REQ-STORE-030
REQ-HTTP-050 → REQ-HTTP-040, REQ-EDIT-010
REQ-EDIT-020 → REQ-EDIT-010, REQ-CONTENT-010
REQ-EDIT-030 → REQ-EDIT-010, REQ-CONTENT-050
REQ-EDIT-040 → REQ-EDIT-010, REQ-CONTENT-020
REQ-EDIT-050 → REQ-EDIT-020, REQ-EDIT-040
REQ-EDIT-060 → REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
REQ-EDIT-070 → REQ-EDIT-010, REQ-CONTENT-060
REQ-EDIT-080 → REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
REQ-MIGRATION-010 → REQ-CONTENT-010
REQ-MIGRATION-020 → REQ-MIGRATION-010, REQ-CONTENT-040
REQ-MIGRATION-030 → REQ-MIGRATION-010
```

## Manifest

File names are `NNN-<ID>.md`, where `NNN` is the zero-based position in the Topological Creation Order (`000` is the epic). Effort is engineer-days; LOC is changed human-authored lines, excluding generated files and lockfiles. Recommended models: Claude Opus 5.5 for 23 leaves, Claude Sonnet 5.5 for 53; none for Claude Fable (no leaf is long-horizon or cross-boundary) or OpenAI GPT (the project uses no OpenAI tooling).

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
| 009 | REQ-HTTP-000 | Public site and HTTP boundary | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 010 | REQ-STORE-000 | Content Store and code repository access | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 011 | REQ-PIPELINE-000 | Secrets and build/deploy pipeline | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 012 | REQ-EDIT-000 | Editing Interface (Sveltia CMS) | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 013 | REQ-MIGRATION-000 | Legacy migration | REQ-EPIC-001 | — | — | — | — | Parent issue, no implementation |
| 014 | REQ-CONTENT-010 | Define the Work and Image content model with its required-field rules | REQ-CONTENT-000 | — | 1–1.5 days | 250–500 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | It sets the shared rule set and portable format that every page, validation and editing issue reads; a modelling mistake here spreads everywhere. |
| 015 | REQ-CONTENT-020 | Define the Text, CV entry, Download and Page content models | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | It follows the pattern REQ-CONTENT-010 sets; fields and rules come straight from FR-5 to FR-7 and FR-10.6. |
| 016 | REQ-CONTENT-050 | Define the Exhibition entry and News item content models | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A field list taken directly from FR-4 and FR-10.6, following the REQ-CONTENT-010 pattern. |
| 017 | REQ-CONTENT-060 | Define the Instagram settings: hidden posts and the feed switch | REQ-CONTENT-000 | REQ-CONTENT-010 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A small, fully specified settings item. |
| 018 | REQ-CONTENT-030 | Fail the Site Build and publish nothing when content violates the content model | REQ-CONTENT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-060 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | It wires the existing rule set into a fail-closed first stage; SEC-INPUT-3 fully specifies the behavior. |
| 019 | REQ-CONTENT-040 | Exclude unpublished content from the production output | REQ-CONTENT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050 | 0.5–1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | A confidentiality control with subtle failure modes: generators copy resources and build sitemaps and feeds on their own (T-25, T-29). |
| 020 | REQ-CONTENT-070 | Fail the Site Build when the Content Store checkout contains a symbolic link or special file | REQ-CONTENT-000 | REQ-CONTENT-030 | 0.5 day | 60–150 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Trust-boundary control on the build machine; link handling in checkouts has edge cases (submodules, links to links) that need care. |
| 021 | REQ-OUTPUT-010 | HTML-encode editor text and render rich text only from the restricted format | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Security-critical encoding and sanitizing that needs careful use of the renderer and a hostile test corpus. |
| 022 | REQ-OUTPUT-020 | Treat content fields as data and never evaluate generator syntax inside them | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Template-injection prevention depends on generator internals and how content is loaded (T-18). |
| 023 | REQ-OUTPUT-030 | Allow only https: and mailto: links in content and open external links with noopener | REQ-OUTPUT-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A small allow-list with a known bypass corpus, fully specified by SEC-OUT-2. |
| 024 | REQ-LAYOUT-010 | Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon | REQ-EPIC-001 | — | 1–1.5 days | 300–600 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Translating a complete token table and component list into one stylesheet and base template is well specified. |
| 025 | REQ-NAV-020 | Generate every page in English at / and German under /de/ with a language switch to the same page | REQ-NAV-000 | REQ-CONTENT-010, REQ-CONTENT-020, REQ-LAYOUT-010 | 1–2 days | 300–700 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | It defines the page pairing and URL structure every page issue builds on, the largest structural piece of the public site. |
| 026 | REQ-NAV-030 | Show the other language's value, marked with its lang attribute, when a field's value is empty | REQ-NAV-000 | REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | One shared helper applied consistently; FR-1.7 defines the behavior exactly. |
| 027 | REQ-NAV-010 | Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page | REQ-NAV-000 | REQ-NAV-020 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Routine shared-layout work with fixed targets and DESIGN.md states. |
| 028 | REQ-WORKS-020 | Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text | REQ-WORKS-000 | REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010 | 0.5–1 day | 150–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Template rendering of defined fields with optional-field handling. |
| 029 | REQ-IMAGES-010 | Publish images only as responsive sizes of at most 2400 px on the long side, never as originals | REQ-IMAGES-000 | REQ-CONTENT-010 | 1–2 days | 200–500 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Generator resource handling can leak originals silently (T-25); getting sizes, caps and output hygiene right needs care. |
| 030 | REQ-HOME-010 | Show the artist's name and the featured work's image, linked to its work page, on the home page | REQ-EPIC-001 | REQ-CONTENT-010, REQ-CONTENT-030, REQ-NAV-020, REQ-IMAGES-010 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A single template section driven by one content reference. |
| 031 | REQ-WORKS-010 | List all published works newest year first or in Nina's order, and show works in public space as a group | REQ-WORKS-000 | REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020, REQ-IMAGES-010 | 0.5–1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | List and filtered view from fields already in the model; layout fixed by DESIGN.md. |
| 032 | REQ-WORKS-030 | Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking | REQ-WORKS-000 | REQ-WORKS-020, REQ-IMAGES-010 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Ordered image rendering with labels; the image processing itself is a separate issue. |
| 033 | REQ-IMAGES-020 | Remove embedded metadata from published images except the copyright notice and the creator | REQ-IMAGES-000 | REQ-IMAGES-010 | 0.5–1 day | 100–250 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Privacy control with format-specific metadata blocks (EXIF, XMP, IPTC) and a keep-list that is easy to get wrong. |
| 034 | REQ-IMAGES-040 | Reject images larger than 40 megapixels before decoding them fully | REQ-IMAGES-000 | REQ-IMAGES-010, REQ-CONTENT-030 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Denial-of-service guard that must run before the generator's own decoder and handle malformed headers safely. |
| 035 | REQ-IMAGES-050 | Recheck the type and size of every file the Site Build publishes and reject SVGs with active content | REQ-IMAGES-000 | REQ-CONTENT-030 | 0.5–1 day | 120–300 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Security recheck with polyglot and SVG edge cases; must not trust extensions. |
| 036 | REQ-EXHIBITIONS-010 | Show each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors, external link and linked works | REQ-EXHIBITIONS-000 | REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-WORKS-020 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Template rendering of defined fields with optional-field handling. |
| 037 | REQ-WORKS-040 | Link each work page to the published Exhibition entries linked to that work | REQ-WORKS-000 | REQ-WORKS-020, REQ-EXHIBITIONS-010 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A reverse lookup over a relation that the model already holds. |
| 038 | REQ-EXHIBITIONS-020 | Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-010, REQ-CONTENT-040 | 0.5–1 day | 120–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Date-boundary logic fully defined by FR-4.1, with clear test cases. |
| 039 | REQ-HOME-020 | Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page | REQ-EPIC-001 | REQ-HOME-010, REQ-EXHIBITIONS-020 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A small selection rule on top of the grouping in REQ-EXHIBITIONS-020. |
| 040 | REQ-NEWS-010 | List News items in their own section of the Exhibitions/News page, newest date first | REQ-EXHIBITIONS-000 | REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010 | 0.5 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Routine list with optional fields. |
| 041 | REQ-CV-010 | Show the CV page with its six sections in order, hide empty sections and list entries newest year first | REQ-CV-000 | REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Routine grouping of entries into six fixed sections. |
| 042 | REQ-CV-020 | Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works | REQ-CV-000 | REQ-CV-010, REQ-EXHIBITIONS-020, REQ-CONTENT-040 | 0.5–1 day | 120–300 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A clear mapping rule on top of the grouping function. |
| 043 | REQ-CV-030 | Let Nina hide a derived CV line or replace its generated wording | REQ-CV-000 | REQ-CV-020 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Two small, fully specified rules on derived lines. |
| 044 | REQ-TEXTS-010 | List published Texts in Nina's order and give each its own page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010, REQ-IMAGES-010 | 0.5 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Routine list and detail pages. |
| 045 | REQ-DOWNLOADS-010 | Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF | REQ-EPIC-001 | REQ-CV-010, REQ-CONTENT-040, REQ-IMAGES-050 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A routine link list with a text label. |
| 046 | REQ-CONTACT-010 | Show Nina's email address as a mailto link and link to galerie burster on the Contact page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 50–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A single page with two content-driven links. |
| 047 | REQ-LEGAL-010 | Publish the Impressum and Datenschutzerklärung pages and link them from every page | REQ-EPIC-001 | REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010 | 0.5 day | 60–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Two Page renders and footer links; the wording is content. |
| 048 | REQ-INSTAGRAM-010 | Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram | REQ-INSTAGRAM-000 | REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A plain link plus a network check. |
| 049 | REQ-INSTAGRAM-020 | Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link | REQ-INSTAGRAM-000 | REQ-CONTENT-060, REQ-HOME-020, REQ-INSTAGRAM-010 | 0.5–1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Rendering from a neutral list with a small selection rule; the risky fetch is a separate issue. |
| 050 | REQ-PERF-010 | Meet Core Web Vitals "good" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run | REQ-EPIC-001 | REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010 | 1 day | 80–200 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Measurement and tuning against fixed numeric budgets. |
| 051 | REQ-PRIV-010 | Request no third-party resource and include no analytics, tracking, cookies or pixels in any page | REQ-EPIC-001 | REQ-LAYOUT-010, REQ-NAV-020 | 1 day | 150–350 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A browser network-recording test over a known list of page types; the rule is unambiguous. |
| 052 | REQ-HOST-010 | Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly | REQ-EPIC-001 | — | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A checklist and its first review record; no code beyond a DNS check script. |
| 053 | REQ-HOST-020 | Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root | REQ-EPIC-001 | — | 1–1.5 days | 80–250 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Server hardening is security-sensitive and a mistake (e.g. locking out SSH) is costly to undo. |
| 054 | REQ-PRIV-020 | Delete nginx access logs after 14 days and keep them readable only by the maintainer | REQ-EPIC-001 | REQ-HOST-020 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Log rotation and file permissions with a clear retention value. |
| 055 | REQ-HTTP-010 | Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS | REQ-HTTP-000 | REQ-HOST-020 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Host configuration and checks fully specified by SEC-HTTP-1. |
| 056 | REQ-HTTP-020 | Send the self-only Content Security Policy on every public response | REQ-HTTP-000 | REQ-HTTP-010, REQ-NAV-020 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | A strict CSP has to be fitted to the generator's output (inline styles, scripts) without weakening it. |
| 057 | REQ-HTTP-030 | Send nosniff and forbid framing of nina-laaf.de by other origins | REQ-HTTP-000 | REQ-HTTP-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Two headers and a framing test. |
| 058 | REQ-HTTP-040 | Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP on the preview site | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-020, REQ-HTTP-030 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Reuses the public host's header set on two more hosts. |
| 059 | REQ-HTTP-060 | Renew the TLS certificates of all three hosts automatically and alert Ives when a renewal fails | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-040 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Standard renewal job and expiry check once the source and alert channel are chosen. |
| 060 | REQ-HTTP-070 | Limit requests and connections per IP address on all three hosts | REQ-HTTP-000 | REQ-HTTP-010, REQ-HTTP-040 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Standard nginx limits once the values are set. |
| 061 | REQ-STORE-010 | Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record | REQ-STORE-000 | — | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Repository settings and a record file, fully specified by SEC-AUTHZ-1, -2 and -4. |
| 062 | REQ-STORE-020 | Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there | REQ-STORE-000 | — | 0.5 day | 0–20 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | One settings change and one test. |
| 063 | REQ-STORE-030 | Limit write access to dalini/nina-site to the maintainer | REQ-STORE-000 | — | 0.5 day | 0–20 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Repository settings with one negative test. |
| 064 | REQ-PIPELINE-010 | Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key | REQ-PIPELINE-000 | REQ-CONTENT-030, REQ-HOST-020, REQ-STORE-010 | 1–2 days | 100–300 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Least-privilege deploy identity, host-key pinning and fail-closed gating across GitHub Actions and the VPS. |
| 065 | REQ-PIPELINE-020 | Deploy only the Site Build's generated output | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Point the upload at the output directory and add a post-deploy path check. |
| 066 | REQ-PIPELINE-030 | Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Mechanical pinning and checksum verification with a lint check. |
| 067 | REQ-PIPELINE-040 | Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–120 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Secret scanning, masking and a fail-closed gate with platform features. |
| 068 | REQ-PIPELINE-060 | Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-CONTENT-040 | 0.5–1 day | 60–180 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Key separation between two outputs from one run is easy to get subtly wrong (T-20, T-21). |
| 069 | REQ-PREVIEW-010 | Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins | REQ-PIPELINE-000 | REQ-HTTP-040, REQ-PIPELINE-060 | 0.5–1 day | 30–100 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Authentication setup with password-storage and guessing-limit requirements. |
| 070 | REQ-PIPELINE-070 | Record what each production deploy was built from and restore the previous version without a content change | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 40–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A deploy record and a restore drill using the release layout from REQ-PIPELINE-010. |
| 071 | REQ-PIPELINE-080 | Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 20–80 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Workflow trigger configuration fully specified by SEC-CICD-4. |
| 072 | REQ-PIPELINE-090 | Process Content Store files without access to the deploy keys or the Instagram token | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Job isolation for secrets in GitHub Actions has subtle leaks (shared workspaces, artifacts, environment inheritance). |
| 073 | REQ-INSTAGRAM-030 | Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks | REQ-INSTAGRAM-000 | REQ-INSTAGRAM-020, REQ-IMAGES-020, REQ-IMAGES-040, REQ-PIPELINE-090 | 1–1.5 days | 200–450 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Security-sensitive integration with untrusted external data, a secret and per-item failure handling. |
| 074 | REQ-INSTAGRAM-040 | Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails | REQ-INSTAGRAM-000 | REQ-INSTAGRAM-030, REQ-PIPELINE-010 | 0.5–1 day | 80–200 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | It reads back across a trust boundary from hosting and must fail safe in several ways. |
| 075 | REQ-PIPELINE-100 | Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 10–40 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Configuration and settings with a simple test. |
| 076 | REQ-PIPELINE-110 | Email Ives when a build fails | REQ-PIPELINE-000 | REQ-PIPELINE-010 | 0.5 day | 0–30 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Notification settings and two forced-failure tests. |
| 077 | REQ-PIPELINE-120 | Write the publish status file with only the content version, the result and the failing item and field | REQ-PIPELINE-000 | REQ-PIPELINE-010, REQ-CONTENT-030 | 0.5 day | 50–150 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A small writer with a fixed schema; the open access question is excluded. |
| 078 | REQ-EDIT-010 | Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN | REQ-EDIT-000 | REQ-HTTP-040, REQ-STORE-030 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Supply-chain control for code that runs with every editor's token. |
| 079 | REQ-HTTP-050 | Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub | REQ-HTTP-000 | REQ-HTTP-040, REQ-EDIT-010 | 0.5–1 day | 30–120 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Fitting a strict CSP to a third-party single-page app without weakening it protects the editors' tokens. |
| 080 | REQ-EDIT-020 | Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-010 | 1–2 days | 150–400 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Declarative CMS configuration following the documented model. |
| 081 | REQ-EDIT-030 | Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-050 | 1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Declarative CMS configuration following the documented model. |
| 082 | REQ-EDIT-040 | Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-020 | 1 day | 100–250 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Declarative CMS configuration following the documented model. |
| 083 | REQ-EDIT-050 | Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-040 | 0.5–1 day | 50–250 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Security-relevant validation in a third-party UI, where support for content sniffing and pixel checks must be verified. |
| 084 | REQ-EDIT-060 | Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | 0.5–1 day | 50–150 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Security verification across every view of a third-party UI, with configuration to restrict formats. |
| 085 | REQ-EDIT-070 | Let Nina hide a single Instagram post and switch the feed off in the Editing Interface | REQ-EDIT-000 | REQ-EDIT-010, REQ-CONTENT-060 | 0.5 day | 30–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A small settings form. |
| 086 | REQ-EDIT-080 | Show who changed each content item and when, and restore an earlier version | REQ-EDIT-000 | REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040 | 0.5–1 day | 30–200 | Claude Opus 5.5 (`claude-opus-5-5`, NOT VERIFIED) | Depends on a capability of the editing system that must be verified, with a careful restore path. |
| 087 | REQ-MIGRATION-010 | Import the 53 legacy works with their German title, year and images into the Content Store | REQ-MIGRATION-000 | REQ-CONTENT-010 | 1–1.5 days | 200–450 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A mapping script from a known, uniform source format. |
| 088 | REQ-MIGRATION-020 | Import legacy works without a year as unpublished, publish the rest, and feature the newest work | REQ-MIGRATION-000 | REQ-MIGRATION-010, REQ-CONTENT-040 | 0.5 day | 40–100 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | Two fixed rules added to the import. |
| 089 | REQ-MIGRATION-030 | Give every migrated image interim alt text made of the work title and the view number | REQ-MIGRATION-000 | REQ-MIGRATION-010 | 0.5 day | 20–60 | Claude Sonnet 5.5 (`claude-sonnet-5-5`, NOT VERIFIED) | A single string rule added to the import. |

Total for the 76 leaves: 45–67 engineer-days.

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
10. REQ-HTTP-000
11. REQ-STORE-000
12. REQ-PIPELINE-000
13. REQ-EDIT-000
14. REQ-MIGRATION-000
15. REQ-CONTENT-010
16. REQ-CONTENT-020
17. REQ-CONTENT-050
18. REQ-CONTENT-060
19. REQ-CONTENT-030
20. REQ-CONTENT-040
21. REQ-CONTENT-070
22. REQ-OUTPUT-010
23. REQ-OUTPUT-020
24. REQ-OUTPUT-030
25. REQ-LAYOUT-010
26. REQ-NAV-020
27. REQ-NAV-030
28. REQ-NAV-010
29. REQ-WORKS-020
30. REQ-IMAGES-010
31. REQ-HOME-010
32. REQ-WORKS-010
33. REQ-WORKS-030
34. REQ-IMAGES-020
35. REQ-IMAGES-040
36. REQ-IMAGES-050
37. REQ-EXHIBITIONS-010
38. REQ-WORKS-040
39. REQ-EXHIBITIONS-020
40. REQ-HOME-020
41. REQ-NEWS-010
42. REQ-CV-010
43. REQ-CV-020
44. REQ-CV-030
45. REQ-TEXTS-010
46. REQ-DOWNLOADS-010
47. REQ-CONTACT-010
48. REQ-LEGAL-010
49. REQ-INSTAGRAM-010
50. REQ-INSTAGRAM-020
51. REQ-PERF-010
52. REQ-PRIV-010
53. REQ-HOST-010
54. REQ-HOST-020
55. REQ-PRIV-020
56. REQ-HTTP-010
57. REQ-HTTP-020
58. REQ-HTTP-030
59. REQ-HTTP-040
60. REQ-HTTP-060
61. REQ-HTTP-070
62. REQ-STORE-010
63. REQ-STORE-020
64. REQ-STORE-030
65. REQ-PIPELINE-010
66. REQ-PIPELINE-020
67. REQ-PIPELINE-030
68. REQ-PIPELINE-040
69. REQ-PIPELINE-060
70. REQ-PREVIEW-010
71. REQ-PIPELINE-070
72. REQ-PIPELINE-080
73. REQ-PIPELINE-090
74. REQ-INSTAGRAM-030
75. REQ-INSTAGRAM-040
76. REQ-PIPELINE-100
77. REQ-PIPELINE-110
78. REQ-PIPELINE-120
79. REQ-EDIT-010
80. REQ-HTTP-050
81. REQ-EDIT-020
82. REQ-EDIT-030
83. REQ-EDIT-040
84. REQ-EDIT-050
85. REQ-EDIT-060
86. REQ-EDIT-070
87. REQ-EDIT-080
88. REQ-MIGRATION-010
89. REQ-MIGRATION-020
90. REQ-MIGRATION-030

## Proposed Commands (not run)

Pre-flight checks for CREATE mode: `gh auth status`; confirm the target repository is `dalini/nina-website` (the specification repository, as in the earlier run); confirm that `gh issue create` in the installed version supports `--body-file` and `--parent` (otherwise link sub-issues through the GitHub API); confirm no existing issue reuses a planning ID for different scope.

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
gh issue create --title "[REQ-HTTP-000] Public site and HTTP boundary" --body-file ".planning/github-issues/009-REQ-HTTP-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-STORE-000] Content Store and code repository access" --body-file ".planning/github-issues/010-REQ-STORE-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PIPELINE-000] Secrets and build/deploy pipeline" --body-file ".planning/github-issues/011-REQ-PIPELINE-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-EDIT-000] Editing Interface (Sveltia CMS)" --body-file ".planning/github-issues/012-REQ-EDIT-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-MIGRATION-000] Legacy migration" --body-file ".planning/github-issues/013-REQ-MIGRATION-000.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTENT-010] Define the Work and Image content model with its required-field rules" --body-file ".planning/github-issues/014-REQ-CONTENT-010.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-020] Define the Text, CV entry, Download and Page content models" --body-file ".planning/github-issues/015-REQ-CONTENT-020.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-050] Define the Exhibition entry and News item content models" --body-file ".planning/github-issues/016-REQ-CONTENT-050.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-060] Define the Instagram settings: hidden posts and the feed switch" --body-file ".planning/github-issues/017-REQ-CONTENT-060.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-030] Fail the Site Build and publish nothing when content violates the content model" --body-file ".planning/github-issues/018-REQ-CONTENT-030.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-040] Exclude unpublished content from the production output" --body-file ".planning/github-issues/019-REQ-CONTENT-040.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-CONTENT-070] Fail the Site Build when the Content Store checkout contains a symbolic link or special file" --body-file ".planning/github-issues/020-REQ-CONTENT-070.md"  # parent: REQ-CONTENT-000
gh issue create --title "[REQ-OUTPUT-010] HTML-encode editor text and render rich text only from the restricted format" --body-file ".planning/github-issues/021-REQ-OUTPUT-010.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-020] Treat content fields as data and never evaluate generator syntax inside them" --body-file ".planning/github-issues/022-REQ-OUTPUT-020.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-OUTPUT-030] Allow only https: and mailto: links in content and open external links with noopener" --body-file ".planning/github-issues/023-REQ-OUTPUT-030.md"  # parent: REQ-OUTPUT-000
gh issue create --title "[REQ-LAYOUT-010] Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon" --body-file ".planning/github-issues/024-REQ-LAYOUT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NAV-020] Generate every page in English at / and German under /de/ with a language switch to the same page" --body-file ".planning/github-issues/025-REQ-NAV-020.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-030] Show the other language's value, marked with its lang attribute, when a field's value is empty" --body-file ".planning/github-issues/026-REQ-NAV-030.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-NAV-010] Show the main navigation to Works, Exhibitions/News, Texts, CV and Contact on every page" --body-file ".planning/github-issues/027-REQ-NAV-010.md"  # parent: REQ-NAV-000
gh issue create --title "[REQ-WORKS-020] Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text" --body-file ".planning/github-issues/028-REQ-WORKS-020.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-IMAGES-010] Publish images only as responsive sizes of at most 2400 px on the long side, never as originals" --body-file ".planning/github-issues/029-REQ-IMAGES-010.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-HOME-010] Show the artist's name and the featured work's image, linked to its work page, on the home page" --body-file ".planning/github-issues/030-REQ-HOME-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-WORKS-010] List all published works newest year first or in Nina's order, and show works in public space as a group" --body-file ".planning/github-issues/031-REQ-WORKS-010.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-WORKS-030] Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking" --body-file ".planning/github-issues/032-REQ-WORKS-030.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-IMAGES-020] Remove embedded metadata from published images except the copyright notice and the creator" --body-file ".planning/github-issues/033-REQ-IMAGES-020.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-040] Reject images larger than 40 megapixels before decoding them fully" --body-file ".planning/github-issues/034-REQ-IMAGES-040.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-IMAGES-050] Recheck the type and size of every file the Site Build publishes and reject SVGs with active content" --body-file ".planning/github-issues/035-REQ-IMAGES-050.md"  # parent: REQ-IMAGES-000
gh issue create --title "[REQ-EXHIBITIONS-010] Show each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors, external link and linked works" --body-file ".planning/github-issues/036-REQ-EXHIBITIONS-010.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-WORKS-040] Link each work page to the published Exhibition entries linked to that work" --body-file ".planning/github-issues/037-REQ-WORKS-040.md"  # parent: REQ-WORKS-000
gh issue create --title "[REQ-EXHIBITIONS-020] Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive" --body-file ".planning/github-issues/038-REQ-EXHIBITIONS-020.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-HOME-020] Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page" --body-file ".planning/github-issues/039-REQ-HOME-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-NEWS-010] List News items in their own section of the Exhibitions/News page, newest date first" --body-file ".planning/github-issues/040-REQ-NEWS-010.md"  # parent: REQ-EXHIBITIONS-000
gh issue create --title "[REQ-CV-010] Show the CV page with its six sections in order, hide empty sections and list entries newest year first" --body-file ".planning/github-issues/041-REQ-CV-010.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-CV-020] Fill the CV exhibition and works-in-public-space sections from Exhibition entries and works" --body-file ".planning/github-issues/042-REQ-CV-020.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-CV-030] Let Nina hide a derived CV line or replace its generated wording" --body-file ".planning/github-issues/043-REQ-CV-030.md"  # parent: REQ-CV-000
gh issue create --title "[REQ-TEXTS-010] List published Texts in Nina's order and give each its own page" --body-file ".planning/github-issues/044-REQ-TEXTS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-DOWNLOADS-010] Offer the PDFs Nina publishes on the CV page, each labelled with its title and as a PDF" --body-file ".planning/github-issues/045-REQ-DOWNLOADS-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-CONTACT-010] Show Nina's email address as a mailto link and link to galerie burster on the Contact page" --body-file ".planning/github-issues/046-REQ-CONTACT-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-LEGAL-010] Publish the Impressum and Datenschutzerklärung pages and link them from every page" --body-file ".planning/github-issues/047-REQ-LEGAL-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-INSTAGRAM-010] Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram" --body-file ".planning/github-issues/048-REQ-INSTAGRAM-010.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-020] Show the 6 most recent visible Instagram posts on the home page from the build's post list, or only the account link" --body-file ".planning/github-issues/049-REQ-INSTAGRAM-020.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-PERF-010] Meet Core Web Vitals \"good\" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run" --body-file ".planning/github-issues/050-REQ-PERF-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-010] Request no third-party resource and include no analytics, tracking, cookies or pixels in any page" --body-file ".planning/github-issues/051-REQ-PRIV-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-010] Hold the registrar, DNS and VPS accounts with individual logins and second factors, and review them yearly" --body-file ".planning/github-issues/052-REQ-HOST-010.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HOST-020] Harden the VPS: security updates, key-only SSH without root sign-in, only ports 22, 80 and 443, and nginx workers without root" --body-file ".planning/github-issues/053-REQ-HOST-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-PRIV-020] Delete nginx access logs after 14 days and keep them readable only by the maintainer" --body-file ".planning/github-issues/054-REQ-PRIV-020.md"  # parent: REQ-EPIC-001
gh issue create --title "[REQ-HTTP-010] Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS" --body-file ".planning/github-issues/055-REQ-HTTP-010.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-020] Send the self-only Content Security Policy on every public response" --body-file ".planning/github-issues/056-REQ-HTTP-020.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-030] Send nosniff and forbid framing of nina-laaf.de by other origins" --body-file ".planning/github-issues/057-REQ-HTTP-030.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-040] Serve edit.nina-laaf.de and preview.nina-laaf.de over HTTPS with HSTS, nosniff and no framing, and the public CSP on the preview site" --body-file ".planning/github-issues/058-REQ-HTTP-040.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-060] Renew the TLS certificates of all three hosts automatically and alert Ives when a renewal fails" --body-file ".planning/github-issues/059-REQ-HTTP-060.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-HTTP-070] Limit requests and connections per IP address on all three hosts" --body-file ".planning/github-issues/060-REQ-HTTP-070.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-STORE-010] Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record" --body-file ".planning/github-issues/061-REQ-STORE-010.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-020] Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there" --body-file ".planning/github-issues/062-REQ-STORE-020.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-STORE-030] Limit write access to dalini/nina-site to the maintainer" --body-file ".planning/github-issues/063-REQ-STORE-030.md"  # parent: REQ-STORE-000
gh issue create --title "[REQ-PIPELINE-010] Build main of the Content Store and deploy it with a least-privilege, non-root deploy account over a pinned SSH host key" --body-file ".planning/github-issues/064-REQ-PIPELINE-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-020] Deploy only the Site Build's generated output" --body-file ".planning/github-issues/065-REQ-PIPELINE-020.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-030] Pin CI actions and build tools to exact versions, verify downloaded tools, and resolve dependencies from a lockfile" --body-file ".planning/github-issues/066-REQ-PIPELINE-030.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-040] Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change" --body-file ".planning/github-issues/067-REQ-PIPELINE-040.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-060] Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key" --body-file ".planning/github-issues/068-REQ-PIPELINE-060.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PREVIEW-010] Protect preview.nina-laaf.de with per-editor HTTP Basic Auth using strong password hashes and limited failed sign-ins" --body-file ".planning/github-issues/069-REQ-PREVIEW-010.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-070] Record what each production deploy was built from and restore the previous version without a content change" --body-file ".planning/github-issues/070-REQ-PIPELINE-070.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-080] Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group" --body-file ".planning/github-issues/071-REQ-PIPELINE-080.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-090] Process Content Store files without access to the deploy keys or the Instagram token" --body-file ".planning/github-issues/072-REQ-PIPELINE-090.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-INSTAGRAM-030] Fetch recent @ninalaaf posts at build time, self-host their images and leave out every post that fails the checks" --body-file ".planning/github-issues/073-REQ-INSTAGRAM-030.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-INSTAGRAM-040] Reuse the last successfully fetched posts from the VPS when an Instagram refresh fails" --body-file ".planning/github-issues/074-REQ-INSTAGRAM-040.md"  # parent: REQ-INSTAGRAM-000
gh issue create --title "[REQ-PIPELINE-100] Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives" --body-file ".planning/github-issues/075-REQ-PIPELINE-100.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-110] Email Ives when a build fails" --body-file ".planning/github-issues/076-REQ-PIPELINE-110.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-PIPELINE-120] Write the publish status file with only the content version, the result and the failing item and field" --body-file ".planning/github-issues/077-REQ-PIPELINE-120.md"  # parent: REQ-PIPELINE-000
gh issue create --title "[REQ-EDIT-010] Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN" --body-file ".planning/github-issues/078-REQ-EDIT-010.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-HTTP-050] Send a Content Security Policy on edit.nina-laaf.de that allows script only from its own origin and connections only to it and GitHub" --body-file ".planning/github-issues/079-REQ-HTTP-050.md"  # parent: REQ-HTTP-000
gh issue create --title "[REQ-EDIT-020] Let editors create, edit, unpublish and delete works and their images, with required-field checks and the featured, public-space and order settings" --body-file ".planning/github-issues/080-REQ-EDIT-020.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-030] Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks" --body-file ".planning/github-issues/081-REQ-EDIT-030.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-040] Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order" --body-file ".planning/github-issues/082-REQ-EDIT-040.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-050] Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload" --body-file ".planning/github-issues/083-REQ-EDIT-050.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-060] Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup" --body-file ".planning/github-issues/084-REQ-EDIT-060.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-070] Let Nina hide a single Instagram post and switch the feed off in the Editing Interface" --body-file ".planning/github-issues/085-REQ-EDIT-070.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-EDIT-080] Show who changed each content item and when, and restore an earlier version" --body-file ".planning/github-issues/086-REQ-EDIT-080.md"  # parent: REQ-EDIT-000
gh issue create --title "[REQ-MIGRATION-010] Import the 53 legacy works with their German title, year and images into the Content Store" --body-file ".planning/github-issues/087-REQ-MIGRATION-010.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-020] Import legacy works without a year as unpublished, publish the rest, and feature the newest work" --body-file ".planning/github-issues/088-REQ-MIGRATION-020.md"  # parent: REQ-MIGRATION-000
gh issue create --title "[REQ-MIGRATION-030] Give every migrated image interim alt text made of the work title and the view number" --body-file ".planning/github-issues/089-REQ-MIGRATION-030.md"  # parent: REQ-MIGRATION-000
```

## Before CREATE

1. Run step 10 (`prompts/10-resolve-open-questions.md`) to answer the blocking questions PQ-29 to PQ-39 (PQ-29 to PQ-34 are SECURITY.md SQ-16 to SQ-21) and, where possible, the non-blocking PQ-40 to PQ-53.
2. Re-run step 9 in DRAFT_ONLY: the answers change the specs, so the blocked scope gets issues and these drafts are re-decomposed.
3. Run step 9 with `Execution mode: CREATE` once no blocking question remains; CREATE stops before any GitHub change while one does.
