---
name: prompt-create-default
description: Use this skill when the user invokes "/prompt-create-default" or wants to create a new reusable prompt command saved as /prompt-use-default-local:[NAME].
disable-model-invocation: true
---

# Create Prompt Default

Create a new reusable prompt command. **All commands are created as local by default** (`/prompt-use-default-local:[NAME]`), meaning they are gitignored and not committed to the repository. Use `/prompt-make-shared-default` to promote one to shared.

User input: $ARGUMENTS

---

## Step 1 — Extract required information

From the user's input, identify:

### a) Command name (required)
- Will be invoked as `/prompt-use-default-local:[NAME]`
- Only lowercase letters (a-z), numbers (0-9), and hyphens (-) allowed
- No spaces, no special characters, no leading/trailing hyphens
- The user should provide just the `[NAME]` part — do NOT include the prefix in the name
- If the user provides a name that already includes `prompt-use-default-local:` or `prompt-use-default:`, strip the prefix and use only the `[NAME]` part
- If missing → ask the user
- If it contains invalid characters → suggest a corrected version and ask for confirmation

### b) Prompt template (required)
- The reusable text that will be executed when the command is used
- If missing → ask the user

### c) Variables (optional)
- Placeholders inside the prompt that will be filled at runtime
- The user may express them in any format: `[something]`, `{something}`, `<something>`, `{{something}}`, or described separately in natural language (e.g. "the variable is the PR link", "el argumento es el nombre del archivo")
- Infer variables from context. Normalize all of them to `{{variable_name}}` in the stored template, using descriptive names derived from context (not generic names like arg1, arg2 unless the user provides no better hint)
- If it is unclear whether something is a variable or literal text → STOP and ask the user

**If any required field is missing or ambiguous → STOP and ask the user. Do not proceed until you have clear, confirmed values.**

---

## Step 2 — Validate command name

- Pattern: `^[a-z0-9]+(-[a-z0-9]+)*$`
- If invalid → suggest a corrected kebab-case name and confirm with user before continuing

---

## Step 3 — Check for duplicates

Check if `.claude/skills/prompt-use-default-local--[NAME]/SKILL.md` already exists using the Read tool.

If it exists:
- Tell the user: "Ya existe un comando `/prompt-use-default-local:[NAME]`."
- Offer three options:
  - a) Sobreescribirlo
  - b) Usar un nombre diferente (pídele que lo indique)
  - c) Otra acción que el usuario sugiera
- Wait for the user's response before continuing

---

## Step 4 — Build the SKILL.md content for the new command

Construct the file content as follows. Replace all placeholders in brackets.

If there are NO variables, use this structure:

```
---
name: prompt-use-default-local:[NAME]
description: Reusable prompt command (local). Invoke when the user uses "/prompt-use-default-local:[NAME]".
disable-model-invocation: true
---

# Prompt Default (Local): [NAME]

## Prompt Template

[PROMPT TEMPLATE — as provided by the user, with no variable placeholders]

---

## Instructions

This prompt has no variables. Execute it directly.

### Show prompt before executing

Display the following to the user before proceeding:

---
**Prompt a ejecutar:**
[PROMPT TEMPLATE]
---

Wait for the user to confirm (or cancel/modify). If the user requests changes, apply them and show the prompt again.

### Execute

Execute the prompt exactly as shown above.

### Final summary

After completing, display:

---
**Resumen**
Comando: `/prompt-use-default-local:[NAME]`
Prompt ejecutado:
> [PROMPT TEMPLATE]
---
```

If there ARE variables, use this structure:

```
---
name: prompt-use-default-local:[NAME]
description: Reusable prompt command (local). Invoke when the user uses "/prompt-use-default-local:[NAME]". Variables: [COMMA-SEPARATED LIST OF {{variable_name}}].
disable-model-invocation: true
---

# Prompt Default (Local): [NAME]

## Prompt Template

[PROMPT TEMPLATE with {{variable_name}} placeholders]

---

## Variables

| Variable | Description |
|----------|-------------|
[One row per variable: | {{variable_name}} | What this variable represents |]

---

## Instructions

The user invoked this command with: $ARGUMENTS

### Step 1 — Resolve variables from $ARGUMENTS

From the user's input (natural language), infer the value for each variable:

[For each variable, one bullet: - `{{variable_name}}`: [what it represents] — extract from $ARGUMENTS]

**If any variable value is missing, ambiguous, or unclear → STOP and ask the user before proceeding.**

### Step 2 — Show interpolated prompt before executing

Substitute all variables into the template and display:

---
**Prompt a ejecutar:**
[Show the full prompt with all {{variable_name}} replaced by their resolved values]
---

Wait for the user to confirm (or cancel/modify). If the user requests changes, apply them and show again.

### Step 3 — Execute

Execute the filled-in prompt exactly as shown.

### Step 4 — Final summary

After completing, display:

---
**Resumen**
Comando: `/prompt-use-default-local:[NAME]`
Prompt ejecutado:
> [Filled prompt shown again]
---
```

---

## Step 5 — Write the file

Write the constructed content to:
`.claude/skills/prompt-use-default-local--[NAME]/SKILL.md`

(Note: the folder uses `--` as separator instead of `:` because Windows does not allow `:` in file paths. The skill name in the frontmatter still uses `:`.)

---

## Step 6 — Confirm to the user

Report success with:
- Command name: `/prompt-use-default-local:[NAME]`
- Type: **local** (gitignored, not committed to repo)
- Variables detected (if any): list them with descriptions
- Example invocation
- Tip: use `/prompt-make-shared-default [NAME]` to promote it to shared (committed to repo)
