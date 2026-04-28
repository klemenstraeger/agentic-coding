# Advanced Usage

You've got the basics. Here's how to make opencode safer, faster, and yours.


## Permissions & Safety

Every tool call resolves to one of three values: `"allow"` (run silently), `"ask"` (prompt you), `"deny"` (block). Configure them under the `permission` key in `opencode.json`.

```jsonc
{
  "permission": {
    "edit": "ask",
    "webfetch": "allow",
    "external_directory": "ask",
    "bash": {
      "*":            "ask",
      "git *":        "allow",
      "git push *":   "deny",
      "rm *":         "deny",
      "npm *":        "allow"
    }
  }
}
```

**Pattern matching:** `*` = any characters, `?` = one character. The **last matching rule wins**, so put the catch-all `"*"` first and specific overrides after.

**Per-agent overrides** in agent frontmatter are merged on top of the global config:

```yaml
---
mode: subagent
permission:
  edit: deny
  bash: deny
---
```

**Built-in guardrails worth knowing:**

| Guardrail | What it does |
|---|---|
| `doom_loop`              | Defaults to `ask` when the same tool call repeats 3× with identical input — kills runaway loops |
| `external_directory`     | Defaults to `ask` for any path outside the project root |
| `.env` denial            | `read` denies `.env` and `.env.*` by default (`.env.example` is allowed) |
| Plan mode                | When in doubt, `shift+tab` into Plan — all `edit` and `bash` flip to `ask` automatically |


## Subagents & Parallel Work

Subagents are specialized helpers a primary agent can hand work off to. The primary agent reads each subagent's `description` and decides whether to delegate; you can also invoke one manually with `@name`.

**Built-in subagents:**

| Name      | When to use |
|-----------|-------------|
| `general` | Multi-step research or execution that's its own self-contained unit; can read and write files |
| `explore` | Fast, read-only codebase search — the right pick for "where is X defined?" type questions |

Each `@mention` spawns a **child session**. The primary agent gets the result back when the child finishes. You can dive into a child and come back:

| Action                          | Key       |
|---------------------------------|-----------|
| Enter first child session       | `ctrl+x ↓`|
| Cycle to next / previous sibling | `→` / `←` |
| Return to parent                | `↑`       |

**Custom subagent example** (`.opencode/agents/code-reviewer.md`):

```markdown
---
description: Reviews code for bugs, security, and performance. Read-only.
mode: subagent
model: anthropic/claude-sonnet-4-5
temperature: 0.1
permission:
  edit: deny
  bash: deny
---

You are a code reviewer. For each change, flag bugs, security issues, and
performance concerns. Do not modify files — return findings as a checklist.
```

The `description` is the most important field — it's what the primary agent reads to decide whether to auto-delegate to this subagent.

> **Parallel execution:** primary agents can issue multiple `task` calls — the docs imply these run in parallel but don't specify the mechanics. In practice it works for "explore A, B, and C simultaneously" type prompts.


## Hooks & Plugins

Plugins are TS/JS modules that run in the opencode process and can hook into lifecycle events. Two ways to install:

1. **Local file** — drop a `.ts` file in `.opencode/plugins/`. Auto-loaded, no registration.
2. **npm package** — list it in `opencode.json`:

```jsonc
{
  "plugin": ["opencode-notify", "@my-org/internal-plugin"]
}
```

If your local plugin needs npm deps, add a `.opencode/package.json` with `dependencies` — opencode runs `bun install` at startup.

**Minimal plugin skeleton:**

```ts
// .opencode/plugins/sanitize-bash.ts
import type { Plugin } from "@opencode-ai/plugin"

export const SanitizeBash: Plugin = async ({ project, client, $ }) => ({
  "tool.execute.before": async (input, output) => {
    if (input.tool === "bash") {
      // mutate output.args.command before it runs
      output.args.command = output.args.command.replace(/--no-verify/g, "")
    }
  },
})
```

The factory runs once at startup; the returned object maps event names to handlers.

**Most useful events** (full list at [opencode.ai/docs/plugins](https://opencode.ai/docs/plugins)):

| Category    | Events |
|-------------|--------|
| Tool        | `tool.execute.before`, `tool.execute.after` |
| Session     | `session.created`, `session.idle`, `session.compacted`, `session.error` |
| Message     | `message.updated`, `message.part.updated` |
| File        | `file.edited`, `file.watcher.updated` |
| Permission  | `permission.asked`, `permission.replied` |
| Command     | `command.executed` |


## Ecosystem

Community resources worth bookmarking:

- **[awesome-opencode](https://github.com/awesome-opencode/awesome-opencode)** — curated index of plugins, themes, custom agents, and official SDKs. Start here when you want to extend opencode before writing anything yourself.
- **[octto](https://github.com/vtemian/octto)** — replaces terminal Q&A with a browser UI offering ~14 input types (radio buttons, sliders, side-by-side diffs). Useful when planning sessions involve many tradeoff decisions.
- **[opencode-notify](https://github.com/kdcokenny/opencode-notify)** — native OS notifications when a session finishes, errors out, or is blocked on a permission prompt. Pair it with long-running tasks so you can switch apps without polling the terminal.
- **[opencode-worktree](https://github.com/kdcokenny/opencode-worktree)** — gives the agent `worktree_create` / `worktree_delete` tools that spin up isolated git worktrees with their own opencode instance. Great for letting the AI experiment on a branch without touching your main working tree.
