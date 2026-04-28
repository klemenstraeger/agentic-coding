# Now its your time to code!


## Starter Prompt

Copy, tweak the bits in `<>`, paste into OpenCode (Plan mode — shift+tab):

```
I want to build <Horse Tinder> in ~2 hours.

Stack preference: <Vue 3 + Vite + UnoCSS, or whatever fits best — your call>.
Scope: MVP only. Cut anything that won't ship in the timebox.

Before writing code:
1. Ask me clarifying questions if anything is ambiguous.

```

Swap the project name for any idea below. Keep the rules — they're what makes the agent useful instead of chatty.


## Examples


### Quick Wins (~1h)

**CLI Game**
Snake, 2048, Wordle, or Minesweeper in the terminal.
→ Tests: from-scratch scoping, single-shot generation.

**Markdown → Slidev Deck**
Drop your meeting notes, get a presentation. Use the `slidev` skill.
→ Tests: skill invocation, structured output.

**Repo Stats Visualizer**
`git log` → interactive HTML chart (commits/author, hot files, churn over time).
→ Tests: data wrangling + frontend in one shot.

**Dotfiles Bootstrap**
Idempotent installer for your dev environment (brew, zsh, configs).
→ Tests: shell scripting, edge case handling.


### Real Tools You'll Keep Using (~1.5h)

**Git Worktree TUI**
Interactive terminal UI to create/switch/clean worktrees. Ink or Bubble Tea.
→ Tests: TUI libs, daily-driver value.

**Local API Mock Server**
Reads an OpenAPI spec → fake server with realistic data.
→ Tests: spec parsing, code generation.

**PR Review Helper**
Reads `git diff`, prints structured review notes to terminal.
→ Tests: code understanding, formatted output.

**Codebase Archaeologist**
Analyzes a repo + git history → generates a "story" of how it evolved as Markdown.
→ Tests: multi-step reasoning, file exploration.


### Stress Tests (full 2h)

**Resurrect a Dead Repo**
Pick an abandoned GitHub project. Modernize deps, add lint + tests + CI.
→ Tests: legacy navigation, bulk edits, long tool loops.

**Vue 2 → Vue 3 Migration**
Or CommonJS → ESM. Real codebase, not a toy.
→ Tests: multi-file refactor, regression catching, Plan mode discipline.

**Flat Repo → Monorepo**
Split into packages with Turborepo + pnpm workspaces. Use the `turborepo` and `pnpm` skills.
→ Tests: structural refactoring, skill chaining.

**Reverse-Engineer a File Format**
Hand it `.potx`, `.sketch`, `.fig` → ask for a parser with tests.
→ Tests: iterative discovery, hypothesis-driven exploration.

**Bug Hunt**
Pre-seed a repo with 10 bugs. Time the fix loop.
→ Tests: debugging, test-driven self-correction.


### Greenfield Builds

**Personal Site**
Portfolio with VitePress or Nuxt, deploy to Cloudflare Pages.
→ Tests: full-stack flow + deployment.

**Mini Linear / Mini Trello / Mini Excalidraw**
Pick one, scope hard, ship something polished.
→ Tests: scoping, design taste, knowing when to stop.

**Browser Extension**
"Hide YouTube Shorts", "GitHub PR file tree", "1-click meeting joiner".
→ Tests: small surface, real distribution path.


**Horse Tinder**
A fun one: a Tinder-like app for horse adoption. Swipe left/right on horses, see details, contact shelters. Use a public API for horse data or mock it.
→ Tests: API integration, image handling, playful UI.


### If you need LLM-Access

We've setup an virtual llm key for our aime litellm instance you can use. If you want to use it, come to us and we'll give you the details. 
