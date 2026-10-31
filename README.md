# nina-laaf.de: spec-driven starter kit

Built on Jim Manico's *Spec-Driven Bootstrap, Claude 5 Edition* (Manicode Security webinar handout).
Eleven prompts take an empty repo to a full specification, and then to code.

## What's in here

| Path | What it is |
|---|---|
| `docs/PROJECT_NOTES.md` | The input used for steps 1, 3 and 4. Archived: since step 4 the spec files are the source of truth. |
| `docs/CONTENT_SEED.md` | Bio, awards, exhibitions and works collected from public sources, to verify with Nina. Content for loading the site later; not an input to the prompts. It lives in `docs/` so steps 7 and 10 don't treat it as spec. |
| `docs/legacy-content/` | Text content of the previous site (Koken → Eleventy attempt in `dalini/nina-laaf-site`): 53 works, CV, news, contact, essay, old requirements. Images remain in that repo's Git LFS. Reference only. |
| `prompts/01…11-*.md` | The 11 prompts, ready to paste. Steps 1, 3 and 4 read `docs/PROJECT_NOTES.md`. |
| `prompts/original/` | The same prompts, verbatim with the original `<<PLACEHOLDER>>` blocks, for other projects. |

## Status

- Done: steps 1–8 (`REQUIREMENTS.md`, `logo.svg`, `DESIGN.md`, `style-guide.html`, `ARCHITECTURE.md`, `SECURITY.md`, `REQUIREMENT_TEMPLATE.md`, `CLAUDE.md`, `AGENTS.md`; step 7 tightened and de-duplicated them; step 8 added the STRIDE threat model v1, T-1 to T-42, to `SECURITY.md` and the resulting rules, requirements and dependency rules).
- Done: step 9 in `DRAFT_ONLY` (`.planning/github-issues/`: `ISSUE_PLAN.md`, 1 epic, 6 workstreams, 29 leaf issues). 15 blocking questions (PQ-1 to PQ-15) leave the editing interface, images, home page, exhibitions, Instagram feed, default/fallback language, required fields, DNS accounts, backups and migration undecomposed.
- Done: step 10. Every open question was answered (record: `.planning/OPEN_QUESTIONS.md`): Hugo, Sveltia CMS on `edit.nina-laaf.de`, content in a private `dalini/nina-content` repository, code in `dalini/nina-site`, GitHub Actions deploying to a VPS with nginx, Instagram posts copied at build time, English at `/` and German under `/de/`. Deferred: the final logo mark and the test command.
- Done: step 8 again (threat model v2, 2026-10-31) against the decided architecture: T-43 to T-68 added to `SECURITY.md` with new and strengthened rules (SEC-HTTP-4, -5, SEC-HOST-2, SEC-AUTHN-2 to -5, SEC-AUTHZ-3, SEC-AUDIT-1, SEC-AVAIL-1, SEC-INPUT-1, -2, -7, SEC-OUT-3, -4, SEC-PRIV-5, SEC-SECRET-1, -2, SEC-CICD-1, -2, -4, -6 to -9), FR-8.5 and FR-10.8 extended, and the Site Build split into stages in `ARCHITECTURE.md`. Six security questions are open (SQ-16 to SQ-21): session limits and token scope of the CMS sign-in, the GitHub plan needed for branch protection and environments on private repositories, access to the publish status file, how non-root processes on the VPS read their secrets, and the OAuth helper's runtime.
- Done: step 9 again in `DRAFT_ONLY` (2026-10-31) against the decided specs: `.planning/github-issues/` now holds 1 epic, 13 workstreams and 76 leaf issues (the first drafts were replaced). 11 blocking questions (PQ-29 to PQ-39) leave their scope undecomposed: SQ-16 to SQ-21 (as PQ-29 to PQ-34: CMS sign-in, sessions, credential scope, GitHub plan, status-file access, VPS secrets and jobs), statistics vs maintainer-only logs, exhibition required fields, legacy materials/dimensions, the image viewer, and PDF metadata handling. 14 non-blocking questions (PQ-40 to PQ-53) are marked in the issues.
- Done: step 10 again (2026-10-31). PQ-29 to PQ-53 (including SQ-16 to SQ-21) are answered, and the follow-up questions they raised too (record: `.planning/OPEN_QUESTIONS.md`). Decisions include: a paid GitHub plan; the CMS keeps the default OAuth scope, and the missing session limits are accepted as exceptions; the OAuth helper is ported to Deno in `dalini/nina-site`; one secret file per unprivileged account; a public publish status file; Google Analytics with Consent Mode v2 and an own consent banner (replacing the no-tracking rules); a lightbox; a page for each exhibition; image captions; German interface labels; `deno task test`. Deferred: the final logo mark.
- Done: step 8 a third time (threat model v3, 2026-10-31): Google Analytics and the consent banner are modeled (new trust boundary TB-6), and every element was rechecked against the decisions of step 10 again. T-69 to T-85 were added to `SECURITY.md`, with new rules SEC-PRIV-7 (administration of the Google Analytics property) and SEC-OUT-7 (the build fails on pages with scripts other than the site's own), and strengthened SEC-HTTP-2, SEC-PRIV-5, SEC-AUTHN-4, SEC-AUDIT-1, SEC-INPUT-6, SEC-SECRET-1, SEC-CICD-1 and SEC-CICD-2. FR-9.2 was extended (pages work when the Google tag cannot load), and `ARCHITECTURE.md` got one-line references. Two security questions are open: SQ-22 (how the Editing Interface files reach the VPS) and SQ-23 (whether the consent banner records its version and asks again when it changes).
- Done: step 9 a third time in `DRAFT_ONLY` (2026-10-31) against the current specs: `.planning/github-issues/` now holds 1 epic, 15 workstreams (new: CONSENT for Google Analytics and the consent banner, AUTHN for the OAuth helper sign-in) and 100 leaf issues. The scope blocked last time is drafted (OAuth helper, branch protection, `production` environment, VPS content check, token refresh, backup, publish status banner, PDF metadata), as is the scope added by step 10 again and threat model v3 (consent banner and Consent Mode, image viewer, Exhibition pages, SEC-OUT-7, SEC-INPUT-6, SEC-PRIV-7, deploy key limits). Model names were verified. 6 blocking questions leave their scope undecomposed: PQ-54 (= SQ-22, how the Editing Interface files reach the VPS), PQ-55 (= SQ-23, consent banner version and record), PQ-56 (where the SMTP relay credential for VPS alerts lives), PQ-57 (Exhibition slugs), PQ-58 (legacy materials and dimensions: FR-11.1 conflicts with the legacy data) and PQ-59 (how the manual Works order replaces the year order). 7 non-blocking questions (PQ-60 to PQ-66) are marked in the issues.
- Next: step 10 again for PQ-54 to PQ-66, then step 9 again (`DRAFT_ONLY`, then `CREATE`), each in a fresh session.

## How to run it

1. Clone this repo (`dalini/nina-website`).
2. Edit `docs/PROJECT_NOTES.md` before re-running steps 1, 3 or 4. Step 1 builds only what the notes say.
3. Open Claude Code in the repo. For each step, paste the whole prompt file, let it finish, review the result, commit, then run `/clear` (or start a new session) before the next step.

| Step | Prompt | Creates / changes |
|---|---|---|
| 1 | `01-requirements.md` | `REQUIREMENTS.md` |
| 2 | `02-logo-design-styleguide.md` | `logo.svg`, `DESIGN.md`, `style-guide.html` |
| 3 | `03-architecture.md` | `ARCHITECTURE.md` |
| 4 | `04-security.md` | `SECURITY.md` |
| 5 | `05-requirement-template.md` | `REQUIREMENT_TEMPLATE.md` (written verbatim) |
| 6 | `06-claude-md.md` | `CLAUDE.md`, `AGENTS.md` |
| 7 | `07-tighten-and-dedupe.md` | cleans up all spec files |
| 8 | `08-threat-model.md` | STRIDE threat model → `SECURITY.md` (+ fixes) |
| 9 | `09-github-epic.md` | draft epic + issues in `.planning/github-issues/` (`Execution mode: DRAFT_ONLY`) |
| 10 | `10-resolve-open-questions.md` | interviews you until every open question is answered or deferred |
| 9 again | `09-github-epic.md` with `Execution mode: CREATE` | creates the epic and issues on GitHub |
| 11 | `11-start-coding.md` | code, PR by PR |

## Tips

- **`docs/` is reference, not spec.** After step 4, decisions go into the spec files (REQUIREMENTS, DESIGN, ARCHITECTURE, SECURITY), not back into `docs/PROJECT_NOTES.md`. Steps 7 and 10 work on the spec files only.
- **Step 2 (logo):** if Nina already has a logo or signature, add it to the repo before step 2 and mention it in the notes. Otherwise the prompt makes a neutral placeholder mark.
- **Step 9** needs the GitHub CLI (`gh auth login`) only for the `CREATE` run.
- **Good examples:** `jmanico/oracle-buffet` (new app) and `robinschweitz/raid-website` PR #43 (existing codebase) show what each step produces.
- Expect steps 1–4 to leave plenty of `TO BE DECIDED`. That's intended: step 10 resolves them with you before any code exists.
