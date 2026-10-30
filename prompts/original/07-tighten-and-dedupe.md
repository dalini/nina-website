Review the specification files so each owns exactly one domain, nothing is duplicated across files, and no information is lost.

## Ownership

- `REQUIREMENTS.md` — WHAT the system must do: functional and non-functional requirements, each with testable acceptance criteria.
- `DESIGN.md` — the visual and frontend design language: brand and logo, color palette, typography, layout, component conventions, accessibility targets.
- `style-guide.html` — the rendered form of `DESIGN.md`'s tokens and components. It repeats token values by design; that is synchronization, not duplication. Flag it only where it disagrees with `DESIGN.md`.
- `ARCHITECTURE.md` — HOW it is built: components, interfaces, data flow, trust boundaries, technology choices and rationale.
- `SECURITY.md` — the security posture: threat model, security requirements, controls, trust-boundary enforcement. All security requirements are authored here.
- `CLAUDE.md` — agent operating rules: coding conventions, enforcement rules, build/test/run commands, do/don't guidance. It defines no system behavior or architecture.

Non-owning files reference the owning file; they never restate its content (no repeated design tokens, security requirements, or architecture decisions). Tiebreak when content fits more than one domain: security content goes to `SECURITY.md`; everything else goes to the owning file above.

## Procedure

1. **Inventory** — list every distinct requirement, rule, claim, and threat entry (`FR-*`, `SEC-*`, `DEP-*`, `DR-*`, `T-*`) across the files and tag each with its owning file.
2. **Detect** — flag (a) duplicates, (b) contradictions, (c) low-signal content: filler, motivational text, restated-obvious points, dead links.
3. **Resolve** — for each duplicate, keep the copy in the owning file and delete the rest, leaving at most a one-line cross-reference where genuinely needed. For each contradiction, keep the more secure option and remove or replace the other. Delete low-signal content; when genuinely unsure whether something is load-bearing, keep it and flag it for a human decision instead of deleting.
4. **Verify** — re-read the files end to end. Confirm no requirement, control, constraint, or threat mapping was dropped, every cross-reference and ID reference resolves to a real entry, and each file stands on its own.
