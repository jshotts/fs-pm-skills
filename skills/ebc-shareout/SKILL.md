---
name: ebc-shareout
description: Generate and send EBC (Executive Briefing Center) shareout emails to the BIC Frontline team. Use this skill when the user asks to "write an EBC shareout", "draft a shareout email", "summarize an EBC session", or wants to share a customer briefing recap with the team. Triggers include "EBC", "shareout", "executive briefing", "customer briefing recap".
tools: Bash, Agent
---

# EBC Shareout

Generate a customer-facing summary email from an EBC session transcript and send it (or save a draft) to the BIC Frontline team.

## Overview

After an Executive Briefing Center session, the Microsoft account team writes a shareout email to `bicfrontlineallfte@microsoft.com` summarizing the customer, what was discussed, what resonated, and what follow-up actions are in motion. This skill generates that email from a meeting transcript and sends it as a draft via Outlook.

## Inputs

You will receive from the user:
- **Meeting transcript** (required) — Teams auto-transcript, notes, or a summary paste
- **Customer name** (required) — e.g., "Rollins", "Liebherr Holding"
- **Session date** (required) — the date the EBC was held
- **MS hosts** (required) — which Microsoft people hosted (e.g., "Jason Shotts and Bruce Rosado")
- **Deck SharePoint URL** (optional) — link to the EBC slide deck used
- **CC recipients** (optional) — additional MS stakeholders to CC beyond the default

If any required inputs are missing, ask the user before proceeding.

## Default recipients

- **To:** `bicfrontlineallfte@microsoft.com`
- **CC:** the MS hosts (other than the sender) plus any additional names provided by the user

## Subject line format

```
[EBC Shareout] {Customer Name}
```

## Step 1 — Parse the transcript

Read the transcript and extract:
- **Customer background**: industry, company size, current tech stack, ERP/CRM situation, who attended and their roles
- **Session topics**: the main themes discussed — group by topic, not by chronological order
- **Key quotes**: verbatim or near-verbatim statements from the customer that reveal pain points, vision, or priorities
- **What resonated**: features or capabilities that generated visible interest or engagement
- **Action items**: explicit follow-up commitments made during or after the session (owner + action)
- **Concerns or objections**: anything the customer pushed back on or flagged as a risk

## Step 2 — Draft the shareout email

Use this exact structure and formatting. Match the tone and style of the examples below.

```
To: BIC Frontline All FTE <bicfrontlineallfte@microsoft.com>
CC: {ms co-host and others}
Subject: [EBC Shareout] {Customer Name}

---

Hello team,

{MS host 1} and I had the opportunity to host an Executive Briefing Center (EBC) session on {date} with {Customer Name}, {one-sentence company description}. Below is a summary of the customer, what we discussed, what resonated, and what we're following up on.

BACKGROUND

{2-4 paragraph company overview covering: what they do, their scale, their current systems/CRM situation, who attended and their roles, and where they are in their Dynamics journey (pre-implementation, evaluating, going live, etc.)}

WHAT WE DISCUSSED

Deck we used: {SharePoint link or "Not provided"}

{One H3-style section per major topic discussed. Each section starts with a brief heading, then narrates what came up — including customer quotes where available. End each section with any action items that belong to it.}

{Repeat for each topic}
```

### Formatting rules

- **BACKGROUND** and **WHAT WE DISCUSSED** are written in ALL CAPS as section dividers — not markdown headers.
- Topic sections within WHAT WE DISCUSSED use emoji or a symbol prefix to create visual separation (e.g., `📋`, `🤖`, `🗓️`, `💬`). Match the topic to a relevant emoji.
- Customer quotes should appear inline, in double quotes, attributed to the person's name and title if known.
- Action items appear at the end of the relevant topic section, formatted as a flat list with "Action item:" prefix.
- Do not use markdown bold, headers, or bullet lists in the body — this is a plain-text email with light visual structure via caps and whitespace.
- Keep total length to 600-900 words for the body. Dense but readable.
- Write in the voice of the MS host — first person, collegial, analytical.

## Step 3 — Send or save as draft

After drafting:
1. Show the user the complete email for review.
2. Ask: **Send now** or **Save as draft**?
3. If sending: use the `send-email` skill to send to `bicfrontlineallfte@microsoft.com` with the specified CC list.
4. If draft: save `body.html` and `draft_meta.txt` to `shareouts/_draft/` using the template below, then confirm the path.

### Draft file format

`shareouts/_draft/draft_meta.txt`:
```
To=bicfrontlineallfte@microsoft.com
CC={comma-separated list}
Subject=[EBC Shareout] {Customer Name}
```

`shareouts/_draft/body.html`:
The full email body as plain HTML (no styling — just `<p>` and `<br>` tags).

## Examples

Refer to these prior shareouts for tone, structure, and depth:

- **Rollins** (`shareouts/[EBC Shareout] Rollins.txt`) — pest control company, pre-implementation, heavy discussion of scheduling and AI vision
- **Liebherr Holding** (`shareouts/[EBC Shareout] Liebherr Holding.txt`) — industrial manufacturing, pre-implementation, focus on field close-out and back-office burden

These files are in the original `EBC shareout skill/` folder at:
`C:\Users\jasonshotts\OneDrive - Microsoft\Documents\AI workspaces\EBC shareout skill\shareouts\`

Read them (use `-LiteralPath` due to brackets in filenames) to calibrate length, level of detail, and tone before drafting.

## Rules

- Always show the draft to the user before sending — never send without explicit confirmation.
- Do not include internal Microsoft strategy, roadmap commitments, or unreleased feature names in the shareout.
- CC only the MS people who attended — do not add customer contacts.
- If the transcript is sparse, note what is missing and ask the user to fill in gaps rather than fabricating details.
- Use `-LiteralPath` when reading example files with brackets in filenames.
