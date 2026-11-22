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
  - `~/.claude.json`: when a session ends, folders it added are copied back
  - the account dirs `~/.claude-*`: it adds symlinks and its own `.claude.json` keys, and keeps `.credentials.json.prev`, the login a heal replaced (0600)
  - `~/.clx-sessions/<pid>`: one dir per running session
  - `~/.local/bin/{clx,clx-move,cm-login,clx-openclaw}`, `~/.claude/hooks/cc-handoff.sh` and `~/.local/share/clx/openclaw/skills/`, through `install.sh`
  - `~/.claude/settings.json` and, with `--alias`, `~/.bashrc`, through `install.sh`. Both are backed up first.
  - only when you run `clx-openclaw setup`:
    - the shim `~/.local/share/clx/openclaw/bin/claude` and its `state.json`
    - the drop-in `~/.config/systemd/user/<unit>.d/zz-clx-openclaw.conf` and the environment file it names, `~/.local/share/clx/openclaw/gateway.env` (only `PATH` and `CLAUDE_CODE_EXECUTABLE`), when the gateway is a systemd user unit
    - a `claude` link to the shim in one directory of yours on the gateway's PATH, when that directory comes before every other `claude`
    - with `--with-skill`, `<OpenClaw state dir>/skills/clx-accounts/SKILL.md`

    It only overwrites or deletes files that carry its marker comment. It never restarts anything.
  - Files are created `0600`. A file that already exists keeps its mode. The shim (`0755`), the drop-in, its environment file and the skill (`0644`) need to be readable by the gateway.
- **Processes:** one small watcher per running session (`clx --heal-watch`). It exits when the session ends. It also reads the session's own transcript for failed requests, only the part written since its last look.
- **Other processes' environments:** `clx-openclaw status` and `doctor` read the running gateway's `/proc/<pid>/environ`. They keep only `PATH` and `CLAUDE_CODE_EXECUTABLE`, and check a few clx and credential variable names for presence. Every other value is dropped unread and never printed.

## Supported versions

Only the latest release gets fixes.
