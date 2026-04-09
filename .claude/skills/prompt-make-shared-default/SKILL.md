---
name: prompt-make-shared-default
description: Use this skill when the user invokes "/prompt-make-shared-default" or wants to promote a local /prompt-use-default-local:[NAME] command to a shared /prompt-use-default:[NAME] command (committed to repo).
disable-model-invocation: true
---

# Make Prompt Default Shared

Convert a **local** `/prompt-use-default-local:[NAME]` command into a **shared** `/prompt-use-default:[NAME]` command (committed to the repository, available to the whole team).

User input: $ARGUMENTS

---

## Step 1 — Identify the command

From $ARGUMENTS, extract the command name (accept any of these formats: `NAME`, `prompt-use-default-local:NAME`, `/prompt-use-default-local:NAME`).

- If not provided → ask the user
- Check that `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md` exists
  - If it does not exist → inform the user. Also check if `.claude/skills/prompt-use-default--[NAME]/SKILL.md` exists — if so, tell them it is already shared. Otherwise list available local commands (Glob `.claude/skills/prompt-use-default-local--*/SKILL.md`, show `name:` from each frontmatter).

---

## Step 2 — Check for conflict

Check if `.claude/skills/prompt-use-default--[NAME]/SKILL.md` already exists.

If it does:
- Inform the user: "Ya existe un comando shared `/prompt-use-default:[NAME]`."
- Offer options: (a) sobreescribirlo, (b) cancelar
- Wait for response before continuing

---

## Step 3 — Read and update the skill content

Read `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md`.

Update the content:
- In the frontmatter: change `name: prompt-use-default-local:[NAME]` → `name: prompt-use-default:[NAME]`
- In the frontmatter `description:`: replace `prompt-use-default-local:[NAME]` → `prompt-use-default:[NAME]` and remove `(local)` note if present
- In the `# Prompt Default (Local):` heading: remove `(Local)` → `# Prompt Default: [NAME]`
- In all `Resumen` blocks and invocation references: replace `/prompt-use-default-local:[NAME]` → `/prompt-use-default:[NAME]`

---

## Step 4 — Write the new file and delete the old one

1. Write updated content to `.claude/skills/prompt-use-default--[NAME]/SKILL.md`
2. Delete the old folder via Bash: `Remove-Item -Recurse -Force ".claude/skills/prompt-use-default-local--[NAME]"`

---

## Step 5 — Confirm to the user

Report:
- `/prompt-use-default-local:[NAME]` → `/prompt-use-default:[NAME]`
- The command is now **shared** (committed to repo, disponible para el equipo)
- Tip: use `/prompt-make-local-default [NAME]` to reverse this
