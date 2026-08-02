# CO_DE

**Your AI tools. One native workspace.**

Run the coding tools you already use in one macOS workspace, while a coordinator sits above them, assigns work, keeps context, and reports the result.

Not another AI chat.

Not another API bill.

Just the subscriptions and CLIs you already have, working together.

![CO_DE workspace running Claude Code, Codex, Cursor Agent, Pi, Gemini, local llama.cpp and remote orchestration](./media/co_de-agent-workspace.png)

---

# Why I built it

Every provider keeps shipping another great model.

- Claude Code
- Codex
- Gemini CLI
- Cursor Agent
- opencode
- Kimi Code

The problem isn't the models anymore.

The problem is us.

We constantly jump between terminals, copy conversations from one tool to another, rewrite prompts, wait for answers, summarize logs, decide who should do the next step, then repeat the process.

CO_DE exists to coordinate that work.

It doesn't replace the tools.

It lets each one do what it already does well.

---

# The idea

I wanted an orchestrator with a real desktop UI that sits above the provider CLIs already running on my machine.

Not another orchestration platform that replaces everything with expensive headless APIs.

I already pay for subscriptions.

I wanted to use them.

Workers stay exactly where they belong—in their own terminals, using their own authentication, exactly as their creators intended.

The coordinator simply decides who should do what, keeps track of the work, and reports back.

---

# Why local models changed everything

Originally I assumed the coordinator would also have to be a cloud model.

That stopped making sense.

Modern local models are now perfectly capable of orchestrating work.

They read a messy human request.

Turn it into an execution plan.

Coordinate multiple workers.

Keep track of context.

Collect results.

Return a concise report.

That's an ideal orchestration workload.

Cloud models spend their tokens solving the actual engineering problems.

The local model spends almost nothing deciding who should solve them.

That saves both money and context.

---

# What CO_DE does

- Uses your existing subscriptions instead of replacing them.
- Runs the real provider CLIs in their own terminals.
- Supports local models through **llama.cpp** or **Ollama**.
- Supports OpenAI-compatible endpoints through **LiteLLM**.
- Gives every worker its own Git worktree.
- Coordinates work across multiple repositories.
- Lets blocked workers ask the coordinator instead of waiting for you.
- Returns structured reports instead of dumping terminal output.
- Includes experimental Telegram remote control.

![The official Claude Code, Codex and Kimi Code extensions running inside CO_DE](./media/co_de-native-extensions.png)

---

# Workers stay real

CO_DE never pretends to be Claude Code.

Or Codex.

Or Cursor Agent.

Or Gemini CLI.

It launches the real tools.

The official CLIs.

The official VS Code extensions.

Their own authentication.

Their own terminals.

Their own capabilities.

The orchestrator simply coordinates them.

---

# Why this approach

Different models are good at different things.

Some write code faster.

Some review better.

Some plan better.

Some are cheaper.

Some are local.

CO_DE lets you mix all of them inside one workflow instead of forcing every task through the same model.

**The right model.**

**For the right job.**

---

# Download

**macOS (Apple Silicon, macOS 13+)**

[CO_DE.dmg](./CO_DE.dmg)

After copying the application:

```bash
xattr -cr /Applications/CO_DE.app
```

The application is currently unsigned, so this removes the Gatekeeper quarantine attribute.

## Requirements

You need:

- At least one supported coding CLI installed on your machine.
- A coordinator model:
  - llama.cpp
  - Ollama
  - Codex
  - Claude (through Pi)
  - Any OpenAI-compatible endpoint

CO_DE itself does not require a subscription.

---

# Built on Pi

The orchestration engine runs on **Pi**, Mario Zechner's open-source (MIT) agent framework.

I didn't reinvent the agent loop.

I built a native orchestration workspace around it: multi-agent coordination, worktree management, routing, reporting, desktop UI, and cross-repository execution.

Huge thanks to Mario.

---

# Roadmap

Current priorities:

- Better orchestration strategies
- More provider integrations
- Better reporting
- Better desktop UX
- More autonomous workflows

Everything is being developed in the open.

---

# Feedback

Bug reports, ideas and feature requests are welcome.

Open an issue:

https://github.com/gelubodrug/co_de/issues/new/choose
