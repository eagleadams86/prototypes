# Prototypes repo

Self-contained single-file web app prototypes. Deployed via GitHub Pages: https://eagleadams86.github.io/prototypes/

> **The repo currently holds no active prototypes** — ReturnRadar was retired and deleted (recover from history: `git show 6fe221d:returnradar.html`), and PAPTrack graduated out. It's a staging shelf for new single-file experiments.

> **PAPTrack moved out of this repo** into its own product at https://eagleadams86.github.io/paptrack/ (repo `eagleadams86/paptrack`). `cpap-tracker.html` here is now only a redirect stub — don't develop the app here; edit it in the `paptrack` repo.

- Each app is **one HTML file** — everything inline, no build step, no server, works via `file://`. Keep it that way; new prototypes follow the same pattern.
- No accounts or sign-ups ever required.
- **README.md is the index** for all prototypes — it must be updated whenever an app is added or meaningfully changed.
- New prototypes default to the Midnight palette (deep indigo/navy; canonical source: `theme.css` in the lottery repo).
- After changes: **browser-test locally first**, then commit, push, verify the Pages deploy, and spot-check live. To serve locally, use whatever your environment provides: the desktop app's preview pane reads `.claude/launch.json` (port 8011); otherwise run `python3 -m http.server 8011` in this folder and drive a browser with whatever automation is available (e.g. Playwright). Don't spend time hunting for a specific tool — any local server + browser works. These prototypes are candidates for eventual native iOS/macOS versions once polished.

- **Commit subjects are user-facing:** each page shows its last 10 commit messages in a public "Recent changes" section. Write commit subject lines in plain English a non-developer can read (what changed and why it matters, not implementation detail).
