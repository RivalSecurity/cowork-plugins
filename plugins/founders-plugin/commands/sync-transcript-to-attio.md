---
description: Read a Circleback transcript, find company in Attio, extract insights, update Insights note, and sync discovery — with confirmation at every step.
---

# Sync transcript to Attio

You were invoked via the **sync-transcript-to-attio** command. This workflow reads a Circleback **transcript** (not the summary), finds the company in Attio, extracts discovery insights, updates the "Insights so far" note, and syncs discovery fields. **Confirm with the user before every action** — do not proceed to the next step until they approve.

---

## Step 1 — Get the transcript

1. Ask the user **which conversation** they mean (e.g. call name, date, or Circleback link).
2. Obtain the **full transcript** from Circleback — not the summary. If you have no direct access to Circleback, ask the user to paste the full transcript or provide it in a way you can read.
3. Once the transcript is in context, **confirm with the user**: "I have the transcript for [conversation]. Proceed to look up the company in Attio?" Wait for explicit approval before Step 2.

---

## Step 2 — Find the company (or person) in Attio

1. Determine whether this might be a **SageTap call** (anonymous prospect). If the transcript or context suggests SageTap or an anonymous call, do **not** search by company name. Instead:
   - **Ask the user**: "This looks like a SageTap (anonymous) call. What is the **Name of the person object** in Attio for this prospect?" Use that name to find the correct person record, then resolve the linked company from that person if needed.
2. If it is **not** a SageTap call, search for the **company** in Attio using `search-records` on the `companies` object (using company name or identifiers from the transcript).
3. If you **cannot find** the company (or the person, for SageTap): **report an error to the user** and stop. Do not continue. Tell them exactly what you searched for and that no matching record was found.
4. Once you have the target record (company, or company derived from person), **confirm with the user**: "I found [Company name] in Attio (record ID …). Proceed to extract insights from the transcript?" Wait for explicit approval before Step 3.

---

## Step 3 — Extract insights

1. Use the **extract-transcript-insights** skill. The transcript is already in context; follow that skill to produce the full structured extraction (Technical Environment, Dev Org, Vuln Management Today, Compliance & Regulatory, Pain Points & Goals, and Gaps to Address).
2. When the extraction is complete, **confirm with the user**: "Here are the extracted insights. Proceed to update the 'Insights so far' note on the company in Attio?" Wait for explicit approval before Step 4.

---

## Step 4 — Update the Insights note

1. Use the **attio-insights-note** skill. The company record (and any existing "Insights so far" note) and the extraction from Step 3 are in context. Create or update the "Insights so far" note on the company per that skill.
2. When the note is created or updated, **confirm with the user**: "The Insights so far note has been [created/updated]. Proceed to sync discovery fields (scanner stack, capabilities, infrastructure, etc.) to the company record?" Wait for explicit approval before Step 5.

---

## Step 5 — Sync discovery to Attio

1. Use the **attio-discovery-sync** skill. The extraction (and transcript) are in context; the company record is known. Build the review table of proposed field values and **wait for the user to confirm** (as required by that skill) before writing.
2. After the user confirms, perform the writes and the use-cases list step as described in the skill.
3. Report back in one short paragraph: what was written, what was skipped, and what was blocked (if anything).

---

## Reminder

- **Transcript only** — use the full Circleback transcript, never the summary.
- **SageTap** — if anonymous, ask for the Attio **person object name**; do not assume the company.
- **Confirm every action** — do not advance to the next step without explicit user approval.
