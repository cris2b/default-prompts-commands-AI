---
name: prompt-delete-default
description: Use this skill when the user invokes "/prompt-delete-default" or wants to delete or remove an existing /prompt-use-default-local:[NAME] or /prompt-use-default:[NAME] command.
disable-model-invocation: true
---

# Delete Prompt Default

Delete an existing prompt-use-default command. Works with both types:
- **Local**: `/prompt-use-default-local:[NAME]` → `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md`
- **Shared**: `/prompt-use-default:[NAME]` → `.claude/skills/prompt-use-default--[NAME]/SKILL.md`

User input: $ARGUMENTS

---

## Step 1 — Identify the command to delete

From $ARGUMENTS, extract the command name and determine its type.

**Detecting the type:**
- If the user provides the full name with prefix (`prompt-use-default-local:NAME` or `prompt-use-default:NAME`) → type is explicit
- If the user provides just `NAME` → check both paths:
  1. `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md` (local)
  2. `.claude/skills/prompt-use-default--[NAME]/SKILL.md` (shared)
  - If found in one → use that type
  - If found in both → ask the user which one they want to delete
  - If found in neither → inform the user and list available commands (Glob on both patterns, show `name:` from frontmatter)

**If command name is not provided → ask the user.**

---

## Step 2 — Show what will be deleted and ask for confirmation

Read the skill file and show the user:
- Command name: `/prompt-use-default-local:[NAME]` or `/prompt-use-default:[NAME]`
- Type: **local** or **shared**
- Prompt template (so the user can confirm it's the right one)
- Variables (if any)

Then ask: "¿Confirmas que quieres eliminar este comando? Esta acción no se puede deshacer."

**Wait for explicit confirmation before proceeding. If the user cancels → stop.**

---

## Step 3 — Delete the folder

Run via Bash (use the correct path based on type):
- Local: `Remove-Item -Recurse -Force ".claude/skills/prompt-use-default-local--[NAME]"`
- Shared: `Remove-Item -Recurse -Force ".claude/skills/prompt-use-default--[NAME]"`

---

## Step 4 — Confirm to the user

Report that the command has been deleted.
