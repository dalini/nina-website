Before we write any code, resolve every open question in this project.

Scan the entire repo for unresolved items:
- any `Open Questions` or `Open Security Questions` section, at any heading level (`REQUIREMENTS.md`, `DESIGN.md`, `ARCHITECTURE.md`, `SECURITY.md`, the epic, sub-issues)
- every `TO BE DECIDED`, `UNKNOWN`, `TBD`, or unresolved `{{PLACEHOLDER}}` marker, including provisional defaults labeled in `style-guide.html` — but not `{{ISSUE_URL:...}}` markers, which the issue-creation run resolves, not this interview
- "Required ... Inputs" fields that are still blank or provisional
- entries labeled `ASSUMPTION` in `ARCHITECTURE.md` and `SECURITY.md`
- threat-model rows and `SQ-*` questions that still need a decision
- GitHub issues or epic items flagged as blocked, ambiguous, or needing input Collect all of these into a single ordered list, most foundational first (requirements and architecture before downstream details).

Then interview me. Ask in small batches of closely related questions — up to four at a time, each with concrete options where sensible defaults exist. Wait for my answers before the next batch. Do not guess and do not fill anything in on my behalf; "defer" is an acceptable answer. If my answer raises a new question, add it to the list.

Record each answer as it lands. Once every question is answered or explicitly deferred:
- update `REQUIREMENTS.md`, `DESIGN.md`, `ARCHITECTURE.md`, `SECURITY.md`, `CLAUDE.md`, `style-guide.html`, the epic, and any sub-issues with the resolved values
- remove the markers and resolved placeholders you addressed, and clear the Open Questions entries you resolved
- keep anything I deferred clearly visible so it is not lost Do not start implementation until every pending question is either answered or explicitly deferred by me.
