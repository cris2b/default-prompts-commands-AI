---
name: prompt-make-local-default
description: Use this skill when the user invokes "/prompt-make-local-default" or wants to convert a shared /prompt-use-default:[NAME] command into a local /prompt-use-default-local:[NAME] command (gitignored).
disable-model-invocation: true
---

# Make Prompt Default Local

Convert a **shared** `/prompt-use-default:[NAME]` command into a **local** `/prompt-use-default-local:[NAME]` command (gitignored, not committed to the repo).

User input: $ARGUMENTS

---

## Step 1 — Identify the command

From $ARGUMENTS, extract the command name (accept any of these formats: `NAME`, `prompt-use-default:NAME`, `/prompt-use-default:NAME`).

- If not provided → ask the user
- Check that `.claude/skills/prompt-use-default--[NAME]/SKILL.md` exists
  - If it does not exist → inform the user. Also check if `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md` exists — if so, tell them it is already local. Otherwise list available shared commands (Glob `.claude/skills/prompt-use-default--*/SKILL.md`, show `name:` from each frontmatter).

---

## Step 2 — Check for conflict

Check if `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md` already exists.

If it does:
- Inform the user: "Ya existe un comando local `/prompt-use-default-local:[NAME]`."
- Offer options: (a) sobreescribirlo, (b) cancelar
- Wait for response before continuing

---

## Step 3 — Read and update the skill content

Read `.claude/skills/prompt-use-default--[NAME]/SKILL.md`.

Update the content:
- In the frontmatter: change `name: prompt-use-default:[NAME]` → `name: prompt-use-default-local:[NAME]`
- In the frontmatter `description:`: replace `prompt-use-default:[NAME]` → `prompt-use-default-local:[NAME]` and add `(local)` note if not present
- In the `# Prompt Default:` heading: add `(Local)` → `# Prompt Default (Local): [NAME]`
- In all `Resumen` blocks and invocation references: replace `/prompt-use-default:[NAME]` → `/prompt-use-default-local:[NAME]`

---

## Step 4 — Write the new file and delete the old one

1. Write updated content to `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md`
2. Delete the old folder via Bash: `Remove-Item -Recurse -Force ".claude/skills/prompt-use-default--[NAME]"`

---

## Step 5 — Confirm to the user

Report:
- `/prompt-use-default:[NAME]` → `/prompt-use-default-local:[NAME]`
- The command is now **local** (gitignored)
- Tip: use `/prompt-make-shared-default [NAME]` to reverse this
