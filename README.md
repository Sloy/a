# a

One short command for the adb chores Android developers repeat all day: animation scales, font size, screenshots, screen recordings, TalkBack and friends.

Run `a` bare and pick from an fzf menu, or pass the command and its argument to skip every prompt. Every run prints the equivalent one-liner, and adds it to your shell history, so the next time is one ↑ away.

```
$ a
╭─ Android ─────────────────────────────────────────────────╮
│ › ↑↓ move · enter select · esc quit                        │
│ ❯ animations        window, transition and animator scales │
│   screenshot        capture every device to ~/Downloads    │
│   record            record the screen to ~/Downloads       │
│   font-size         system font scale                      │
│   ...                                                      │
╰────────────────────────────────────────────────────────────╯

────────────────────────────────────────────────────────────
 ANDROID · FONT SIZE
 Device   Pixel 8 (35211FDH2000AB)
 Command  a font-size 1.5
────────────────────────────────────────────────────────────
 ✓ font scale set to 1.5
```

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/Sloy/a/main/install.sh | bash
```

Then open a new terminal and type `a`.

The installer clones this repo into `~/.a` and adds one line to `~/.zshrc` (or `~/.bashrc` if bash is your shell):

```bash
[ -f "$HOME/.a/init.sh" ] && . "$HOME/.a/init.sh"
```

Options, set as environment variables in front of `bash`:

| Variable | Effect |
|---|---|
| `A_HOME=<dir>` | Install somewhere other than `~/.a` |
| `A_NO_MODIFY_RC=1` | Don't touch your rc file. Useful when your dotfiles source `init.sh` themselves |
| `A_REPO=<url>` | Clone from a fork or a local path |

```bash
curl -fsSL https://raw.githubusercontent.com/Sloy/a/main/install.sh | A_NO_MODIFY_RC=1 bash
```

### Requirements

- macOS (`a paste` uses `pbpaste`; everything else is plain bash and adb)
- `git`
- [`fzf`](https://github.com/junegunn/fzf) for the interactive menus: `brew install fzf`
- `adb` on your `PATH`, usually `$ANDROID_HOME/platform-tools`

## Commands

| Command | Arguments | What it does |
|---|---|---|
| `a animations` | `off` · `fast` · `on` · `slow` · `<scale>` | Sets window, transition and animator scales together (0.0, 0.5, 1.0, 5.0 or any number) |
| `a screenshot` | `now` · `<seconds>` | Captures **every** connected device to `~/Downloads`, optionally after a countdown |
| `a record` | — | Records the screen until you press enter, then pulls the mp4 to `~/Downloads` (Android caps it at 3 min) |
| `a touch-pointer` | `on` · `off` | Shows or hides the circle drawn under every touch |
| `a font-size` | `0.85` · `1` · `1.5` · `2` · `<scale>` | Sets the system font scale |
| `a talkback` | `toggle` · `on` · `off` | Turns the TalkBack screen reader on or off. Opens the Play Store if Accessibility Suite is missing |
| `a navigation` | `gestures` · `buttons` | Switches between gesture and three-button navigation |
| `a paste` | — | Types the macOS clipboard into the focused text field |
| `a current-activity` | — | Prints the foreground activity's class, package and component |
| `a fix-date` | — | Syncs the device clock to your Mac's (needs a rootable emulator image, not Play Store images) |
| `a update` | — | Pulls the newest version of `a` |

Leave out the argument and `a` asks for it. `a <command> --help` explains each one.

Command names are forgiving: `a font`, `a fontSize`, `a anim`, `a nav`, `a tb`, `a ss`, `a rec` and `a activity` all work.

### Multiple devices

When more than one device or emulator is connected, `a` shows a picker before acting. With one device it just uses that one. `a screenshot` is the exception: it captures all connected devices at once.

## Examples

```bash
a animations off       # stable UI tests and faster demos
a animations slow      # inspect a transition frame by frame
a font-size 2          # check your layouts at the largest font scale
a screenshot 5         # 5 second countdown, then capture every device
a record               # record a bug repro, press enter to stop
a talkback toggle      # quick accessibility check
a touch-pointer on     # show taps in a screen recording
a paste                # type a long password or URL into the device
a current-activity     # which screen am I looking at?
```

## Updates

When you run `a`, it checks for new versions in the background, at most once a day. The check never slows down the command you ran. If it finds a new version, the next run prints:

```
! a: update available (3 new commits). Run a update
```

Run `a update` to fast-forward your install. To turn the check off, set `A_NO_UPDATE_CHECK=1`. To change how often it runs, set `A_UPDATE_INTERVAL_MINUTES`.

## Uninstall

```bash
rm -rf ~/.a
```

Then remove the `init.sh` line from your `~/.zshrc` or `~/.bashrc`.

## Disclaimer

The interactive `a` command was created by AI, based on a set of adb aliases and shell functions I collected and used over the years. The commands are the ones I rely on every day, but the scripts around them (menus, device picker, installer, updater) were generated by an AI tool. Read them before trusting them with anything important.
