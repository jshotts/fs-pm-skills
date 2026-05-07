---
name: release-plan
description: Draft release plan content for Dynamics 365 Field Service features. Use this skill when the user asks to write, draft, or create a release plan, business value, metadata, or feature detail statement for a product feature. Triggers include "release plan", "write a release plan", "business value", "feature detail", "metadata statement", or any request to prepare content for the Microsoft release planner tool.
tools: Agent
---

# Release Plan — Dynamics 365 Field Service

Draft the three sections required by the Microsoft release planner tool for a given feature: **Business Value**, **Metadata**, and **Feature Detail**.

## Inputs

You will receive from the user:
- A **feature name or description**, and optionally a technical brief or spec document

Before drafting, gather context from available sources in this priority order:
1. Search QMD memory for any existing specs, design docs, or prior decisions about the feature
2. Search ADO (dynamicscrm org) for linked work items, acceptance criteria, and feature descriptions
3. Search Teams/email via WorkIQ for recent discussions about the feature
4. Use what you know from CLAUDE.md domain context (Subcontractor Agent, Asset Maintenance, etc.)

If the user provides a technical brief or spec document, read it carefully before drafting. Technical briefs often contain scope boundaries (non-goals), open design questions, and constraints that directly affect what the release plan should and should not claim.

Do not ask the user to supply spec content if you can retrieve it. Only ask for clarification if critical information is missing after searching.

---

## Section 1 — Business Value

### Audience
Business decision makers who want to understand business impact, ROI, and why they should care.

### Purpose
Published before the feature detail in the release planner. Articulates why a customer should invest in this feature and how it transforms their business.

### Rules
- Write directly to the user ("you")
- Informal, casual, but authoritative tone — use contractions, spell out abbreviations
- Present tense ("bots can use" not "bots will be able to use")
- Be concise but precise
- Answer: Who is this for? What value does it add? What does the customer save (time, money, resources)?
- Do NOT use dashes, colons, or hash/pound symbols
- Do NOT cite industry statistics or analyst figures unless sourced — use customer-grounded language instead
- Lead with the problem the customer actually faces, then introduce the feature as the solution
- If the feature removes a failed alternative (e.g., portals, manual processes), name it explicitly — this sharpens the value story
- If pricing is non-seat-based (consumption, etc.), call it out — it is a key differentiator for seasonal or high-churn scenarios

### Format
Two short paragraphs:
- Paragraph 1: The problem the customer faces, what the feature does, and who it serves
- Paragraph 2: How it transforms the business — cost model, control, efficiency, or competitive edge

### Example
> Reach up to 100 million contacts or leads and send up to 300 million messages per month with Dynamics 365 real-time marketing customer journey orchestration.
>
> This added capacity empowers you to deliver personalized experiences at scale and delight customers in new ways. Additional interactions will help you grow your business, whether you're increasing your customer base in new markets, reaching additional geographies, promoting new products, or expanding your prospective customer pipeline to reach higher sales targets.

---

## Section 2 — Metadata

### Audience
Business decision makers and technical roles — used by search engines to surface the feature.

### Purpose
"Hidden" content that powers search discoverability. Appears in search engine results as the page description.

### Rules
- Minimum 100, maximum 160 characters including spaces
- Full sentences, not keyword lists
- Include important words not already in the title
- Do NOT repeat the feature name verbatim
- Do NOT use any of these characters: & < > ' * @ ` [ ] ^ : © { } $ — – = ! / \ # - ( ) % . + ? " ® ; ~ ™ |
- Think: what would a customer type into a search engine to find this?

### Format
One sentence, 100–160 characters, no special characters, written as a search snippet.

---

## Section 3 — Feature Detail

### Audience
Business decision makers and technical roles — the main body of the release plan, published on the live site.

### Purpose
Provides the technical aspects of a feature. Describes what it does, how it works, and how to enable or disable it.

### Rules
- Write directly to the user ("you")
- Informal, casual, but authoritative — use contractions, spell out abbreviations
- Present tense throughout
- Be concise but precise; use white space (separate sentences into individual paragraphs where helpful)
- Link to relevant docs using Markdown syntax
- Call out explicitly whether the feature is automatically enabled or requires manual enablement (and if so, the steps)
- Do NOT use dashes, colons, or hash/pound symbols
- Remind the user to test any relative links after drafting (relative links do not include http:// and must be tested manually)
- Do NOT claim a feature supports "all" field types, entities, or configurations unless explicitly confirmed — use hedged language ("supported field types", "supported entities") when scope is not fully defined
- Do NOT use "Form Fill" to describe the review experience for AI-extracted updates — v3 replaces Form Fill with a custom review surface; use "agent update review queue" or equivalent neutral language
- Do NOT imply admins configure which entities the agent targets if entities are fixed — only describe what admins actually configure (forms, profiles, flags)
- When describing authorization models (e.g., Bookable Resource allow-listing), explain the mechanism: admins enable the resource record, the booking creates the authorization automatically — this is a key differentiator from manual per-record allow-lists

### Format
**Paragraph 1 — What the feature does**
Technical overview: how it works, what it processes, what it produces. Include the authorization or access model if relevant.

**Paragraph 2 — Configurability and extensibility**
What admins configure, what the feature adapts to (custom fields, forms, profiles). Be precise about scope — do not overstate coverage.

**Paragraph 3 — Enablement and setup**
State whether the feature is automatically enabled. If not, provide clear setup steps. Include the docs link here.

**Optional:** Bullet list of key capabilities if the feature has multiple distinct functions worth calling out.

### Relative Link Testing Instructions
After drafting, remind the user to test any relative links:
1. Copy the relative link
2. Type `https://learn.microsoft.com` in the browser bar
3. Paste the relative link after it (for example, `https://learn.microsoft.com/dynamics365/field-service/subcontractor-agent`)

---

## Workflow

1. **Gather context** — search QMD memory, ADO, and WorkIQ for spec details, acceptance criteria, and recent discussions about the feature. If a technical brief is provided, read it fully before drafting — pay attention to non-goals, open design questions, and constraints.
2. **Draft Title** — value-grounded verb phrase, no abbreviations, reads as a benefit statement (e.g., "Delegate subcontractor work updates to the Field Service Email Agent")
3. **Draft Business Value** — two paragraphs: lead with the problem, then the transformation
4. **Draft Metadata** — one sentence, 100–160 characters, no special characters, written as a search snippet
5. **Draft Feature Detail** — three paragraphs (what it does / configurability / setup) plus optional capability bullets and docs link
6. **Present all four elements** to the user clearly labeled, ready to copy into the release planner tool
7. **Remind the user** to test any relative links included in the Feature Detail

---

## Output Format

Present output in this structure, clearly labeled and ready to copy:

---

**TITLE**

[Value-grounded verb phrase, no abbreviations]

---

**BUSINESS VALUE**

[Two paragraphs — problem first, then transformation]

---

**METADATA**

[One sentence, 100–160 chars]
Character count: [N]

---

**FEATURE DETAIL**

[Paragraph 1 — what the feature does, how it works, authorization model if relevant]

[Paragraph 2 — configurability: forms, profiles, custom fields, scope caveats]

[Paragraph 3 — enablement and setup steps, docs link]

[Optional capability bullets]

---

> Reminder: Test any relative links in the Feature Detail by prepending `https://learn.microsoft.com` to the path in your browser before submitting.

---

## Field Service Domain Context

When drafting for known workstreams, draw on this context:

| Workstream | Key facts |
|---|---|
| Field Service Email Agent (v3) | Replaces Work Ingestion Agent v2 (Power Automate-based); Copilot Studio agent + Dataverse Custom APIs + AI Builder prompts; processes inbound subcontractor emails, extracts structured updates, routes to work orders and bookings; HITL review via agent update review queue (NOT Form Fill — Form Fill is retired in v3); Bookable Resource records must have email coordination enabled; authorization is booking-based (BR booked to WO = authorized to update); custom fields supported via form-driven ingestion profiles (supported field types only — do not claim all types); consumption-based pricing; no D365 license required for subcontractors; Wave 2 2026 target GA; design partners include Constellation, Programmed, GI-DE, City FM, Lee Company |
| Asset Maintenance | Maintenance plans, Service Meter Units (SMUs), automated work order generation; Wave 1 2026 GA |
| Universal Resource Scheduling (URS) | Scheduling engine, resource management, schedule board, booking rules |
| Field Service Mobile (FSM) | Frontline worker mobile app |

Target customer segments: facilities management (GWS, SBM), utilities, telecom. Subcontractor volume is 27–75% of field service work globally.
