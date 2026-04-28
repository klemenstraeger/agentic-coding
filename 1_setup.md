# Installation & Setup

## Prerequisites

- A terminal with true-color support (any modern Terminal.app, iTerm2, Windows Terminal, or your IDE's integrated terminal)
- GitHub Copilot license — acquired via the IT-Shop
  - **Heads up:** some Copilot models require a **Pro+** tier. With Individual you'll be limited to a smaller set — pick a working one in `/models` after auth.


## Install

Pick whichever fits your environment. All three install the same `opencode` binary.

| Platform | Command |
|---|---|
| macOS / Linux (recommended) | `curl -fsSL https://opencode.ai/install \| bash` |
| macOS (Homebrew)            | `brew install opencode`                          |
| Windows (Chocolatey)        | `choco install opencode`                         |
| Windows (Scoop)             | `scoop install opencode`                         |
| Any (npm / pnpm / bun)      | `npm i -g opencode-ai`                           |

> **Windows:** WSL is the smoothest path. Native PowerShell works but a few keybinds and shell features behave differently.

Verify the binary works:

```bash
opencode --version
```


## Connect GitHub Copilot

Inside the opencode TUI, run:

```
/connect
```

Pick **GitHub Copilot** from the list. opencode prints an 8-character device code (e.g. `8F43-6FCF`). Open <https://github.com/login/device> in your browser, paste the code, authorize.

> No browser opens automatically — this is GitHub's device flow. The TUI just sits and waits until you complete the step in the browser.

Verify auth was stored:

```bash
opencode auth list
```

Credentials live in `~/.local/share/opencode/auth.json` if you ever need to reset or inspect them.


## Pick a Model

opencode does **not** auto-select a model after `/connect`. Run:

```
/models
```

…and pick one your Copilot tier supports. If you see a "selected model has an issue" error later, this is almost always the cause — switch to a different model.




## Optional Tips

- **`.env` files** are loaded automatically at startup. Provider keys in `.env` will *override* what `/connect` set — check it first if auth behaves oddly.
- **Upgrade**: `opencode upgrade` (curl install) or `opencode upgrade --method npm` if you installed via a Node package manager.
- **Autoupdate** is on by default — disable in `opencode.json` with `"autoupdate": false` or `"notify"` if you'd rather get a prompt.
