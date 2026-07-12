# CO_DE

**Your AI tools. One native workspace.**

Claude Code, Codex, opencode, Gemini (Antigravity), Cursor Agent, Droid —
side by side with your browser, terminals, and notes, in one structured
local **macOS** workspace: sessions and desks, a git worktree per agent,
worker state, reports, and live feeds in one place — instead of scattered
terminals with fragmented context.

**Now that workspace has a dedicated orchestrator seat — and you choose
who sits in it:**

- **A local model on your own server** (llama.cpp / Ollama) — coordination
  and supervision without spending a single API token — the only bill is
  your own GPU. This is the setup CO_DE is built around: *your local model
  runs the meeting; your subscriptions write the code.*
- **A subscription you already pay** — Codex or Claude via the bundled Pi
  runtime; no per-token bill, though it draws on that sub's usage limits.
- **Any model behind a LiteLLM / OpenAI-compatible server** — premium API,
  self-hosted, gateway. Your call.

The orchestrator observes and coordinates the agents already living in the
workspace; workers still execute through their real CLI runtimes.

> **Your providers. Your subscriptions. Your orchestrator. No new
> subscription required.**

---

### Before you decide — the honest part

- **Source is currently private.** One developer, not ready to maintain a
  community fork. This public repo mirrors DMG releases with checksums.
  Don't take my word on network behavior — run it under Little Snitch or
  `lsof -i`: the coordinator talks to `localhost`; your worker CLIs talk to
  their own vendors, exactly as they do without CO_DE. Tell me if you catch
  it doing anything else.
- **macOS arm64 only. The DMG is unsigned** — I don't pay Apple. Install is
  one command (below). If you're cautious, run it first in a separate macOS
  user account.
- **Beta. One developer. It has bugs.** If something breaks, open an issue.

---

## What makes it different

**1 · Coordination is off the meter — and not slow.**
Run the coordinator on a local model and orchestration bills zero per-token
and never touches the rate limits your workers need. And "local" isn't slow
here: MTP speculative decoding runs Qwen3.6-35B at 109 tok/s, 100% draft
acceptance. Coordination is dispatch and tracking, not deep reasoning — the
local brain keeps up.

**2 · Blocked workers ask a local brain, not your phone at 2am.**
A stuck worker doesn't sit silent and doesn't guess — it asks the
coordinator mid-task and gets the answer injected into its own terminal, and
continues. No human in the loop, no paid tokens spent on the answer.

**3 · Cross-repo dispatch.**
From repo A, tell the coordinator to work in repo B. It mounts the repo, the
task travels, workers spawn there — worktrees aren't a cage.

Below the fold: Claude Code + Codex as native first-class agents · local
coder CLIs (opencode / goose / droid / qwen) on your local model as cheap
triage before paid tokens touch a problem · worker reports come back
*rendered* in chat, not dumped · A|B adversarial planning at zero API tokens ·
worker CLIs resume their own sessions across restarts · **Telegram remote
(experimental)** — drive the orchestrator from your phone; still rough, gets
refined once the core loop is bullet-proof.

## Download

**macOS — Apple Silicon (13+):** [CO_DE.dmg](./CO_DE.dmg)

### Install (unsigned build)

```sh
# after dragging CO_DE into /Applications:
xattr -cr /Applications/CO_DE.app
```

Then open it. That clears the Gatekeeper quarantine flag on an unsigned app.

## Requirements

- macOS 13+ on Apple Silicon.
- At least one coding agent CLI on your `PATH` (Claude Code, Codex,
  opencode, Gemini CLI, Droid, Cursor Agent…). CO_DE auto-detects them.
- A coordinator brain — any one of: a **llama.cpp / Ollama server** running a
  local model; a **Codex/Claude subscription** (via the bundled Pi runtime);
  or a **LiteLLM / OpenAI-compatible server** pointing at any model you like.

## Built on

CO_DE's coordinator runs on **[Pi](https://pi.dev)** — Mario Zechner's
open-source (MIT) agent harness. CO_DE didn't reinvent the agent loop; it
wired a local model into a solid one and built the desk, the fleet roster,
and the cross-repo dispatch around it. Thanks, Mario.

## Feedback

Bugs and **feature requests** both go here:
**[open an issue](https://github.com/gelubodrug/co_de/issues/new/choose)**
(there's a picker — 🐛 Bug report / ✨ Feature request). One dev reads them
all.
