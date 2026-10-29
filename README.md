# The Second Brain Stack

One brain for all your AI agents, on every device. It's a private GitHub repo of markdown files.
Claude Code, Codex, and any other agent you connect read it before they work and write to it when
they learn something worth keeping.

From the **AiEdge Summit 2026** workshop with Dan Klosterman.

## Workshop slides

- **Online:** https://edgeteamio.github.io/summit-workshop-resources-2026/slides/?deck=second-brain-stack
- **On your computer:** from this repo's folder, run `python3 -m http.server 8000` (Windows:
  `py -m http.server 8000`), then open
  http://localhost:8000/slides/?deck=second-brain-stack. Use the arrow keys to move; **N** shows
  the speaker notes, **G** shows every slide, and **F** goes full screen.

## Two pieces

| File | What it does |
|---|---|
| [SETUP-PROMPT.md](SETUP-PROMPT.md) | Copy the whole file (the copy button at the top right of the file on GitHub) and paste it into Claude Code or Codex. On your first device it creates your brain and interviews you, in about ten minutes. On every other device it connects the brain in two. |
| [BRAIN-INSTRUCTIONS.md](BRAIN-INSTRUCTIONS.md) | Teaches an agent to use the brain. The setup prompt installs it for Claude Code and Codex. Paste it into any other agent yourself. |

Then spend ten minutes on **[SAFETY.md](SAFETY.md)**: lock your GitHub account, give apps only
the brain, and add a check that blocks keys from being committed.

Plus my **[builder toolkit](RESOURCES.md)**: the model pickers, skills, secrets tools, and habits I
use every day.

**You need** a GitHub account, Claude Code or Codex, and git plus the GitHub CLI (`gh`), logged
in. [GITHUB-SETUP.md](GITHUB-SETUP.md) gets you there in five minutes. Do it before the workshop.
No terminal at all? Use the [browser path](BRAIN-INSTRUCTIONS.md#no-terminal).

## What's in a brain

```
brain/
├── README.md     the map: agents read this first
├── rules.md      rules every agent follows, in every project
├── me.md         who you are and how you like to work
├── people.md     who you work with
├── projects/     one file per project: status and decisions
├── notes/        everything else worth keeping
└── inbox/        new notes without a home yet. You sort these.
```

## How it stays in sync

Agents pull at the start of a task and push after every change, so a decision you make with
Claude on your laptop is there when Codex starts on your desktop. Every commit is labeled
`agent@device`, so `git log` shows who wrote what and from where, and anything can be undone.
If two devices edit the same note at once, the agent keeps both versions.

## Which agents can use it

- **Agents on your own devices with a shell** (Claude Code, Codex, Cursor's agent): full read and
  write. Run the setup prompt on each device.
- **Agents with GitHub tools but no shell** (a GitHub connector or MCP server): they can read the
  brain through the GitHub API, and write to it if the tool can edit files.
- **Personal agents:** Grok Bot reads the brain through its GitHub connection and writes by
  starting a cloud coding agent that commits for it (it's in beta). Meta Muse reads through its
  GitHub connector, and tells you exactly what to add when it can't write. Use the short version
  in [BRAIN-INSTRUCTIONS.md](BRAIN-INSTRUCTIONS.md) for these, and for Claude and ChatGPT
  projects. The slides walk through each one.
- **Cloud agents** that run in a sandbox: many only get access to the repo you start them on.
  To use the brain, they also need access to your brain repo, through the platform's repo
  settings or a [token limited to that one repo](SAFETY.md#3-if-an-app-wants-a-token-make-it-brain-only)
  and stored in the platform's secrets. Never put a token in the brain or in the instructions.
- **Chat apps with no file or GitHub access** can't use it.

## Stuck?

- **`gh: command not found` right after installing:** open a new terminal window.
- **Not logged in:** `gh auth login --hostname github.com --git-protocol https --web`, then
  `gh auth setup-git`.
- **Push fails with "Permission denied (publickey)", or hangs:** run
  `gh config set git_protocol https` and ask your agent to switch the brain's remote to
  `https://github.com/<you>/brain.git`.
- **In Codex, `gh auth status` says your token is invalid:** that's Codex's sandbox. Run the check
  in a normal terminal window.
- **The brain landed under the wrong GitHub account:** on GitHub, open the repo's Settings →
  Transfer ownership, or `gh auth switch` and run the setup prompt again with a new name.
- **No terminal at all:** use the [browser path](BRAIN-INSTRUCTIONS.md#no-terminal).
