# bigwords — a big-word countdown timer

A single static page that turns any screen into a giant countdown timer and
keeps the screen awake. A drop-in replacement for the timer use case of
[bigwords.page](https://bigwords.page), with two extras:

- **Focus-stolen indicator** — if the browser tab loses focus (or is hidden),
  the keep-awake wake lock is released by the browser. This page shows a
  pulsing corner pill *and* a full-screen flashing **"FOCUS LOST"** overlay so
  you immediately know the screen may go to sleep. Click / press any key to
  take focus back and resume.
- **Negative countdown** — when time runs out the display flips to a flashing
  red **TIME'S UP** state and keeps counting negative (e.g. `-1m 03s`) so the
  overrun is obvious at a glance.

Everything lives in the URL, so sharing the link shares the timer. No account,
no backend, nothing stored.

## Usage

Open the page with a URL hash, matching the bigwords scheme:

```
https://<your-host>/#&timer=30m
```

| Param   | Meaning                                                        |
| ------- | ------------------------------------------------------------- |
| `timer` | Duration: `30m`, `1h30m`, `90m`, or seconds like `900` / `14400`. |
| `until` | ISO 8601 target, e.g. `until=2027-01-01T00:00:00` (local time, add `Z` for UTC). |
| `msg`   | Text shown above the timer (e.g. `msg=Break ends in`).        |
| `endmsg`| Text shown when finished (default: `Time's up!`).             |
| `zero`  | `freeze` to stop at 0 instead of counting negative.            |
| `bg`    | 6-digit hex background, e.g. `bg=000000`.                     |
| `fg`    | 6-digit hex foreground, e.g. `fg=ffffff`.                     |

The timer starts when the page opens (so opening or reloading starts it over),
and it keeps accurate time while the tab is in the background because it's
clock-based.

### Examples

```
#&timer=30m                       → bare 30-minute countdown
#Break ends in&timer=15m&endmsg=Back to work!
#&timer=1h&bg=111111&fg=ffd60a
#&until=2027-01-01T00:00:00&msg=Happy New Year in
```

## Controls

- **Click / tap** the screen or press **F** → toggle fullscreen (helps some TVs and lock screens stay awake).
- **Esc** → exit fullscreen.
- **Click / any key** while "FOCUS LOST" is showing → take focus back.

## Notes on keep-awake

Uses the [Screen Wake Lock API](https://developer.mozilla.org/docs/Web/API/Screen_Wake_Lock_API),
so the tab must be **focused and visible** for the screen to stay on. If the
user switches away or the OS dims the screen, the browser releases the lock —
which is exactly the "focus stolen" case the overlay warns about. Wake Lock is
not supported in every browser; the corner pill reports that.

## Hosting on GitHub Pages

A GitHub Actions workflow (`.github/workflows/deploy.yml`) deploys the site on
every push to `main`. This is a pure static site — it just publishes the repo
root, no build step.

1. Push to `main`.
2. **Repository → Settings → Pages → Build and deployment → Source**:
   select **"GitHub Actions"** (not "Deploy from a branch").
3. Wait for the *Deploy to GitHub Pages* workflow to finish.

Then use:

```
https://<username>.github.io/<repo>/#&timer=30m
```

> The path is part of the URL, so the hash comes *after* the repo path.
