# Builder toolkit

The resources I actually use, plus a few I'd hand any builder. Links checked October 2026.

## The 10-minute tour

1. **Pick the model.** [Artificial Analysis Model Recommender](https://artificialanalysis.ai/models/recommend).
   Rate intelligence, speed, and cost from "not considered" to "critical", add requirements like
   open weights or long context, and get a ranked shortlist you can share as a link or PDF. Free.
   One thing to know: "cost" here is what it costs to run their benchmark, so a model that burns a
   lot of tokens scores worse even if its per-token price looks cheap.
2. **Check it against reality.** [Coding Agents benchmark](https://artificialanalysis.ai/agents/coding-agents)
   shows cost and time per task with models running inside real coding agents.
   [OpenRouter Rankings](https://openrouter.ai/rankings) shows what people actually use, by tokens.
   When the benchmarks and the usage disagree, find out why before you pick.
3. **Give every agent the same skills.** [skills.sh](https://skills.sh) is a directory of skills
   plus a CLI that installs them into Claude Code, Codex, and dozens of other agents at once:
   `npx skills add coreyhaines31/marketingskills`. It works across tools because of the open
   [Agent Skills](https://agentskills.io) format.
4. **Get a second opinion from a different model.** Have Codex review what Claude built (below).
   Two models rarely make the same mistake.
5. **Read the idea behind the brain.** [Karpathy's "LLM Wiki"](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f):
   raw sources go in, the agent maintains a wiki, and an index file tells it where everything is.
   Same shape as the brain you built today.

## Pick a model

| Resource | What it's for |
|---|---|
| [Model Recommender](https://artificialanalysis.ai/models/recommend) | A shortlist for your job, weighing intelligence, speed, and cost |
| [Coding Agents benchmark](https://artificialanalysis.ai/agents/coding-agents) | How models do inside real coding agents: cost per task, time per task |
| [Arena](https://arena.ai/leaderboard) | Rankings from human votes. This is LMArena, renamed in January. |
| [OpenRouter Rankings](https://openrouter.ai/rankings) | Which models people actually use, by tokens processed |
| [Vercel AI Gateway](https://vercel.com/ai-gateway) | One API key for hundreds of models, with fallbacks and spend limits |

## Skills

Install any of these with `npx skills add <owner/repo>`.

| Skill | What it does |
|---|---|
| [Impeccable](https://skills.sh/pbakaus/impeccable) (`pbakaus/impeccable`) | Critiques and fixes a UI: hierarchy, spacing, type, color. I run it on every site. |
| [Hallmark](https://skills.sh/nutlope/hallmark) (`nutlope/hallmark`) | Keeps new pages from looking AI-generated |
| [Taste](https://skills.sh/leonxlnx/taste-skill) (`leonxlnx/taste-skill`) | Rules for frontend that doesn't look like a template |
| [Emil Kowalski's design skill](https://skills.sh/emilkowalski/skill) (`emilkowalski/skill`) | Animation and polish decisions, from a designer who sweats them |
| [Make interfaces feel better](https://skills.sh/jakubkrehel/make-interfaces-feel-better) (`jakubkrehel/make-interfaces-feel-better`) | The small details: shadows, hover states, optical alignment |
| [No AI slop](https://skills.sh/petergyang/no-ai-slop) (`petergyang/no-ai-slop`) | The same idea for writing: edits drafts until they sound like a person |
| [Marketing skills](https://skills.sh/coreyhaines31/marketingskills) (`coreyhaines31/marketingskills`) | Copywriting, copy editing, landing pages, and more, from Corey Haines |

## How I run agents

**Codex reviews what Claude builds.** It's the habit I use most. Pipe a diff in, keep Codex
read-only, and ask it to find reasons to block:

```bash
git diff main | codex exec --sandbox read-only "Review this diff like you're trying to block it. Real bugs only, with file and line."
```

From inside Claude Code you can just say "have Codex review this diff," and it'll run Codex for you.

**Agents get secrets from 1Password, never from chat or the brain.** I keep the secrets agents
need in their own vault, and they read them at runtime with the
[1Password CLI](https://www.1password.dev/cli):

```bash
op read "op://Agents/OpenAI/credential"
```

On your own login, an agent can read anything you can. To actually fence agents in, give them a
1Password service account that only has access to that one vault.

**A guardrail that blocks dangerous commands.** [CC Safety Net](https://github.com/kenryu42/cc-safety-net)
is a Claude Code plugin that stops risky shell commands before they run. It blocked my own agent
twice while I built this workshop.

## Secrets and agents

The rule: agents use secrets without seeing them. A key you paste into a chat sits in that app's
history, and an agent that can read a key can be tricked into sending it somewhere. Start at the
top of this list and work down as the stakes rise. To lock down the brain itself, see
[SAFETY.md](SAFETY.md).

| Resource | What it's for |
|---|---|
| [1Password CLI](https://www.1password.dev/cli) | `op run --env-file .env -- <command>` swaps the `op://` references in a `.env` file for the real values, for that one command only. Their [AI access guide](https://www.1password.dev/get-started/secure-ai-access) adds `.env` files that never touch the disk (Mac and Linux) and a Claude Code plugin. |
| [Infisical](https://infisical.com/docs/cli/overview) | A secrets manager for a team. `infisical run -- <command>` loads a project's secrets into that one command, the same way `op run` does. Open source (MIT). Free on their cloud for up to 5 users and machine accounts in total, or host it yourself. |
| [Doppler](https://docs.doppler.com/docs/cli) | The hosted-only alternative to Infisical: `doppler run -- <command>`. Free for up to 3 users, then $8 a month per extra user. |
| [HashiCorp Vault](https://developer.hashicorp.com/vault) | The secrets server many large companies run on their own machines. It can create short-lived keys that expire on their own. Free to self-host, but under the Business Source License since 2023, so it isn't open source. There's no `run --` command: [Vault Agent](https://developer.hashicorp.com/vault/docs/agent-and-proxy/agent/process-supervisor) starts a program with its secrets loaded. [OpenBao](https://openbao.org) is the open-source fork (MPL 2.0), run under the Linux Foundation, with a `bao` command in place of `vault`. |
| [Claude Code settings](https://code.claude.com/docs/en/settings) | Stop Claude Code from reading `.env` files: add `"deny": ["Read(./.env)", "Read(./.env.*)"]` under `permissions` in `~/.claude/settings.json` |
| [TruffleHog](https://github.com/trufflesecurity/trufflehog) | Scans a repo and its whole history for keys, then asks each service which keys still work: `trufflehog git file://. --results=verified,unknown`. Open source (AGPL-3.0), with new releases most weeks. [Gitleaks](https://github.com/gitleaks/gitleaks) now gets security patches only. GitHub's free secret scanning only covers public repos. |
| [The lethal trifecta](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) | Simon Willison on why an agent with private data, content anyone can write, and a way to send data out can be tricked into leaking. Read it before you give one agent all three. |
| [OWASP Top 10 for Agentic Applications](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) | The ten biggest risks in agent apps, with fixes for each |

## Other ways to build a second brain

Every second brain has four parts: context files (what it knows), a sync engine (where it lives),
interfaces (where you use it), and instructions (when agents read and write it). Most tools bundle
all four. The brain from today keeps them separate, so any agent can use it and you can swap any
part later.

| Kind of tool | Examples | What it bundles |
|---|---|---|
| All-in-one workspaces | [Notion](https://www.notion.com), Coda | Files, sync and app in one. Agents reach it through an API or MCP, not as files. |
| Local markdown | [Obsidian](https://obsidian.md) | Only the app, over plain files you own. Sync it with Obsidian Sync, iCloud, or git. |
| Outliners | Tana, Logseq, Capacities | Mostly all-in-one. Logseq's new version stores notes in a database instead of markdown. |
| Everyday notes | Apple Notes, OneNote, Google Keep, Evernote | All-in-one, and hard for agents to reach |
| AI-native notes | Gemini Notebook (called NotebookLM until July 2026), Mem, Reflect | All-in-one and closed |
| Built-in memory | ChatGPT memory, Claude memory | All-in-one, and only that app can read it |
| Memory layers | [Mem0](https://mem0.ai), [Supermemory](https://supermemory.ai), [Cognee](https://www.cognee.ai) | Any agent can call them through an API or MCP, but your memory lives in their database, not in files you can read and edit |

Popular ways to organize one:

| Structure | What it is |
|---|---|
| [PARA](https://fortelabs.com/blog/para/) | Projects, Areas, Resources, Archives, sorted by how soon you'll act. Tiago Forte's method and the most common default. |
| [Zettelkasten](https://zettelkasten.de/introduction/) | Small notes, one idea each, linked to each other. Popular with writers and researchers. |
| [Johnny.Decimal](https://johnnydecimal.com) | Numbered areas and categories, like `15.52`, never more than two levels deep |
| [Linking Your Thinking](https://www.linkingyourthinking.com) | Nick Milo's maps of content: index notes that link related notes, instead of deep folders |
| [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) | The agent-era pattern: one index file the agent reads first, a few folders, and a log. Today's brain follows it. |

## Your brain, next steps

| Resource | What it's for |
|---|---|
| [Obsidian](https://obsidian.md) | Open your brain folder as a vault to browse and edit it by hand |
| [qmd](https://github.com/tobi/qmd) | Local search over markdown, for when your brain gets too big to skim. Works with Claude Code. |
| [AGENTS.md](https://agents.md) | The instructions file most agents read. Claude Code reads it too when a repo has no CLAUDE.md. |
| [Claude Code memory docs](https://code.claude.com/docs/en/memory) | How CLAUDE.md, imports, and rules files work |
| [Codex docs](https://learn.chatgpt.com/docs) | AGENTS.md, skills, and config for Codex |

## Go deeper

Ideas for after the workshop, from the "Build on your brain" slide.

| Idea | Where to start |
|---|---|
| **Pipe in every meeting.** A recording assistant pulls transcripts from every recorder you use and writes the decisions to your brain. | [Zoom's API](https://developers.zoom.us/docs/api/) and the [Google Meet API](https://developers.google.com/workspace/meet/api/guides/overview) both hand you transcripts. Write decisions and action items to the brain, not whole transcripts, and record only with everyone's consent. |
| **Build your own MCP server.** Turn your own data into tools that Claude, ChatGPT, Codex, and Cursor can all call. | The [Model Context Protocol docs](https://modelcontextprotocol.io/docs/getting-started/intro), then [MCP in Claude Code](https://code.claude.com/docs/en/mcp). Put auth on it before anything else can reach it. |
| **Let an agent tend the brain.** A weekly agent sorts `inbox/`, merges duplicates, and flags notes that went stale. | Claude Code [scheduled tasks](https://code.claude.com/docs/en/scheduled-tasks) run in the cloud on a repo you pick. Set one up with `/schedule`. |
| **Share a team brain.** The same setup, in one repo the whole team writes to. | Start from the same files in a private repo under your team's GitHub organization. Keep `me.md` and personal notes in your own brain. |

## Learn and stay current

| Resource | What it's for |
|---|---|
| [Simon Willison](https://simonwillison.net) | The best single feed on coding agents. Start with his [Agentic Engineering Patterns](https://simonwillison.net/guides/agentic-engineering-patterns/) guide. |
| [Latent Space](https://www.latent.space) | Podcast and newsletter for AI engineers. The AINews daily recap lives here now. |
| [Anthropic Academy](https://anthropic.skilljar.com) | Free courses: Claude Code in Action, Agent Skills, subagents, MCP |
