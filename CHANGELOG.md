# Changelog

All notable changes to Stenobar are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project
follows semantic versioning.

## [Unreleased]

### Added
- Initial public release: system + mic capture, library, multi-provider
  transcription and summarization, global-hotkey dictation HUD, and the
  Thoughts pipeline (LLM-classified utterances routed to Reminders / Notes /
  Todoist / Shortcuts / Inbox).
- `stenobar://` URL scheme and a JSON library index for external integrations
  (e.g. a Raycast extension): open the library / a recording / project /
  Thoughts inbox / settings, and fire record-toggle, dictate, thought-capture,
  and marker the same way the global hotkeys do. A versioned `index.json`
  snapshot of recordings + thoughts is refreshed on every change so external
  tools can search without touching SwiftData. "Copy Link" actions produce
  `stenobar://recording/<uuid>` links. See `docs/URL-SCHEME.md`.
- App Intents for Shortcuts, Siri, and Spotlight: start/stop/toggle recording,
  drop a marker, toggle dictation, capture a thought, open the library or
  Thoughts inbox, and get your last recording — with built-in Siri phrases and
  no setup.
- AssemblyAI as a dictation provider: low-latency streaming transcription via
  AssemblyAI Universal-Streaming, selectable in Settings → Dictation and
  surfaced in onboarding. Gated on an AssemblyAI API key.
- Deepgram as a dictation provider: streaming transcription with a model picker
  (Flux and Nova-3) in Settings → Dictation and onboarding. Gated on a Deepgram
  API key.

### Changed
- GUI performance: API-key presence checks no longer hit the Keychain on
  every render (Settings panes, summary availability in the library detail
  view); markdown summaries are styled once per text snapshot instead of on
  every redraw (most visible while a summary streams in); transcript rows
  skip re-rendering when nothing about them changed; and the streaming
  summary's follow-the-tail auto-scroll is coalesced instead of firing on
  every LLM delta.

### Fixed
- The Settings window now gets a Dock icon and the app menu bar (Stenobar /
  File / Edit …) like the Library and Thoughts windows, instead of leaving the
  app menu-bar-only — so you can click the Dock icon to return to it. The main
  menu bar also no longer intermittently fails to appear when a Library or
  Thoughts window is open and frontmost.
- The Dock icon's right-click menu now lists the open Library (Recordings) and
  Thoughts windows so you can jump straight to them; previously only Settings
  appeared there.
- Microphone recordings from external USB audio interfaces that deliver
  24-bit (or 32-bit) integer PCM — e.g. the Focusrite Scarlett 2i2 — no longer
  capture as static/buzz. The WAV writer previously decoded every non-float
  buffer as 16-bit, misaligning every sample; it now decodes 16/24/32-bit
  signed PCM correctly.
- The selected microphone is now remembered across recordings and app launches
  instead of snapping back to the system-default input each time. The global
  hotkey honors the same saved device.
- Numbered lists in summaries no longer render every item as "1." — items
  keep their numbers from the markdown source, including when paragraphs sit
  between the numbered points (the shape LLM summaries usually produce).
- Windowless deep links (`stenobar://dictate`, `record`, `thought`, `marker`)
  no longer pop the Recordings library open as a side effect. `open
  stenobar://…` reactivates the app, and SwiftUI auto-presents the first
  declared `Window` scene on that reopen — so the library surfaced even when
  the command needed no window. The hidden bridge window is now declared first,
  so the reopen resolves to it instead.
