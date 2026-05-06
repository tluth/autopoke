# autopoke

A lightweight periodic mouse-mover and clicker for Linux (X11 / XWayland).

Configure a screen coordinate once; autopoke moves the cursor there and clicks at randomised intervals with pixel-level jitter on every interaction. Useful for any scenario that requires periodic mouse activity at a fixed screen location.

## How it works

Each click cycle, autopoke:

1. Moves the mouse to the configured target with a small random pixel offset
2. Clicks
3. Waits for a randomised interval (±20% of the configured value, 5 s default)
4. Repeats from step 1

The randomised position and interval produce a more natural interaction pattern than a fixed, mechanical click.

Configuration is stored in `~/.config/autopoke/config` ([XDG Base Directory](https://specifications.freedesktop.org/basedir-spec/latest/) compliant).

## Requirements

- Linux with **X11** or **XWayland**
- [`xdotool`](https://github.com/jordansissel/xdotool) — mouse control (required)
- [`xinput`](https://gitlab.freedesktop.org/xorg/app/xinput) — click detection during `configure` (optional; falls back to Enter-based capture if unavailable)

Both are installed automatically by `autopoke install`.

## Installation

**1. Get the script**

```bash
git clone <repo-url>
cd autopoke
```

Or download the `autopoke` file directly.

**2. Run the installer**

```bash
chmod +x autopoke
./autopoke install
```

This will:
- Detect any missing dependencies and install them via your system package manager (`apt`, `dnf`, `pacman`, or `zypper`)
- Copy `autopoke` to `~/.local/bin/`

**3. Add `~/.local/bin` to your PATH** (if not already present)

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
which autopoke
```

## Setup

**Set the click target** (run once interactively):

```bash
autopoke configure
```

Move your mouse to the desired location and click it. autopoke detects the click, displays the captured coordinates, and asks you to confirm. Answer `n` to retry if you misclicked.

On systems where click detection is unavailable (e.g. a pure Wayland session without XWayland), autopoke falls back to prompting you to press Enter while the cursor is over the target.

**Set the click interval and duration** (optional; sensible defaults are used otherwise):

```bash
autopoke configure --interval 10   # click every ~10 seconds
autopoke configure --duration 1800 # default run time of 30 minutes
```

Both flags can be combined, and either can be re-run independently at any time without affecting other settings:

```bash
autopoke configure --interval 5 --duration 3600
```

## Usage

```
autopoke configure                      Set the click target (interactive)
autopoke configure --interval <secs>    Save the click interval
autopoke configure --duration <secs>    Save the default run duration
autopoke run                            Run with saved settings (default: 3600 s)
autopoke run <seconds>                  Run for a specific duration
autopoke <seconds>                      Shorthand for run
autopoke install                        Install dependencies and binary
autopoke uninstall                      Remove the installed binary
autopoke help                           Show help
```

Press **Ctrl+C** at any time to stop a running session.

### Examples

```bash
autopoke configure --interval 10  # click every ~10 s (randomised ±20%)
autopoke run                      # run for the saved or default duration
autopoke run 1800                 # run for 30 minutes, override duration only
autopoke 300                      # shorthand — run for 5 minutes
```

## Configuration

```
~/.config/autopoke/config
```

```ini
TARGET_X=1234
TARGET_Y=567
INTERVAL=5
DURATION=3600
```

| Key | Description | Default |
|---|---|---|
| `TARGET_X`, `TARGET_Y` | Click target coordinates (set via `configure`) | — |
| `INTERVAL` | Seconds between click cycles | `5` |
| `DURATION` | Total run time in seconds | `3600` |

The interval is randomised ±20% on each cycle (minimum spread: 0.5 s), so an interval of `10` produces actual sleeps in the range 8–12 s.

Edit this file directly to adjust any value without re-running `configure`.

## Uninstalling

```bash
autopoke uninstall
```

Removes `~/.local/bin/autopoke`. You will be prompted whether to also delete the config directory.
