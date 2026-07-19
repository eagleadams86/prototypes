# Android Port Plan — PAPTrack (CPAP Supply Tracker)

*Written 2026-07-19 by Claude (Fable 5). Android counterpart to `CPAP_IOS_PORT_PLAN.md`.
**The behavior spec lives there and in `cpap-tracker.html`** — the `SupplyItem` data model
and its web-compatible field shapes, the presets/kits tables, the urgency-threshold math,
the reorder rules, the notification design (§3), and the sync decision framework (§4) are
identical and not repeated here. This file covers only what's different on Android. The
HTML remains the ultimate spec.*

---

## 1. Shape and stack

Same single-screen app as the iOS plan: stat tiles, search, filter chips, urgency-sorted
supply cards, edit sheet, settings sheet.

- **Kotlin + Jetpack Compose**, single-activity, min SDK 26.
- **Dependencies: none for phases 1–4.** `SharedPreferences` under the same
  `pap-items` / `pap-theme` / `pap-updated` keys (JSON via built-in `org.json`), pure-Kotlin
  engine, Compose UI. Phase 5 adds Firebase SDKs *only if* the sync decision lands on
  option A (see §3 below).
- New repo folder **`~/claude-paptrack-android`**.
- Bottom sheets (`ModalBottomSheet`) for edit/settings — the natural Android equivalent of
  the web's dialogs and iOS's sheets.

Engine classes (model, presets, schedule math) stay free of Android imports — JVM unit
tests via `./gradlew test`, pinned to web-app outputs exactly as the iOS plan prescribes.

## 2. Notifications — same design, Android mechanics

Port the iOS plan §3 design (daily digest at a chosen time, replacement alerts 2 days
before + day-of, one-time reorder alerts). Android specifics:

- **`POST_NOTIFICATIONS` runtime permission** (Android 13+): ask on first launch with a
  one-line explanation; degrade gracefully if denied (the app still works, badges in-app).
- **Scheduling: `WorkManager`**, not `AlarmManager`. A daily periodic worker computes
  what's due and posts the digest + any item alerts. Supply reminders don't need
  minute-exact delivery, so avoid the exact-alarm permission entirely (`SCHEDULE_EXACT_ALARM`
  triggers Play policy review and user friction; WorkManager's ±flex is fine here).
  WorkManager is an AndroidX jetpack library — bundled via Gradle, not a third-party dep.
- **Notification channels**: one per category (Cleaning, Replacement, Reorder) so the
  user can silence categories in system settings for free — this replaces the iOS plan's
  in-app per-category toggles (keep simple in-app toggles too; they just flip channel use).
- Same rebuild-don't-diff rule: recompute the schedule after every data mutation.
- App-icon badge counts aren't reliable cross-launcher on Android — skip that iOS nicety;
  the persistent notification categories cover it.

## 3. Sync — same decision, different weights

The iOS plan §4's two options carry over, but Android tilts the scales:

- **Option A (keep Firestore)** is *easier* on Android than iOS — Firebase is
  Google-native: add `google-services.json`, Firebase Auth + Credential Manager for Google
  sign-in, Firestore SDK. Register the Android app in the existing `paptrack-6c817`
  project (Firebase console → Add app → Android). Same document (`paptrack/{uid}`), same
  shapes, same last-write-wins timestamp — the web-compatible field formats the data model
  preserves make this drop-in.
- **Option B (platform-native sync) is weaker here**: Android has no CloudKit equivalent
  with free invisible sync. The honest option-B on Android is "local only + JSON
  backup/import" (matching the web's export format), not a sync service.

So: **if sync matters at all on Android, choose A.** If the phone becomes the only
surface, local-only + JSON backup is fine. Same as iOS: build phases 1–4 fully
local-first; sync bolts on last.

## 4. Themes

Same 7 palettes (Forest, Light, Midnight, Sepia, **Slate**, Solarized, Synthwave —
transcribed from the `<style>` block of `cpap-tracker.html`, not the lottery `theme.css`).
Compose approach per the lottery Android plan §2: a palette data class via
`staticCompositionLocalOf`, Material components with explicit colors, Midnight default,
stored under `pap-theme`, no system dark-mode following.

## 5. Build order

1. **Scaffold + themes** — project, palettes, empty list shell. Emulator.
2. **Engine + JVM tests** — model, presets, schedule math pinned to web outputs.
3. **Main UI** — kits, cards, steppers, search, chips, edit sheet, stat tiles. Feature
   parity minus notifications/sync; still no permissions needed.
4. **Notifications** — §2 above. First phase worth a real device.
5. **Sync** — §3's chosen option, or defer indefinitely.
6. **Polish** — adaptive launcher icon (Midnight palette), JSON backup export/import
   (Storage Access Framework document picker — no storage permission needed), footer
   resupply tip.

## 6. Distribution

Same good news as the lottery Android plan §5: **sideload the release APK for $0, works
forever, no expiry** — no developer account, no review. Play Store ($25 one-time, 12-tester
closed-test hurdle for new personal accounts) only if it ever goes public. If it does:
PAPTrack tracks supplies, not health data — no health-data policy exposure, same as the
iOS App Review note.

**Prerequisites:** Android Studio, and an Android phone with USB debugging for phase 4+
(emulator suffices before that).
