# Prototypes repo

Self-contained single-file web app prototypes (currently ReturnRadar and PAPTrack). Deployed via GitHub Pages: https://eagleadams86.github.io/prototypes/

- Each app is **one HTML file** — everything inline, no build step, no server, works via `file://`. Keep it that way; new prototypes follow the same pattern.
- No accounts or sign-ups ever required (PAPTrack's optional Google sign-in for cross-device sync is the only exception).
- **README.md is the index** for all prototypes — it must be updated whenever an app is added or meaningfully changed.
- New prototypes default to the Midnight palette (deep indigo/navy; canonical source: `theme.css` in the lottery repo).
- After changes: commit, push, verify the Pages deploy, and browser-test. These prototypes are candidates for eventual native iOS/macOS versions once polished.
