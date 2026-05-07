---
name: release-notes
description: Generate customer-facing release notes for Microsoft Dynamics 365 Field Service. Use this skill when the user asks to generate, draft, or produce release notes for a Field Service release, version, sprint, or milestone. Also use it when the user references a release record identifier and wants to know what shipped.
tools: Bash, Agent
---

# Release Notes — Dynamics 365 Field Service

Produce accurate, customer-facing release notes for a given release by querying all associated work items from Azure DevOps. Do not ask the user to supply work item lists — retrieve everything via the ADO MCP server.

## Inputs

You will receive from the user:
- A **release record identifier** (release number, version string, sprint name, or milestone name)

Everything else must be retrieved via the ADO MCP server for the `dynamicscrm` organization.

Additionally, retrieve or ask the user for the following **version metadata** (check the ADO release record first):
- FS version number (e.g., `8.8.144.38`)
- URS version number (e.g., `3.12.3370.1`)
- Resource Scheduling Controls version (e.g., `1.2.99.260833`)
- Dataverse version (e.g., `4.0.3370.1`)

You also need **release calendar data** to update the station rollout schedule. The authoritative source is the internal release calendar at `eng.ms/docs/experiences-devices/business-and-industry-copilot/bic-bis-ai-erp/aierp-frontline/dynamics-365-field-service/dynamics-365-field-service-team-documentation/releases/calendar` — this requires Microsoft authentication and cannot be accessed directly. If the user has not already provided it, ask them to supply the current calendar as a screenshot or copy-paste before proceeding with Step 5.

---

## Step 1 — Locate the Release Record and Fetch Work Items

1. Use the ADO MCP server to find the release record matching the identifier.
2. Retrieve all work items linked to that release. This may require:
   - Querying child/linked work items on the release record
   - Running a WIQL query filtered by the release iteration or tag
   - Following parent/child link chains to surface all relevant items
3. For each work item, retrieve full details: title, description, acceptance criteria, tags, area path, work item type, state, and linked child items.

If the release record cannot be found, report this clearly and ask for clarification before proceeding.

---

## Step 2 — Internal vs. External Triage

For each work item, decide whether it warrants inclusion in external, customer-facing release notes.

**Exclude if any of the following are true:**
- Task, Sub-Task, Test Case, or internal engineering item with no user-visible outcome
- Infrastructure, CI/CD pipeline, internal tooling, or developer environment work
- Duplicate, won't-fix, or closed-as-invalid
- Description is entirely technical with no discernible customer impact
- Addresses only internal test harnesses, monitoring, or telemetry
- Tagged with `internal`, `eng-health`, `tech-debt`, `infra`, `no-release-notes`, or similar
- Code refactor with no behavioral change visible to an end user
- Security fix that should not be disclosed (check for `security-do-not-disclose` tags)

**Include if any of the following are true:**
- New customer-facing feature, capability, or configuration option
- Bug fix that customers have experienced or reported
- Performance, reliability, or usability improvement a customer would notice
- UI, workflow, or data model change affecting how customers use the product
- Integration behavior change with external systems (connector updates, API changes)
- Tagged with `release-notes`, `customer-impact`, or similar

When in doubt, err on the side of inclusion and flag for human review (handled in Step 6, not in the published doc).

---

## Step 3 — Classify by Product Area

Map each included item to one of the three product areas based on ADO area path, tags, or content:

| Product Area | Signals | Description |
|---|---|---|
| **URS** | `UniversalResourceScheduling`, `URS`, `Schedule Board`, `Resource`, `Booking` | Scheduling engine, resource management, schedule board, booking rules |
| **FS** | `FieldService`, `FS`, `Work Order`, `Asset`, `Agreement`, `Inspection`, `Inventory` | Core Field Service — work orders, assets, agreements, inventory, inspections |
| **FSM** | `Mobile`, `FSM`, `FieldServiceMobile` | The Field Service mobile app for frontline workers |

If a work item spans multiple areas, place it in the most relevant area and note the overlap for the PR review comment.

---

## Step 4 — Draft Release Notes

Use this format exactly. Do not include any triage summaries, ADO IDs, flagged items, or internal review notes in this document — those go in the PR comment in Step 6.

```
# Release Notes — [Release Number/Name]
Release Date: [date if known, otherwise leave blank]

---

## Universal Resource Scheduling (URS)

### New Features
- **[Feature Title]**: [One to two sentence description of what changed and why it matters to the customer. Plain language, no internal jargon.]

### Bug Fixes
- **[Bug Title]**: [One sentence describing the issue resolved and the customer impact.]

---

## Field Service (FS)

### New Features
- ...

### Bug Fixes
- ...

---

## Field Service Mobile (FSM)

### New Features
- ...

### Bug Fixes
- ...
```

Omit any subsection entirely if it has no items (do not write "None").

---

## Tone and Style

- Write for **customers and partners** — assume a Dynamics 365 administrator or power user.
- Lead with the **customer benefit**, not the technical implementation.
- Use active voice. Avoid "it was updated to..."
- Do not reference internal ADO IDs, sprint names, or engineering team names in the published notes.
- Keep each entry to 1–2 sentences. Link to documentation where available.

---

## Step 5 — Update Version History Docs

### How to read the release calendar

The release calendar uses the following color coding:

| Color | Meaning |
|---|---|
| Dark green | Applied — publisher-driven update complete for this station |
| Bright green | Released — version available opt-in, publisher-driven still pending |
| Black / no color | Not yet reached this station |
| Light blue | Test |
| Yellow | Engineering |
| Red | Blocked |
| Gray | Cancelled |
| Purple | Skipped |
| White / light | Not started |

When a station cell contains two dates, the **bottom date is the publisher-driven automatic update** — this is what goes in the **Scheduled date** column in the docs.

### Station to region mapping

| Calendar column | Doc station label | Regions |
|---|---|---|
| S1 / FRE | Station 1 | First Release |
| S2 | Station 2 | South America, Canada, India, France, South Africa, Germany, Switzerland, Norway, Korea |
| S3 | Station 3 | UAE, Japan, Asia Pacific, UK; USG is a separate sub-row |
| S4 / EUR | Station 4 | Europe |
| S5 / NAM | Station 5 | North America, Oceania |
| S6 / SPL | Station 6 (sub-row) | Dedicated Scale Groups |
| S6 / Sov | Station 6 | Government Community Cloud, DoD, China |
| S7 / 6CY | Station 7 | 6CY region |
| S8 / 6PD | Station 8 | 6PD region |

### Logic for updating the release schedule table

For each station row in the docs:

| Calendar color | Action |
|---|---|
| **Dark green (Applied)** | Publisher-driven complete → set as **Current version**, clear Next version and Scheduled date |
| **Bright green (Released)** | Publisher-driven pending → keep previous version as **Current version**, set new version as **Next version**, bottom date = **Scheduled date** |
| **Black (not yet reached)** | Set new version as **Next version**, bottom date = **Scheduled date** (if shown); if no date shown yet, omit |
| **Blocked / Cancelled / Skipped** | Do not advance the version for that station; note in the PR comment if relevant |

### Step 5a — Update `ce/field-service/version-history.md`

1. Update the release schedule table using the logic above with FS version numbers.
2. Insert a new version section immediately after the schedule table (before the previous version section) using this format:

```markdown
## 8.8.X.Y
(Includes Universal Resource Scheduling version [3.12.X.Y](/dynamics365/field-service/field-service-version-history-resource-scheduling#anchor) and Resource Scheduling controls version 1.2.X.Y)

- **New Features**
    - [items from FS New Features in the drafted release notes]

- **Fixes & Improvements**
    - [items from FS Bug Fixes in the drafted release notes]
```

- Anchor ID convention: version number with dots removed (e.g., `8.8.144.38` → `#8814438`)
- If the release is a hotfix on a prior version, add: `This release is a hotfix on Field Service version [X.X.X.X](#anchor).`
- If the release contains no FS changes, replace the body with: `- No updates were made to Dynamics 365 Field Service in this release.`
- If the release contains only security improvements, use: `- This release contains security improvements.`

### Step 5b — Update `ce/shared/urs/urs-bug-fixes.md`

1. Update the release schedule table using the same logic with URS version numbers.
2. Insert a new version section immediately after the schedule table using this format:

```markdown
## 3.12.X.Y

**Resource Scheduling Controls:** 1.2.X.Y

**Dataverse:** 4.0.X.Y

- [items from URS section of the drafted release notes — flat bullet list, no subheadings]
```

- URS uses a **flat bullet list** — do not use New Features / Fixes & Improvements subheadings.
- If no URS changes: `- No updates were made to Universal Resource Scheduling in this release.`
- If only security improvements: `- This release contains security improvements.`

---

## Step 6 — Create PR and Post Review Materials as Comments

### 6a — Create the PR

Before creating the PR, create and check out a new branch following this naming convention:

```
<git-username>-release-notes-<release>[,<release>...]
```

- `<git-username>` — the current git user (retrieve with `git config user.name` or from the ADO identity)
- `<release>` — the release short name(s) from the release record (e.g., `FS3.3`, `FS2.3HF1`); use commas with no spaces to separate multiple releases

Examples:
- `jshotts-release-notes-FS3.3`
- `jshotts-release-notes-FS3.3,FS2.3HF1`

Create the PR from this branch to `main`. The PR title and body must be clean and customer-neutral — no ADO IDs, internal sprint names, or triage details.

Add the following as **required reviewers** on every PR:
- `jshotts`
- `Hongyan2014`
- `puneet-singh1`

Use `gh pr create --reviewer jshotts,Hongyan2014,puneet-singh1` or equivalent.

PR body format:
```
## Release notes — [FS version]

Updates version history and URS bug fix docs for the [release name] release.

### Files changed
- `ce/field-service/version-history.md`
- `ce/shared/urs/urs-bug-fixes.md`
```

### 6b — Post triage summary and full work item list as a PR comment

Immediately after creating the PR, post a comment with the full triage details. This must not appear anywhere in the published docs.

```markdown
## Release Notes Triage Summary — [FS version]

**Total work items reviewed:** X
**Included:** X
**Excluded:** X
**Flagged for human review:** X

---

### Included Work Items
| ADO ID | Title | Area | Type |
|---|---|---|---|
| [12345](https://dev.azure.com/dynamicscrm/...) | Title | FS / URS / FSM | Feature / Bug |

---

### Excluded Work Items
| ADO ID | Title | Reason for Exclusion |
|---|---|---|
| [12346](https://dev.azure.com/dynamicscrm/...) | Title | Internal engineering task |

---

### Flagged for Review
| ADO ID | Title | Reason |
|---|---|---|
| [12347](https://dev.azure.com/dynamicscrm/...) | Title | Unclear customer impact |

---

### Ambiguous Area Classifications
| ADO ID | Title | Classified As | Note |
|---|---|---|---|
| [12348](https://dev.azure.com/dynamicscrm/...) | Title | FS | Overlaps with URS |
```

Omit any section that has no items.

---

## Output

Return:
1. The fully drafted release notes document (clean, customer-facing — no ADO IDs or internal notes)
2. Confirmation that `ce/field-service/version-history.md` has been updated
3. Confirmation that `ce/shared/urs/urs-bug-fixes.md` has been updated
4. The PR URL
5. Confirmation that the triage summary and full work item links have been posted as a PR comment