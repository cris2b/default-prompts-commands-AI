# Prompt Commands for Claude Code

A set of Claude Code skills that let you create and manage **reusable prompt commands** — slash commands that store a prompt template and execute it on demand, with optional variables.

---

## Installation

Copy the `.claude/skills/` folder from this repo into your project's `.claude/skills/` directory.

```
your-project/
└── .claude/
    └── skills/
        ├── prompt-create-default/
        ├── prompt-edit-default/
        ├── prompt-delete-default/
        ├── prompt-list-default/
        ├── prompt-make-shared-default/
        └── prompt-make-local-default/
```

Also copy the `.gitignore` entries if you want local commands to stay gitignored:

```gitignore
.claude/skills/prompt-use-default-local--*/
```

That's it. Open Claude Code in your project and the commands are available immediately.

---

## Concepts

Commands come in two types:

| Type | Slash command | Stored in | Committed? |
|------|--------------|-----------|------------|
| **Local** | `/prompt-use-default-local:[name]` | `.claude/skills/prompt-use-default-local--[name]/` | No (gitignored) |
| **Shared** | `/prompt-use-default:[name]` | `.claude/skills/prompt-use-default--[name]/` | Yes |

- **Local** — personal prompts, drafts, or sensitive templates you don't want in the repo.
- **Shared** — team prompts committed to the repo so everyone has them.

---

## Commands

### `/prompt-create-default`
Create a new prompt command.

```
/prompt-create-default
```

Claude will ask for:
1. A name (e.g. `code-review`)
2. The prompt template
3. Any variables (optional)

The command is created as **local** by default.

---

### `/prompt-use-default-local:[name]` / `/prompt-use-default:[name]`
Execute a saved prompt command.

```
/prompt-use-default-local:code-review
/prompt-use-default:fix-bug src/auth.ts line 42
```

If the prompt has variables, Claude resolves them from your input, shows you the filled prompt, and waits for confirmation before executing.

---

### `/prompt-list-default`
List all saved commands.

```
/prompt-list-default          # all
/prompt-list-default local    # only local
/prompt-list-default shared   # only shared
```

---

### `/prompt-edit-default`
Edit an existing command — change the template, variables, or rename it.

```
/prompt-edit-default code-review
```

---

### `/prompt-delete-default`
Delete a command. Asks for confirmation first.

```
/prompt-delete-default code-review
```

---

### `/prompt-make-shared-default`
Promote a local command to shared (moves it out of `.gitignore`).

```
/prompt-make-shared-default code-review
```

---

### `/prompt-make-local-default`
Move a shared command back to local (gitignored again).

```
/prompt-make-local-default code-review
```

---

## Example flows

### Create a simple prompt (no variables)

```
You: /prompt-create-default
     Name: standup
     Prompt: Summarize what I worked on today based on my git commits and write a standup message.

Claude: ✓ Created /prompt-use-default-local:standup
```

Later:

```
You: /prompt-use-default-local:standup

Claude: Prompt to execute:
        > Summarize what I worked on today based on my git commits and write a standup message.
        Confirm? → yes

Claude: [executes the prompt]
```

---

### Create a prompt with variables

```
You: /prompt-create-default
     Name: fix-bug
     Prompt: Review the file {{file}} and fix the bug described as: {{description}}

Claude: ✓ Created /prompt-use-default-local:fix-bug
        Variables: {{file}}, {{description}}
```

Later:

```
You: /prompt-use-default-local:fix-bug  src/auth.ts  "tokens are not being invalidated on logout"

Claude: Prompt to execute:
        > Review the file src/auth.ts and fix the bug described as: tokens are not being invalidated on logout
        Confirm? → yes

Claude: [executes the prompt]
```

---

### Share a prompt with your team

```
You: /prompt-make-shared-default fix-bug

Claude: ✓ /prompt-use-default-local:fix-bug → /prompt-use-default:fix-bug
        Now committed to the repo.
```

Teammates can now use `/prompt-use-default:fix-bug` after pulling.

---

## File structure after creating commands

```
.claude/skills/
├── prompt-create-default/          # management skill
├── prompt-use-default-local--standup/   # local command (gitignored)
└── prompt-use-default--fix-bug/         # shared command (committed)
```
