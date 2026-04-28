# Basic Usage


## Prefixes

What you type at the start of an input determines how opencode interprets it.

| Prefix | Meaning |
|---|---|
| `/`    | Run a slash command (built-in or custom) |
| `@`    | Mention a file (adds its contents to context) **or** delegate to a subagent (e.g. `@general find this function`) |
| `!`    | Run a shell command; output is added to the conversation |


## Modes

opencode runs in an *agent* mode. The two built-ins differ in what they're allowed to touch.

| Mode    | How to activate          | What it does |
|---------|--------------------------|--------------|
| Plan    | `shift+tab` (cycle)      | Read-only analysis. Edits and shell calls require approval. Use to draft an approach before any changes. |
| Build   | `shift+tab` (cycle)      | Default. All tools enabled — reads, writes, runs commands. |
| Custom  | `tab` / `shift+tab`      | Any custom agents you define show up in the cycle. |


## Keybinds

The leader key is `ctrl+x`. Most session actions are `ctrl+x` followed by another key.

### Sessions & history

| Action               | Key             |
|----------------------|-----------------|
| New session          | `ctrl+x n`      |
| List / switch sessions | `ctrl+x l`    |
| Compact (summarize) context | `ctrl+x c` |
| Undo last message + file changes | `ctrl+x u` |
| Redo                 | `ctrl+x r`      |
| Interrupt running agent | `escape`     |

### Agents & models

| Action                           | Key             |
|----------------------------------|-----------------|
| Cycle to next / previous agent   | `tab` / `shift+tab` |
| Open agent list                  | `ctrl+x a`      |
| Open model list                  | `ctrl+x m`      |
| Switch to most recently used model | `f2`          |

### App-level

| Action                  | Key                  |
|-------------------------|----------------------|
| Command palette         | `ctrl+p`             |
| Help dialog             | `ctrl+x h`           |
| Open `$EDITOR` to compose | `ctrl+x e`         |
| Themes                  | `ctrl+x t`           |
| Exit                    | `ctrl+c` / `ctrl+d` / `ctrl+x q` |


## Slash Commands

Type `/` in the input box. The command palette (`ctrl+p`) lists everything available, including any custom commands you've added.

| Command     | Aliases                  | Description |
|-------------|--------------------------|-------------|
| `/help`     | —                        | Show the help dialog |
| `/init`     | —                        | Generate or update `AGENTS.md` (project rules) from your codebase |
| `/new`      | `/clear`                 | Start a fresh session |
| `/sessions` | `/resume`, `/continue`   | List and switch between past sessions |
| `/compact`  | `/summarize`             | Summarize the current context to free up tokens |
| `/undo`     | —                        | Undo the last message and revert file changes (Git repo required) |
| `/redo`     | —                        | Redo the previously undone message |
| `/models`   | —                        | Browse and select the model |
| `/exit`     | `/quit`, `/q`            | Exit opencode |


## Config Files

opencode reads two separate config files. Both can live globally or per project, and lower levels merge on top of higher ones (project overrides global).

| File             | Locations                                                                        | Contains |
|------------------|----------------------------------------------------------------------------------|----------|
| `opencode.json`  | Global: `~/.config/opencode/opencode.json` <br> Project: `./opencode.json` or `./.opencode/opencode.json` | Model, providers, agents, MCP servers, permissions, custom commands |
| `tui.json`       | Global: `~/.config/opencode/tui.json` <br> Project: `./tui.json` or `./.opencode/tui.json` | Theme, keybinds, scroll behaviour |

Useful top-level keys in `opencode.json`:

- `model` — default model (e.g. `"anthropic/claude-sonnet-4-5"`)
- `provider` — API keys, timeouts (supports `{env:VAR_NAME}` substitution)
- `agent` — define or override agents
- `command` — define custom slash commands inline
- `mcp` — register MCP servers
- `instructions` — extra files (paths or globs) appended to the system prompt
- `permission` — `edit` / `bash` set to `"ask"`, `"allow"`, or `"deny"`


## Extending opencode

### Custom Commands

Drop a markdown file in `.opencode/commands/<name>.md` (project) or `~/.config/opencode/commands/<name>.md` (global). The filename becomes the command — `test.md` → `/test`.

```markdown
---
description: Run tests with coverage and explain failures
agent: build
model: anthropic/claude-sonnet-4-5
---

Run the full test suite with coverage. For each failing test,
show the failure and suggest a fix. Focus on $ARGUMENTS.
```

| Frontmatter   | Required | Purpose |
|---------------|----------|---------|
| `description` | yes      | Shown in the command palette |
| `agent`       | no       | Run the command as a specific agent (`build`, `plan`, custom) |
| `model`       | no       | Override the model for this command |

Inside the body: `$ARGUMENTS` expands to everything the user typed after the command; `$1`, `$2`, … pick individual positional args.


### Agents & Skills

Custom agents and skills both live in `.opencode/`. Agents are full alternative system prompts you can switch into; skills are reusable instruction bundles loaded on demand.

| Type   | Location                                       |
|--------|------------------------------------------------|
| Agent  | `.opencode/agents/<name>.md`                   |
| Skill  | `.opencode/skills/<name>/SKILL.md`             |

**Agent frontmatter:** `description` (required), `mode` (`primary` or `subagent`), `model`, `permission` (`edit`/`bash`: `allow`/`ask`/`deny`), `tools`.

**Skill frontmatter:** `name` (must match the directory, lowercase + hyphens) and `description` (1–1024 chars).

For project-wide rules every agent should follow, run `/init` to generate an `AGENTS.md` in the repo root.


### MCP Servers

Register MCP servers under the `mcp` key in `opencode.json`. Two types:

```jsonc
{
  "mcp": {
    "files": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-everything"],
      "enabled": true
    },
    "linear": {
      "type": "remote",
      "url": "https://mcp.linear.app/sse",
      "headers": { "Authorization": "Bearer {env:LINEAR_TOKEN}" },
      "enabled": true
    }
  }
}
```

Helpful CLI commands:

- `opencode mcp list` — show configured servers and their status
- `opencode mcp auth <name>` — run the OAuth flow for a remote server
