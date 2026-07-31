# Claude Prototypes

Self-contained, no-server web app prototypes. Each app is a single HTML file — open it directly in a browser (`file://` works) or use a GitHub Pages link. No build step, no account ever required.

**There are currently no active prototypes in this repo.** It's a staging shelf: new single-file experiments land here, and the ones that prove themselves graduate into their own repos.

---

## Graduated & retired

| App | Status |
|-----|--------|
| **PAPTrack** | Graduated. Now its own product at [eagleadams86.github.io/paptrack](https://eagleadams86.github.io/paptrack/) ([repo](https://github.com/eagleadams86/paptrack)), alongside native [iOS](https://github.com/eagleadams86/paptrack-ios) and [Android](https://github.com/eagleadams86/paptrack-android) apps. `cpap-tracker.html` here is now just a redirect so old bookmarks keep working. |
| **ReturnRadar** | Retired and removed. It was a return-window & warranty deadline tracker. The last version is still in git history — recover it with `git show 6fe221d:returnradar.html > returnradar.html`. |

---

## Files

| File | Description |
|------|-------------|
| `cpap-tracker.html` | Redirect stub → PAPTrack's new home (`eagleadams86.github.io/paptrack`) |
| `favicon.ico` | Shared favicon (same one used across the dashboard and lottery repos) |

---

## Architecture

```
GitHub Pages (static hosting, this repo, main branch)
    └── one *.html file per prototype — zero dependencies
            └── all state ──► browser localStorage (per device)
```

There is no server of our own. Each prototype must work from a double-clicked file just as well as from GitHub Pages, and any data it holds stays on the device.

---

## Adding a prototype

1. Drop a new self-contained `*.html` file in the repo root (link `favicon.ico` in its `<head>`)
2. Add it to the list at the top of this README
3. Push — GitHub Pages serves it at `eagleadams86.github.io/prototypes/<file>.html`

New prototypes default to the Midnight palette (deep indigo/navy; canonical source: `theme.css` in the lottery repo).
