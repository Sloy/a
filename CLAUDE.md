# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

`a` is a bash CLI that wraps everyday adb chores behind an fzf menu. It was extracted from the `and/` directory of Sloy's dotfiles and renamed. Users install it with `install.sh`, which clones the repo into `~/.a` and sources `init.sh` from their shell rc.

## Layout

- `a` — dispatcher: runs the update check, shows the fzf menu when bare, otherwise maps the subcommand name (loose spellings like `fontSize`, `nav`, `tb` included) to `commands/<name>.sh` and `exec`s it
- `init.sh` — sourced by zsh or bash; defines the `a` function that runs `./a` and pushes the resolved one-liner (written by `banner` to `$A_RESOLVED_FILE`) into shell history
- `install.sh` — curl-able installer; honours `A_HOME`, `A_REPO`, `A_NO_MODIFY_RC`
- `lib/common.sh` — colours, `FZF_STYLE`, `pick`, `banner`, `die`, spinners; sets `$A_ROOT`/`$A_LIB`/`$A_COMMANDS`
- `lib/device.sh` — `resolve_device` sets `"${ADB[@]}"` to an adb call pinned to one device: 0 devices errors, 1 is used silently, 2+ get an fzf picker
- `lib/update.sh` — `check_for_updates` warns when the last background `git fetch` found new commits and starts a detached fetch at most every `A_UPDATE_INTERVAL_MINUTES` (default 1440); stamp file is `.git/a-last-update-check`
- `commands/*.sh` — one file per subcommand, each runnable standalone

## Conventions

- To add a command: drop a script in `commands/`, add it to `COMMANDS` and `canonical()` in `a`, and to the README table.
- Commands that act on a single device call `resolve_device`; `screenshot` deliberately skips it and loops over every device instead.
- `pick` and `resolve_device` must be called directly, never inside `$(...)` — `die` and the fzf prompt both need the caller's shell and tty.
- Every action calls `banner` with the full non-interactive command so it can be copied and lands in history.
- `lib/common.sh` supports fzf older than 0.35 (no `--border-label`, no named colours); keep both `FZF_STYLE` branches working.

## Testing

No test suite. Always test changes locally before committing and pushing.

The installed `a` shell function runs whatever lives at `~/.a`. On a development machine `~/.a` should be a symlink to this working copy, so the user's shell runs local edits immediately:

```bash
mv ~/.a ~/.a.bak && ln -s "$PWD" ~/.a
```

If `~/.a` is a regular directory instead, local edits are invisible to the user's `a` until they are pushed and `a update` runs.

Check changes by running them against a connected emulator, e.g. `./a animations off`, and test the installer in a throwaway home:

```bash
HOME=$(mktemp -d) A_REPO="$PWD" bash install.sh
```
