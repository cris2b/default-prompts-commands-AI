---
name: prompt-edit-default
description: Use this skill when the user invokes "/prompt-edit-default" or wants to edit, update, or modify an existing /prompt-use-default-local:[NAME] or /prompt-use-default:[NAME] command.
disable-model-invocation: true
---

# Edit Prompt Default

Edit an existing prompt-use-default command. Works with both types:
- **Local**: `/prompt-use-default-local:[NAME]` → stored in `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md`
- **Shared**: `/prompt-use-default:[NAME]` → stored in `.claude/skills/prompt-use-default--[NAME]/SKILL.md`

User input: $ARGUMENTS

---

## Step 1 — Identify the command to edit

From $ARGUMENTS, extract the command name and determine its type.

**Detecting the type:**
- If the user provides the full name with prefix (`prompt-use-default-local:NAME` or `prompt-use-default:NAME`) → type is explicit
- If the user provides just `NAME` → check both paths:
  1. `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md` (local)
  2. `.claude/skills/prompt-use-default--[NAME]/SKILL.md` (shared)
  - If found in one → use that type
  - If found in both → ask the user which one they want to edit
  - If found in neither → inform the user and list available commands (see below)

**If command name is not provided or ambiguous → ask the user.**

To list available commands, use Glob on:
- `.claude/skills/prompt-use-default-local--*/SKILL.md` (local)
- `.claude/skills/prompt-use-default--*/SKILL.md` (shared)

Show names extracted from each file's `name:` frontmatter field.

---

## Step 2 — Read and show the current skill

Read the skill file at the resolved path.

Show the user:
- Type: **local** or **shared**
- Current prompt template
- Current variables and their descriptions (if any)

---

## Step 3 — Identify what to change

From $ARGUMENTS or by asking the user, determine what to edit:

- **Prompt template**: new or modified text
- **Variables**: add, remove, rename, or change descriptions
- **Command name** (rename): provide a new name — this requires creating a new file and deleting the old one
- Any combination of the above

**Note:** Changing the type (local ↔ shared) is done with `/prompt-make-local-default` or `/prompt-make-shared-default`, not here.

**If the desired changes are unclear or ambiguous → STOP and ask the user before proceeding.**

---

## Step 4 — Validate changes

If the command name is being changed:
- Apply the same validation: `^[a-z0-9]+(-[a-z0-9]+)*$`
- If invalid → suggest a corrected name and confirm
- Check that the new name does not already exist in the same type path

For variable changes:
- Normalize all variable references in the updated template to `{{variable_name}}` format
- Update the variable table accordingly

---

## Step 5 — Rebuild the SKILL.md content

Reconstruct the full SKILL.md using the same template structure defined in `prompt-create-default`:
- Preserve the type (local or shared) — use `prompt-use-default-local:` or `prompt-use-default:` prefix accordingly in the frontmatter `name:` field and all references inside the file
- If no variables: use the no-variables template
- If variables exist: use the variables template

Apply all changes from Step 3.

---

## Step 6 — Write the updated file

Determine the correct output path based on type:
- Local: `.claude/skills/prompt-use-default-local--[NEW_NAME]/SKILL.md`
- Shared: `.claude/skills/prompt-use-default--[NEW_NAME]/SKILL.md`

Write the new content to that path.

If the command was renamed (new name ≠ old name):
- Write the new file first
- Then delete the old folder via Bash:
  - Local: `Remove-Item -Recurse -Force ".claude/skills/prompt-use-default-local--[OLD_NAME]"`
  - Shared: `Remove-Item -Recurse -Force ".claude/skills/prompt-use-default--[OLD_NAME]"`

---

## Step 7 — Confirm to the user

Report what changed:
- Command: `/prompt-use-default-local:[NAME]` or `/prompt-use-default:[NAME]`
- Old name → new name (if renamed)
- Prompt template (if changed): show the new version
- Variables (if changed): show the updated list
