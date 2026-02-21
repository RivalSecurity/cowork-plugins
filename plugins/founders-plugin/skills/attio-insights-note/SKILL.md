---
name: attio-insights-note
description: >
  Creates or updates a living "Insights so far" note on an Attio company record, accumulating discovery findings across multiple calls. Use this skill whenever the user wants to save or update structured insights about a prospect — e.g. "update the insights note", "add what we learned to Attio", "save these insights to the company", "update insights so far", "log the findings from this call", or any variation where discovery data is in context and should be persisted as a cumulative note on the company. Always use this skill when extracted insights are in context and the user mentions saving, logging, or updating a company note — even if they don't say "Insights so far" by name.
---

# Attio Insights Note

Creates or updates a note titled **"Insights so far"** on the Attio company record. This is a living document — it accumulates findings across multiple calls with the same prospect. When the note already exists, new information is merged in intelligently rather than overwriting.

---

## Note template

Every "Insights so far" note uses this exact structure. Do not add or remove sections.

```markdown
# Insights so far — [Company Name]
_Last updated: [date] · Sources: [comma-separated list of call names or dates]_

---

## Technical Environment
**Cloud provider(s)**:
**On-prem servers**:
**Infrastructure architecture**:
**Environment scale**:
**IaC**:

---

## Dev Org
**Size of engineering/dev org**:
**Number of active contributors**:

---

## Vuln Management Today
**Current process**:
**How they prioritize**:
**Backlog size**:
**Vuln breakdown**:
**Scanners in use**:
**Aggregation layer**:
**Where vulns live**:

---

## Compliance & Regulatory
**Frameworks**:
**Is compliance a driver?**:
**Currently failing any controls?**:
**Audit cycles / pen tests / deadlines**:
**Who owns compliance**:

---

## Pain Points & Goals
**Primary pain point(s)**:
**What they've already tried**:
**What success looks like**:
**Triggering event**:
**Who else is involved**:

---

## Gaps Still Open
[Numbered list of high-value unknowns not yet answered]
```

Use "Not mentioned" for any field with no evidence. Do not leave fields blank.

---

## Process

### Step 1 — Identify the company

If the Attio record ID isn't already known from context, use `search-records` on `companies`. If multiple records exist for the same company, prefer the one with active pipeline/deal data.

### Step 2 — Find the existing "Insights so far" note

Use `search-notes-by-metadata` with `parent_record_id` and `parent_record_object: "companies"` to find notes on this company. Look for a note whose title is exactly **"Insights so far"**. If you find it, read its body with `get-note-body`.

### Step 3 — Extract insights from context

The insights to merge come from whatever is already in the conversation — a raw transcript, an extraction produced by `extract-transcript-insights`, or both. Work through each field in the template and identify the best available evidence for it.

### Step 4 — Merge (or create fresh)

**If no existing note**: Build the note fresh from the template, filling in what you know and writing "Not mentioned" for everything else.

**If an existing note exists**, merge field by field using these rules:

| Situation | Action |
|---|---|
| Existing = "Not mentioned", new value exists | Replace with new value + source attribution |
| Both have values and they agree | Keep existing, optionally note the reinforcing source |
| Both have values but they differ or new adds detail | Append new info below existing, prefixed with the source (e.g. `→ Feb 18 call:`) |
| Existing has a value, new = "Not mentioned" | Keep existing, no change |
| Gaps Still Open: a gap has now been answered | Remove it from the list |
| Gaps Still Open: new unanswered questions emerged | Add them |

Source attribution format: `→ [Call name or date]: [new info]`

After merging, update the header line to reflect the latest date and add the new source to the sources list.

### Step 5 — Write the note

**If creating fresh**: Use `create-note` with:
- `title`: "Insights so far"
- `body`: the full merged note
- `parent_object`: "companies"
- `parent_record_id`: the company's record ID

**If updating**: Use `update-note` if available, or `create-note` with a version suffix like "Insights so far (v2)" if the note cannot be edited in place. In that case, note in the summary that the old note should be archived.

### Step 6 — Report back

One sentence confirming what was done: whether the note was created or updated, on which company record, and how many fields changed. If any fields still read "Not mentioned", mention the count so the user knows what's still open.

---

## Formatting rules for note content

Attio notes render markdown. Use these conventions inside field values:

- Lead with a direct quote in italics when evidence is strong: _"they use Qualys and CrowdStrike"_
- Follow with a plain interpretation in the same line or the next
- Flag inferences explicitly: _(Inferred — not explicitly stated)_
- Flag partial information: _(Partial — not fully addressed)_
- For lists within a field (e.g. multiple pain points), use inline prose or a short bulleted sub-list

Keep values dense but readable — each field should be 1–4 sentences, not an essay. The goal is a document the team can scan in 2 minutes.

---

## What "Gaps Still Open" should contain

This section is not a dump of everything unknown. Limit it to the 3–6 fields that are both unanswered and high-value for qualification or deal progression — things like:
- Environment scale (affects pricing)
- Budget / buying decision owner (affects close path)
- Specific toolchain details needed for integration (affects technical fit)
- What success looks like concretely (affects proof of value framing)

Remove a gap when it gets answered in a later call. Add new gaps as calls surface new unknowns.
