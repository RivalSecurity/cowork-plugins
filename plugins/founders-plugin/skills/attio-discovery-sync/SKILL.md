---
name: attio-discovery-sync
description: >
  Syncs structured sales discovery data from a transcript or extraction already in context into the target Attio company record. Use this skill whenever the user wants to push discovery insights into Attio — e.g. "update Attio with the discovery", "fill in the Attio fields", "sync these findings to Attio", "push the insights to Attio", "update the company record with what we learned", "log the discovery data". Always use this skill even if the user doesn't explicitly say "Attio" — if extracted discovery data is in context and the user wants to save it somewhere structured, this applies. Also use after running extract-transcript-insights when the user wants to persist the output.
---

# Attio Discovery Sync

Takes structured discovery data from a transcript or extraction already in context and writes it into the Attio company record — specifically the seven fields used to track technical environment, vulnerability management posture, vulnerabilities focus, and workflow fit. Always shows the user a review table before writing anything.

## Fields this skill writes to

| Field | Attio slug | Type |
|---|---|---|
| Scanner Stack | `scanner_stack` | multiselect select |
| Capabilities Needed | `capabilities_needed_5` | multiselect select |
| Infrastructure | `infrastructure` | multiselect select |
| Current Process Description | `current_process_description` | text |
| Ticketing Systems | `ticketing_systems` | multiselect select |
| Asset Scale | `asset_scale` | single select |
| Vulnerabilities Focus | `vulnerabilities_focus` | multiselect select |

---

## Process

### Step 1 — Identify the company record

If the Attio record ID isn't already known from context, search using `search-records` on the `companies` object. If multiple records exist for the same company, prefer the one with `account_status: Pipeline` or the one with the most recent interaction data.

### Step 2 — Read field definitions and current values (run in parallel)

- `list-attribute-definitions` on `companies`, querying for the seven slugs above — to get the full list of available select options for each field
- `get-records-by-ids` for the company — to see what's already populated

This tells you two things: what options exist (so you don't try to write something that isn't there), and what's already set (so you add without clobbering).

### Step 3 — Propose values from context

For each field, extract a proposed value from the transcript and/or discovery extraction in context. Apply these rules consistently:

**Evidence standard**: Only propose a value if it's grounded in something the prospect actually said or clearly demonstrated. Don't fill fields based on company size, industry norms, or what similar companies typically use. If a field topic wasn't raised in the call, mark it `Skip — not mentioned`.

**Select fields**: Only propose values that match existing options exactly. If the right value doesn't exist as an option yet, mark the field `Blocked — option "[value]" doesn't exist`. Do not try to create options; that requires manual action in Attio settings.

**Multiselect fields**: Propose values to add. If the field already has values, list them as context — your job is to add, not replace.

**Text fields**: If the field is empty, write the full description. If it already has content, don't overwrite it — flag it as `Needs review — field already populated` and show the user both the existing and proposed values so they can decide.

**Asset Scale**: This is frequently not stated directly. If you must infer, use employee count, fleet size descriptions, or compliance scope as signals — but flag it clearly as `Inferred — not explicitly stated` so the user knows the confidence level.

**Vulnerabilities Focus**: Map from the extraction's **Vulnerabilities focus** (Code / Cloud / OS / Both/All). Propose the matching option(s): one value for Code, Cloud, or OS; or all three (Code, Cloud, OS) when the extraction says **Both/All**. Only propose values that exist as options. Use `patch_multiselect_values: true` when writing — add without removing existing values.

### Step 4 — Show the review table

Before writing anything to Attio, present this table:

| Field | Action | Value | Why |
|---|---|---|---|

**Action** values:
- `Add` — adding to an existing multiselect (non-destructive)
- `Set` — writing to an empty field
- `Skip` — not mentioned, or field already has correct value
- `Blocked` — option doesn't exist in Attio
- `Needs review` — text field already populated, showing both values

After the table, note any blocked fields in plain language: what option is missing and what the user would need to create in Attio settings to unblock it.

Wait for explicit confirmation before proceeding. "go ahead", "yes", "looks good", or similar.

### Step 5 — Write confirmed fields

For each confirmed `Add` or `Set` field:
- Use `update-record` on the `companies` object
- For multiselect fields, always pass `patch_multiselect_values: true` — this adds to existing values without removing them
- Skip `Blocked` and `Skip` fields entirely

### Step 6 — Use-Cases list

Call `list-records-in-list` on the `use_cases` list and scan the results for the company's record ID. If it's not there, add it with `add-record-to-list`. The `allow_duplicates` default is false, so this is safe to call even if you're uncertain whether it's already present.

### Step 7 — Report back

One short paragraph summarizing: what was written, what was skipped, and what was blocked (if anything). Don't re-list everything — just cover anything that needs follow-up.

---

## Common failure modes

**"Cannot find select option"** on `update-record`: The value doesn't exist as an option in Attio. You can't create select options via the API. Tell the user exactly which option is missing and in which field so they can add it in Attio settings and you can retry.

**Multiple company records**: Amex GBT (and similar acquired companies) often have several Attio records from different sources. Always pick the one with active pipeline/deal data and note which record ID you used.

**Text field already has content**: Never silently overwrite. Show the user both the current value and your proposed value and let them decide.
