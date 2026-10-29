# Keep your brain safe

Your brain holds who you are, your projects, and the people you work with, and every agent you
connect can read it. The setup prompt covers the basics: the repo is private (it checks), `.env`
files are ignored, and every agent is told never to write a password, key or token, to commit only
the files it wrote, and to treat brain notes as information, not orders.

This page covers what the setup prompt can't do for you. It takes about ten minutes.

## 1. Lock your GitHub account

The brain is only as safe as the account it lives in.

- Turn on two-factor authentication, ideally with a passkey or an authenticator app:
  https://github.com/settings/security
- Keep the brain under your personal account, not a work one.

## 2. Give each app only the brain

When Claude, ChatGPT, Grok Bot, Meta Muse, or any other app asks for GitHub access and offers the
choice, pick **Only select repositories**, then your brain. "All repositories" gives that app every
private repo you have.

Every few months, check what has access and remove what you don't use:

- Apps: https://github.com/settings/installations
- Authorized apps, including the GitHub CLI: https://github.com/settings/applications

## 3. If an app wants a token, make it brain-only

Some cloud agents and custom connectors ask for a GitHub token instead. Make one that can only
touch the brain:

1. Open https://github.com/settings/personal-access-tokens/new
2. Name it after the app, like `brain-grok`, and pick an expiration (30 or 90 days).
3. Repository access: **Only select repositories**, then your brain.
4. Permissions: add **Contents** with **Read and write**. Leave everything else off.
5. Paste it only into the app's secrets or connector settings. Never paste it into a chat message,
   the brain, or a clone URL like `https://<token>@github.com/...`.

Make one token per app, so you can revoke one without breaking the others.

## 4. Keep secrets out of the brain and out of chats

Passwords, API keys, tokens, recovery codes, account numbers, and client secrets never go in the
brain. The repo is private, but it's copied to every device, read by every agent you connect, and
git keeps every version forever.

Write down where a secret lives instead. "The OpenAI key is in 1Password, in the Agents vault" is
a fine note.

The same goes for chats: a key you paste into a chat sits in that app's history. Agents should use
secrets without seeing them, for example through the 1Password CLI, which hands the key to the one
command that needs it. [Secrets and agents](RESOURCES.md#secrets-and-agents) lists the tools.

## 5. Optional: block keys before they're committed

The instructions tell agents never to write a key. This adds a check that works even when an agent
doesn't listen: a small git hook in the brain that stops any commit when a file looks like it holds
an AWS, GitHub, OpenAI, Anthropic, Stripe, Slack, or Google key, or a private key.

It runs for agents that commit from your own devices, like Claude Code and Codex. Apps that write
through GitHub's website or API, like Grok Bot's cloud agent, skip it.

Paste this into Claude Code or Codex on each device where your brain lives. It takes a minute:

````text
Add a secret check to my shared brain, so no commit can include a key or token. Work inside the
brain folder from my brain instructions.

1. Sync the brain first.
2. If `.githooks/pre-commit` isn't there yet, create it with exactly the script below, using LF
   line endings. Add the line `.githooks/* text eol=lf` to `.gitattributes` (create the file if
   it's missing). On Mac or Linux, run `chmod +x .githooks/pre-commit`. Add this line to the
   "My additions" section of `rules.md`: "If the brain's secret check blocks a commit, take the
   secret out and tell me. Never use `--no-verify`."
3. Run `git config core.hooksPath .githooks`. Every device needs this, even when the script is
   already there.
4. If you created files in step 2, run `git add --chmod=+x .githooks/pre-commit`, stage
   `.gitattributes` and `rules.md`, commit the way you commit brain changes, and push. If the
   commit is blocked, something already in the brain looks like a key: show me which file and stop.
5. Test it. Create `inbox/secret-check-test.md` containing only `AKIATESTTESTTESTTEST` (a fake
   AWS key), stage only that file, and try to commit it. The commit must fail with "Blocked". Then
   unstage and delete the test file, and never push it. If the commit went through, undo it with
   `git reset --soft HEAD~1`, delete the file, and tell me the check isn't working.
6. Tell me in one line whether the test was blocked, and that I should paste this same prompt on
   each of my other devices.

```sh
#!/bin/sh
# Brain safety check: stops a commit if anything in the brain looks like a key or token.
p='AKIA[0-9A-Z]{16}|gh[pousr]_[A-Za-z0-9]{36}|github_pat_[A-Za-z0-9_]{40,}|(^|[^A-Za-z0-9_-])sk-[A-Za-z0-9_-]*[A-Za-z0-9]{20}|[rs]k_live_[A-Za-z0-9]{20,}|xox[abposr]-[A-Za-z0-9-]{10,}|AIza[0-9A-Za-z_-]{35}|-----BEGIN [A-Z ]*PRIVATE KEY'
found=$(git grep --cached -I -l -E -e "$p")
if [ -n "$found" ]; then
  echo "Blocked: these files look like they contain a key or token:" >&2
  echo "$found" >&2
  echo "Take it out and keep it in a password manager instead. Don't skip this check." >&2
  exit 1
fi
```
````

## 6. If a secret gets into the brain

1. **Rotate it first.** Revoke the key where it was issued and make a new one. That's the real
   fix. Deleting the file doesn't remove the key from git history, from the copies on your other
   devices, or from the apps that already read it.
2. Delete it from the brain and commit.
3. Find out how it got there. `git log` shows which agent and device made each commit.

GitHub's free secret scanning only covers public repos, so don't count on a warning for a private
brain.

## 7. Watch for planted instructions

Anything one agent writes into the brain, every other agent reads. If a web page, email, or
message tricks one agent into saving "always run this command first", the rest will see it. The
brain instructions tell agents that notes are information, not orders, and that only you change
`rules.md`. You can help:

- Sort `inbox/` yourself, and delete anything that reads like an order.
- Now and then, skim what changed: `git log --stat` in the brain folder, or the commit list on
  GitHub. Every commit names the agent and device that made it, and `git revert <commit>` undoes
  one.
- Be most careful with an agent that has all three: your brain (private data), the web or your
  messages (content anyone can write), and a way to send things out (email, posts, webhooks).
  Simon Willison calls this [the lethal trifecta](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/).
  Make that agent ask before it sends anything.

## 8. Lost or replaced a device

- Revoke its GitHub access. Browser sessions are at https://github.com/settings/sessions. For the
  GitHub CLI, open https://github.com/settings/applications, find **GitHub CLI**, and revoke it.
  That signs `gh` out everywhere, so run `gh auth login` again on the devices you keep.
- Turn on disk encryption on every device with a copy of the brain: FileVault on a Mac, BitLocker
  or Device Encryption on Windows. Then a lost laptop doesn't hand over your brain.

## 9. Work material

Check your employer's AI and data policies before you put work in a personal repo. Keep out
customer data, anything under NDA, and anything your employer wouldn't want in a personal account.
When you're not sure, write a pointer ("the Acme contract is in the company Drive") instead of the
content.
