---
name: extract-transcript-insights
description: >
  Extracts structured sales discovery insights from a prospect call transcript that is already in context. Use this skill whenever the user wants to analyze a transcript for technical environment details, vulnerability management maturity, compliance posture, or pain points — e.g. "extract insights from this transcript", "pull discovery notes from the call", "what did we learn about their environment", "summarize findings from the meeting", "fill in my discovery template", or any variation where the user wants structured qualification data from a call they just had or retrieved. Always use this skill even if the user just pastes a raw transcript or says "what did we learn" — if a transcript is in context and they want structured discovery output, this applies.
---

# Extract Transcript Insights

Extracts structured sales discovery data from a prospect call transcript. The transcript is assumed to already be in context — this skill does not retrieve it.

The goal is to produce a reliable, evidence-based qualification document that Omer and the team can trust. Every claim must be grounded in something the prospect actually said. Speculation dressed up as discovery is worse than a gap.

## Core Principle: Quote or "Not mentioned"

For every field, you have two valid outputs:

1. **A direct quote + interpretation** — pulled verbatim from the transcript, with your reading of what it means.
2. **"Not mentioned"** — used when the topic was genuinely not raised. Do not infer, assume, or fill in blanks based on company size, industry, or what "most companies like this" do.

If something is weakly implied but not stated, you can note the inference as long as you flag it clearly: _"Inferred — not explicitly stated."_

---

## Output Structure

Produce the full extraction below. Use the exact section headers. Within each item, lead with the direct quote (in italics), then your interpretation on the next line.

---

### Technical Environment

**Cloud provider(s)**
What cloud they're on, and if multiple, what each is used for.

**On-prem servers**
Yes or no. If yes: how many, why they still exist, whether migration is planned.

**Infrastructure architecture**
Kubernetes, cloud-native, lift-and-shift, hybrid, serverless — however they described it.

**Environment scale**
Number of workloads, services, repos, engineers — whatever unit they used to describe size.

**IaC**
Terraform, Pulumi, CDK, manual — or not mentioned.

---

### Dev Org

**Size of engineering/dev org**
Total headcount if mentioned.

**Number of active contributors**
Specifically active contributors to repos, if mentioned separately from total headcount.

---

### Vuln Management Today

**Current process**
End-to-end: how vulns are found, triaged, assigned, and tracked.

**How they prioritize**
CVSS score, EPSS, exploitability, asset criticality, compliance deadline, manual judgment, or not mentioned.

**Backlog size**
Total open vulns or rough estimate. Note the source (scanner, aggregator, etc.) if given.

**Vuln breakdown**
By type: cloud, container/image, SCA, SAST, runtime, network. Use their framing.

**Scanners in use**
All tools named. Note if any are being replaced or evaluated.

**Aggregation layer**
Whether they use a vuln aggregator (e.g. Nucleus, Seemplicity, Vulcan Cyber, Devo Ocean). If they built something internal, note that too.

**Where vulns live**
Jira, spreadsheet, scanner dashboard, internal system, or not mentioned.

---

### Compliance & Regulatory

**Frameworks**
SOC 2, ISO 27001, PCI-DSS, HIPAA, FedRAMP, NIST, or others actively pursued or subject to.

**Is compliance a driver for vuln management?**
Does compliance pressure drive what gets fixed, or is it a separate workstream?

**Currently failing any controls?**
Explicitly mentioned gaps or audit findings related to vulns.

**Audit cycles / pen tests / deadlines creating urgency**
Upcoming assessments, annual pen tests, certification renewals that create time pressure.

**Who owns compliance**
Security team, GRC, legal, shared, or not mentioned.

---

### Pain Points & Goals

**Primary pain point(s)**
What they said is hardest right now. Use their language.

**What they've already tried that didn't work**
Tools purchased, processes implemented, or approaches that fell short.

**What success looks like in 6 months**
Explicitly stated goals or outcomes they mentioned wanting.

**Triggering event**
Any specific incident, audit finding, executive ask, or external pressure that made this a priority now.

**Who else is involved in the buying decision**
Names, titles, or teams they mentioned needing to loop in.

---

## Formatting Rules

- Start each item with the prospect's direct quote in italics. If multiple quotes are relevant, use the most specific one and note if others reinforce it.
- Follow the quote with a plain-language interpretation — one or two sentences.
- Use **"Not mentioned"** as the entire entry when nothing relevant was said. Don't write "Not discussed in the transcript" or "The prospect did not address this topic." Just: _Not mentioned._
- If something is partially touched on but incomplete, write what was said and flag: _(partial — not fully addressed)._
- Do not editorialize. No "This is a red flag" or "This suggests a mature security posture." Stick to what was said.
- Preserve the exact section headers above — they map to a downstream template.

---

## Example Entry (good)

**Backlog size**
_"If we look at Codem, we have over 90,000 vulnerabilities right now… and then in Suite, it's similar. We have, you know, tens of thousands."_
Combined backlog likely exceeds 100k. The 90k figure is from Codem (SCA/runtime); tens of thousands additional from Suite CNAPP (cloud). No aggregated total was given.

## Example Entry (bad — do not do this)

**Backlog size**
They have a large vulnerability backlog consistent with a company of their size and tooling maturity. Based on using Codem and Suite CNAPP across a full-stack cloud environment, it's reasonable to estimate they have upwards of 100k vulnerabilities in total.

> The second version is fabrication masquerading as insight. The first is evidence.

---

## After the Extraction

Once the structured output is complete, add a short **"Gaps to Address"** section at the end. List any fields marked "Not mentioned" that are high-value for qualification — things worth asking in the next call. Keep it to the 3–5 most important gaps, not an exhaustive list of everything that wasn't said.