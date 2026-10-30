Conduct a STRIDE threat model of the specified system before implementation begins, then update the specification files with the results.

Read in order: `REQUIREMENTS.md`, `ARCHITECTURE.md`, `SECURITY.md`, `DESIGN.md`. No implementation exists yet — threat model the documented system, not imagined code. Where a threat depends on an unresolved decision (auth mechanism, API style, deployment model), record it conditionally rather than assuming the mechanism; unresolved decisions become open questions in the documents, not conversation.

Marker convention: `UNKNOWN` for facts the documents do not provide, `TO BE DECIDED` for decisions not yet made, `ASSUMPTION` for any unsupported inference.

## Analysis

Enumerate threats systematically, not free-form:
1. Build the analysis surface from `ARCHITECTURE.md`: every component, trust boundary, data flow, and external interface, plus the browser-facing input and rendering surfaces from `DESIGN.md` and the sensitive data and actors from `REQUIREMENTS.md`.
2. For each element, evaluate all six STRIDE categories: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege. Record "no credible threat identified" explicitly rather than skipping a category silently.
3. For each credible threat, identify whether an existing requirement or security rule already mitigates it, citing exact `FR-*` and `SEC-*` IDs.

Record the results as a table:

| Threat ID | STRIDE category | Element (component / boundary / flow) | Threat description | Existing mitigation (FR-* / SEC-* or NONE) | Gap and proposed change |
|---|---|---|---|---|---|

Use stable IDs `T-1`, `T-2`, ... Every threat names a specific element of this system, described at the level of actor, capability, and impact.

## Applying the Results

Update the artifacts using the established ownership rules:
- New or strengthened security rules go in `SECURITY.md` as `SEC-*` entries, with verification methods and traceability rows, following its existing format. Record the threat table (or a summary referencing it) in `SECURITY.md` and update `Threat model status` accordingly.
- New functional behavior needed to close a gap (an audit trail, a permission check surfaced to users) goes in `REQUIREMENTS.md` as `FR-*` requirements.
- Structural changes (a new boundary, a moved enforcement point, a changed data flow) go in `ARCHITECTURE.md`, with traceability updated.
- Never duplicate a change across files; the owning file gets the content, others get at most a one-line reference.

For each change, note the driving `T-*` ID so the edit is traceable to the threat that motivated it. Threats that cannot be resolved without a pending decision become open questions (`SQ-*` in `SECURITY.md`) tied to their `T-*` ID.

## Constraints

Propose controls at the level the documents support — no invented features, mechanisms, or vendors to mitigate threats; mark specifics `TO BE DECIDED`. Never weaken an existing requirement or rule.
