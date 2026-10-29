# Get GitHub ready

Do this before the workshop. It takes about five minutes, and it's where most people get stuck
on the day.

You need a free GitHub account (https://github.com/signup) and Claude Code or Codex. Make the
brain under your personal GitHub account, not a work one, so it stays yours.

## 0. Open a terminal

- **Mac:** press Cmd+Space, type `Terminal`, and press Return.
- **Windows:** open the Start menu, type `PowerShell`, and press Enter.

Type each command below, press Enter, and wait for it to finish before the next one.

## 1. Install Claude Code or Codex

You need one of them, logged in. Claude Code needs a paid Claude plan. Codex signs in with your
ChatGPT account.

- Claude Code: https://code.claude.com/docs/en/setup
- Codex: https://learn.chatgpt.com/docs/codex/cli

Then type `claude` or `codex` to start it and log in.

## 2. Install git and the GitHub CLI

**macOS**

1. Type `git --version`. If a window offers to install the developer tools, click **Install** and
   wait. It's a big download, so do it before the workshop.
2. Download `gh_..._macOS_universal.pkg` from https://github.com/cli/cli/releases/latest and open
   it. Already have Homebrew? `brew install gh` does the same.

**Windows**

```powershell
winget install --id Git.Git -e
winget install --id GitHub.cli -e
```

No winget? Get Git from https://git-scm.com/download/win and `gh_..._windows_amd64.msi` from
https://github.com/cli/cli/releases/latest.

Then close your terminal and open a new one.

**Linux:** follow https://github.com/cli/cli/blob/trunk/docs/install_linux.md

## 3. Log in

```bash
gh auth login --hostname github.com --git-protocol https --web
```

If it asks whether to authenticate Git with your GitHub credentials, say **yes**. Copy the
one-time code it shows, press Enter, and paste the code into the browser page that opens.

## 4. Let git use your login

```bash
gh auth setup-git
```

## 5. Tell git who you are

Skip this if you've committed with git on this computer before.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## 6. Check it worked

```bash
gh auth status
```

You should see `Logged in to github.com account <your-username>` and
`Git operations protocol: https`. If it says `ssh`, run `gh config set git_protocol https`.

## If something's off

- **More than one GitHub account?** `gh auth status` shows which one is active, and
  `gh auth switch` changes it. Your brain repo is created under the active account.
- **`gh: command not found` right after installing?** Open a new terminal window.
- **In Codex, `gh auth status` says your token is invalid?** That's Codex's sandbox blocking the
  network. Run the check in a normal terminal window instead.
- **Work laptop won't let you install anything?** Use the browser path in
  [BRAIN-INSTRUCTIONS.md](BRAIN-INSTRUCTIONS.md#no-terminal), or bring a personal laptop.
