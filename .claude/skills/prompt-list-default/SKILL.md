---
name: prompt-list-default
description: Use this skill when the user invokes "/prompt-list-default" or wants to see, list, or browse prompt-use-default commands. Optional argument: "local" (only local), "shared" (only shared). Default: show all, separated by type.
disable-model-invocation: true
---

# List Prompt Defaults

List prompt-use-default commands, separated by type.

User input: $ARGUMENTS

---

## Step 1 — Determine filter from $ARGUMENTS

- If $ARGUMENTS contains `local` → show only local commands
- If $ARGUMENTS contains `shared` → show only shared commands
- If $ARGUMENTS is empty or unrecognized → show all, separated into two sections

---

## Step 2 — Find commands

Depending on the filter:

- **Local** (always fetch unless filter is `shared`):
  Glob: `.claude/skills/prompt-use-default-local--*/SKILL.md`

- **Shared** (always fetch unless filter is `local`):
  Glob: `.claude/skills/prompt-use-default--*/SKILL.md`

---

## Step 3 — Read each skill file

For each file found, read it and extract:
- `name:` from frontmatter → the full command name
- Content under `## Prompt Template` → first line or short excerpt
- Content under `## Variables` → variable names from the table (if present)

---

## Step 4 — Display the list

### If filter is `local` or showing all (local section):

```
🔒 Local (gitignored, no commitados al repo)

  /prompt-use-default-local:[NAME1]
  Prompt: [excerpt]
  Variables: {{var1}}, {{var2}}   |   Sin variables

  /prompt-use-default-local:[NAME2]
  ...
```

### If filter is `shared` or showing all (shared section):

```
🌐 Shared (commitados al repo, disponibles para el equipo)

  /prompt-use-default:[NAME1]
  Prompt: [excerpt]
  Variables: {{var1}}   |   Sin variables

  /prompt-use-default:[NAME2]
  ...
```

### If no commands found in a section → show:
`  (ninguno)`

### Footer (always):

```
─────────────────────────────────────────
Total: [N local] local · [N shared] shared

Crear:        /prompt-create-default
Editar:       /prompt-edit-default [nombre]
Borrar:       /prompt-delete-default [nombre]
→ Shared:     /prompt-make-shared-default [nombre]
→ Local:      /prompt-make-local-default [nombre]
```

If a filter was applied, omit the section and count for the hidden type.
