# Security policy

clx handles the OAuth logins of your Claude accounts, so security reports are welcome and taken seriously.

## Reporting a vulnerability

Report it privately through GitHub: **Security → Report a vulnerability** on this repository. Please don't open a public issue for it.

Please include:
- what an attacker controls and what they gain
- the file and line, or the steps to reproduce
- your clx version (the `CLX_VERSION` line near the top of `clx`)

You'll get an answer within a week. Fixes are released as a new tag, with a note in the release.

## What clx touches

- **Network:** only `GET https://api.anthropic.com/api/oauth/usage`, with each account's own token. clx never refreshes or sends a token anywhere else.
- **Files:**
  - `~/.claude/cc.json`, `cc-usage-cache.json`, `active-account.json` and `clx-handoff.log`
  - the account dirs `~/.claude-*`: it adds symlinks and its own `.claude.json` keys
  - `~/.clx-sessions/<pid>`: one dir per running session
  - `~/.local/bin/{clx,clx-move,cm-login}` and `~/.claude/hooks/cc-handoff.sh`, through `install.sh`
  - `~/.claude/settings.json` and, with `--alias`, `~/.bashrc`, through `install.sh`. Both are backed up first.
- **Processes:** one small watcher per running session (`clx --heal-watch`). It exits when the session ends.

## Supported versions

Only the latest release gets fixes.
