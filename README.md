# FS PM Skills

A shared library of AI skills and workflows for the D365 Field Service PM team, built on top of [Agency Cowork](https://github.com/ahsi-microsoft/agency-cowork).

## Get Started — Set Up Agency Cowork First

These skills run inside **Agency Cowork**, a personal AI coworker that connects to your Microsoft 365 data. Before pulling anything from this repo, get Agency Cowork running on your machine.

> **[→ Set up Agency Cowork](https://github.com/ahsi-microsoft/agency-cowork)**

The app includes a **setup wizard** that handles everything in a few minutes — agent identity, persistent memory, M365 integrations (Outlook, Teams, Calendar, SharePoint, ADO), and the full skills library. You don't need to configure anything manually.

### What you get out of the box

Agency Cowork ships with 25+ pre-built skills. As a PM, the most immediately useful are:

| Skill | What it does |
|-------|-------------|
| **email-triage** | Categorizes your inbox, drafts responses, delivers a priority summary. Runs on a 30-minute schedule or on demand |
| **weekly-report** | Generates a weekly status from your ADO work items, emails, and Teams activity |
| **meeting-summary** | Produces a structured summary from a Teams meeting transcript or recap |
| **powerpoint** | Creates new decks from scratch or edits existing `.pptx` files |
| **word-doc** | Drafts and edits Word documents — specs, memos, briefs |
| **excel** | Creates and edits spreadsheets with formulas, charts, and batch edits |
| **ado** | Queries, creates, assigns, and comments on Azure DevOps work items |
| **teams** | Sends and reads Teams messages, manages chats and channels |
| **calendar** | Creates and manages Outlook calendar events, finds meeting times |
| **sharepoint** | Downloads and uploads files between your machine and SharePoint/OneDrive |
| **spec-kit** | Structured spec writing — problem statement, requirements, task breakdown |
| **visual-explainer** | Turns a concept or doc into a visual diagram or explainer image |
| **confluence** | Searches, creates, and edits Confluence pages |
| **oneplanner** | Manages Project for the Web schedules |
| **d365-expense** | Files Dynamics 365 expense reports from receipts |
| **task-scheduler** | Schedules recurring tasks — e.g., run email triage every morning at 8am |

To personalize your agent for your role and working style, just ask:
> *"Personalize my agent"*

This runs a guided interview that configures your agent's identity, communication style, and domain knowledge automatically.

---

## What Lives Here

Skills in this repo are **FS PM team-specific** — they go beyond what ships with Agency Cowork and are tailored to our workflows (release planning, EBC sessions, release notes, etc.).

```
fs-pm-skills/
├── README.md          ← You are here
└── skills/            ← One subfolder per skill
    └── <skill-name>/
        └── SKILL.md   ← Instructions the agent follows at runtime
```

Each skill is a self-contained markdown file. Skills can reference local data files, call MCP tools (Outlook, Teams, ADO, SharePoint), or chain into other skills.

## How to Pull a Skill

1. Clone this repo (one-time):
   ```powershell
   git clone https://github.com/jshotts/fs-pm-skills.git
   ```

2. To get the latest skills from the team:
   ```powershell
   cd fs-pm-skills && git pull
   ```

3. Copy the skill you want into your Agency Cowork `skills/` directory:
   ```powershell
   # Run from your Agency Cowork workspace root
   Copy-Item -Recurse "path\to\fs-pm-skills\skills\<skill-name>" "skills\"
   ```

4. Register it in your `AGENTS.md` skill table so the agent routes to it:
   ```markdown
   | **skill-name** | `skills/skill-name/SKILL.md` | Brief trigger description |
   ```

5. Ask your agent to use it — or just describe what you want and it will route automatically.

## How to Publish a Skill

1. Fork this repo and create a branch: `git checkout -b <your-alias>/<skill-name>`
2. Add your skill under `skills/<skill-name>/SKILL.md`
   - Use the [skill template](#skill-template) below
   - Include any supporting data files (lookup tables, JSON configs) in the same folder
3. Commit and push, then open a PR against `main`
4. Add a row to the [Skill Index](#skill-index) in your PR

## Skill Template

```markdown
---
name: your-skill-name
description: One sentence — what does this skill do?
---

# Skill Name

Brief description of what the skill does and when to use it.

## When to use

- "Example prompt that triggers this skill"
- "Another example prompt"

## Instructions

Step-by-step instructions the agent follows. Be explicit — the agent reads this
at runtime and follows it literally.

## Notes

Any caveats, dependencies on MCP tools, or data files this skill requires.
```

## Skill Index

| Skill | Author | Description |
|-------|--------|-------------|
| [ebc-shareout](skills/ebc-shareout/SKILL.md) | jshotts | Draft and send EBC session shareout emails to the BIC Frontline team |
| [release-plan](skills/release-plan/SKILL.md) | jshotts | Draft Business Value, Metadata, and Feature Detail for the Microsoft release planner |
| [release-notes](skills/release-notes/SKILL.md) | jshotts | Generate customer-facing FS/URS/FSM release notes from ADO work items and update version-history.md |

## Tips

- **Rename before customizing.** If you modify a bundled Agency Cowork skill, save it under a new name here (e.g., `weekly-report` → `pm-weekly-report`) so upstream updates don't overwrite your version.
- **Data files are fine.** Skills can reference `.md` lookup tables, JSON configs, or CSV data in the same folder. See `skills/release-notes/SKILL.md` for an example that reads station mapping tables.
- **Chain skills.** A skill can reference another skill's file path for multi-step workflows.
- **Test locally first.** Run the skill in your own Agency Cowork workspace before publishing.
