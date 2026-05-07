# PM Agents & Skills

A shared library of AI skills and workflows for the D365 Field Service PM team. Team members publish skills they've built in the [Agency Cowork](https://github.com/ahsi-microsoft/agency-cowork) experience here so the whole team can adopt and build on them.

## What Lives Here

```
agents/PM/
├── README.md          ← You are here
└── skills/            ← One subfolder per skill
    └── <skill-name>/
        └── SKILL.md   ← Skill instructions the agent follows
```

Each skill is a self-contained markdown file that tells the AI agent what to do. Skills can reference local files, call MCP tools, or chain sub-skills — they're just instructions.

## How to Pull a Skill

1. `git pull` to get the latest skills from the team
2. Copy the skill folder you want into your local Agency Cowork `skills/` directory:

   ```powershell
   # From your Agency Cowork workspace
   Copy-Item -Recurse "path\to\docfx.fieldservice\agents\PM\skills\<skill-name>" "skills\"
   ```

3. Register it in your `AGENTS.md` skill table so the agent knows it exists:

   ```markdown
   | **skill-name** | `skills/skill-name/skills/skill-name/SKILL.md` | Brief trigger description |
   ```

4. Ask your agent to use it — or just describe what you want and it will route automatically.

## How to Publish a Skill

1. Create a branch: `git checkout -b pm/<your-alias>-<skill-name>`
2. Add your skill under `agents/PM/skills/<skill-name>/SKILL.md`
   - Use the [skill template](#skill-template) below
   - Include any supporting data files your skill references in the same folder
3. Commit and push: `git push origin pm/<your-alias>-<skill-name>`
4. Open a PR against `main` — no approvals required for the PM folder, but a brief description helps the team understand what the skill does

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
- **Data files are fine.** Skills can reference `.md` lookup tables, JSON configs, or CSV data in the same folder. Check `agents/release-tracker/` for an example.
- **Chain skills.** A skill can reference another skill's file path for multi-step workflows.
- **Test locally first.** Run the skill in your own Agency Cowork workspace before publishing.
