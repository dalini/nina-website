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

- Done: steps 1–4 (`REQUIREMENTS.md`, `logo.svg`, `DESIGN.md`, `style-guide.html`, `ARCHITECTURE.md`, `SECURITY.md`).
- Next: step 5 (`prompts/05-requirement-template.md`), in a fresh session.

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
