# Claude Prototypes

Self-contained, no-server web app prototypes. Each app is a single HTML file — open it directly in a browser (`file://` works) or use a GitHub Pages link. No build step, no account ever required.

**There are currently no active prototypes in this repo.** It's a staging shelf: new single-file experiments land here, and the ones that prove themselves graduate into their own repos.

---

## Graduated & Retired

| App | Status |
|-----|--------|
| **PAPTrack** | Graduated. Now its own product at [eagleadams86.github.io/paptrack](https://eagleadams86.github.io/paptrack/) ([repo](https://github.com/eagleadams86/paptrack)), alongside native [iOS](https://github.com/eagleadams86/paptrack-ios) and [Android](https://github.com/eagleadams86/paptrack-android) apps. `cpap-tracker.html` here is now just a redirect so old bookmarks keep working. |
| **ReturnRadar** | Retired and removed. It was a return-window & warranty deadline tracker. The last version is still in git history — recover it with `git show 6fe221d:returnradar.html > returnradar.html`. |

---

## Files

| File | Description |
|------|-------------|
| `index.html` | Hand-written landing page. Without it, GitHub Pages renders this README into an index page with no CSP and a third-party script — never delete it (or `.nojekyll`, which turns that rendering off). |
| `cpap-tracker.html` | Redirect stub → PAPTrack's new home (`eagleadams86.github.io/paptrack`) |
| `favicon.ico` | Shared favicon (same one used across the dashboard and lottery repos) |
| `theme.css` | The shared four-theme palette, generated in the private [claude-theme-pack](https://github.com/eagleadams86/claude-theme-pack) repo and copied here byte-for-byte. Linked, not inlined, so a palette change reaches this page like every other app |
| `tests.html` | Checks the files above rather than any app logic: `.nojekyll` present and empty, `index.html` real, a CSP on every page, no off-origin subresource, no palette hard-coded locally. Run it on `localhost` — see below. Opened on the live site it declines to run and shows the latest CI result instead, which is why its own CSP names `https://api.github.com`; the pages it checks name nothing |
| `.nojekyll` | Empty file. Its presence turns off Jekyll, which otherwise renders every `.md` here into a page with no CSP and a third-party script |

---

## Architecture

```
GitHub Pages (static hosting, this repo, main branch)
    └── one *.html file per prototype — zero dependencies
            └── all state ──► browser localStorage (per device)
```

There is no server of our own. Each prototype must work from a double-clicked file just as well as from GitHub Pages, and any data it holds stays on the device.

---

## Tests

There is no app logic here, so the suite checks the things this repo actually exists to hold:
`.nojekyll` is present and empty, `index.html` is a real hand-written page rather than a Jekyll
rendering of the README, every page carries a Content-Security-Policy that names no external
host, nothing loads a script or stylesheet from another site, the landing page runs no script
at all (its CSP has no `script-src`, so one would be silently dead), the redirect stub redirects
all three ways to the same URL, no page hard-codes a colour instead of using a theme token, and
the landing page is a real `<main>` / `<footer>` pair with `</main>` closing before the footer.

That pair went in on 2026-08-21: the closing three paragraphs sat *inside* `<main>` as loose
prose, and a `<footer>` nested in `main` is not contentinfo at all. The same correction moved
through every public page in the family at once. The middle of those three lines was reworded
in the same pass — it claimed the page "makes no network requests", which is not quite true of
a page that fetches `theme.css` and `favicon.ico`; it now says what it actually asks for. This
is the one public page in the family with no privacy policy, deliberately, so that sentence is
standing in for one and has to be exactly right. The suite asserts the claims rather than the
old wording, and fails if the overclaiming sentence comes back.

It runs on `localhost` only — it reads the repo folder, so it should test a copy you are
serving rather than the published site:

```bash
python3 -m http.server 8019   # then open http://localhost:8019/tests.html
```

CI runs the same page headlessly in Chromium on every push, and fails if the summary goes red.

## Adding a Prototype

1. Drop a new self-contained `*.html` file in the repo root (link `favicon.ico` in its `<head>`)
2. Add it to the list at the top of this README
3. **Add its filename to `PAGES` in `tests.html`** — otherwise it ships with no CSP check at all
4. Push — GitHub Pages serves it at `eagleadams86.github.io/prototypes/<file>.html`

New prototypes are designed against the Midnight palette (deep indigo/navy), which is the family's base palette; what a page *opens* on is `auto` — the family default since 2026-08-22 — which follows the reader's own system, resolving to Light or Midnight. Canonical source: the private [claude-theme-pack](https://github.com/eagleadams86/claude-theme-pack) repo — copy its generated `theme.css` or transcribe from its `tokens.json`. Both pages here carry `data-theme="auto"` on `<html>`; they have no picker and save nothing, so that attribute is the whole of their theme.
