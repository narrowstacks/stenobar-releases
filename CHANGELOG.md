# Changelog

All notable changes to Stenobar are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project
follows semantic versioning.

## [Unreleased]

## [1.0.0-beta.14] - 2026-06-28

## [1.0.0-beta.13] - 2026-06-23

### Fixed
- The Settings window now gets a Dock icon and the app menu bar (Stenobar /
  File / Edit …) like the Library and Thoughts windows, instead of leaving the
  app menu-bar-only — so you can click the Dock icon to return to it. The main
  menu bar also no longer intermittently fails to appear when a Library or
  Thoughts window is open and frontmost.
- The Dock icon's right-click menu now lists the open Library (Recordings) and
  Thoughts windows so you can jump straight to them; previously only Settings
  appeared there.

## [1.0.0-beta.12] - 2026-06-23

### Fixed
- Microphone recordings from external USB audio interfaces that deliver
  24-bit (or 32-bit) integer PCM — e.g. the Focusrite Scarlett 2i2 — no longer
  capture as static/buzz. The WAV writer previously decoded every non-float
  buffer as 16-bit, misaligning every sample; it now decodes 16/24/32-bit
  signed PCM correctly.
- The selected microphone is now remembered across recordings and app launches
  instead of snapping back to the system-default input each time. The global
  hotkey honors the same saved device.

## [1.0.0-beta.11] - 2026-06-15

### Added
- AssemblyAI as a dictation provider: low-latency streaming transcription via
  AssemblyAI Universal-Streaming, selectable in Settings → Dictation and
  surfaced in onboarding. Gated on an AssemblyAI API key.
- Deepgram as a dictation provider: streaming transcription with a model picker
  (Flux and Nova-3) in Settings → Dictation and onboarding. Gated on a Deepgram
  API key.

## [1.0.0-beta.10] - 2026-06-14

### Added
- App Intents for Shortcuts, Siri, and Spotlight: start/stop/toggle recording,
  drop a marker, toggle dictation, capture a thought, open the library or
  Thoughts inbox, and get your last recording — with built-in Siri phrases and
  no setup.

## [1.0.0-beta.9] - 2026-06-10

### Changed
- Library derivation is memoized and transcript search parallelized for a
  snappier library at scale.

### Fixed
- Onboarding playback no longer clips the start: microphone warm-up is trimmed
  and the player follows audio-format swaps.

## [1.0.0-beta.8] - 2026-06-10

### Changed
- GUI performance: API-key presence checks no longer hit the Keychain on
  every render (Settings panes, summary availability in the library detail
  view); markdown summaries are styled once per text snapshot instead of on
  every redraw (most visible while a summary streams in); transcript rows
  skip re-rendering when nothing about them changed; and the streaming
  summary's follow-the-tail auto-scroll is coalesced instead of firing on
  every LLM delta.

### Fixed
- Numbered lists in summaries no longer render every item as "1." — items
  keep their numbers from the markdown source, including when paragraphs sit
  between the numbered points (the shape LLM summaries usually produce).

## [1.0.0-beta.7] - 2026-06-10

### Fixed
- Windowless deep links (`stenobar://dictate`, `record`, `thought`, `marker`)
  no longer pop the Recordings library open as a side effect. `open
  stenobar://…` reactivates the app, and SwiftUI auto-presents the first
  declared `Window` scene on that reopen — so the library surfaced even when
  the command needed no window. The hidden bridge window is now declared first,
  so the reopen resolves to it instead.

## [1.0.0-beta.6] - 2026-06-09

### Added
- `stenobar://` URL scheme and a JSON library index for external integrations
  (e.g. a Raycast extension): open the library / a recording / project /
  Thoughts inbox / settings, and fire record-toggle, dictate, thought-capture,
  and marker the same way the global hotkeys do. A versioned `index.json`
  snapshot of recordings + thoughts is refreshed on every change so external
  tools can search without touching SwiftData. "Copy Link" actions produce
  `stenobar://recording/<uuid>` links. See `docs/URL-SCHEME.md`.

## [1.0.0-beta.5] - 2026-06-09

### Changed
- Public GitHub links now point to the `stenobar-releases` repo so they resolve
  for end users (the source repo is private).

## [1.0.0-beta.4] - 2026-06-09

### Added
- Website links (Home, Privacy, License) in the About box.

### Changed
- Release tooling syncs PRIVACY and LICENSE to the public releases repo.

## [1.0.0-beta.3] - 2026-06-09

### Fixed
- App failed to launch on some Macs: hardened runtime is disabled for ad-hoc
  Sparkle builds so Library Validation no longer blocks Sparkle.framework.

## [1.0.0-beta.2] - 2026-06-09

### Changed
- Release tooling: publish the DMG + Sparkle appcast to the public
  `stenobar-releases` repo; the DMG is named `Stenobar.dmg`.
