# Changelog

All notable changes to Stenobar are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project
follows semantic versioning.

## [Unreleased]

## [1.0.0-beta.22] - 2026-07-27

### Changed
- Local dictation is much faster after the first use. Parakeet and WhisperKit
  models now stay loaded for as long as Stenobar is running, so each dictation
  after the first takes well under a second instead of around one. The models
  also start loading in the background when Stenobar launches, so the first
  dictation of a session no longer waits on a twenty-second model load —
  previously that load ran at the worst possible moment, right after you
  stopped speaking. Switching models in Settings → Dictation reloads
  immediately so the new one is ready when you are. If you use Apple Speech or
  a cloud provider, nothing loads and nothing changes.
- Internal: the local transcription providers now have automated test coverage
  of their result handling and branch selection, which previously could only be
  verified by hand against a downloaded model.

### Fixed
- Dictation no longer gets stuck on "Transcribing…". Ending a take almost
  immediately — a fumbled hotkey press, or a modifier chord you thought better
  of — used to hand the near-empty recording to the full local pipeline
  anyway, which spent tens of seconds loading models before reporting the
  empty transcript it could never have avoided. Takes shorter than 0.3 s are
  now discarded up front.
- Added a way out of a transcription that's taking too long: an ✕ on the
  dictation HUD, or Esc. Cancelling frees the hotkey immediately instead of
  swallowing every subsequent press, and nothing is pasted or copied.
- A transcription that never returns at all now gives up after 60 seconds and
  says so, rather than leaving dictation permanently unavailable until you
  restart the app. Applies to the Thoughts HUD as well.

## [1.0.0-beta.21] - 2026-07-13

### Added
- Hold a single modifier key to dictate or capture a thought. In Settings →
  Shortcuts, each "hold" shortcut now takes an optional bare modifier — pick a
  side (left or right) and a key (⌘ ⌃ ⌥ ⇧), and holding just that key starts
  recording. Press any other key while it's down and the take is cancelled
  silently, so your everyday shortcuts still work: ⌘C copies as usual and
  nothing is transcribed or pasted. Right-side keys are the sweet spot, since
  most shortcuts are typed with the left hand. Your existing key-combo
  shortcuts keep working alongside it, and a modifier can drive dictation or
  Thoughts, but not both. Needs Accessibility permission — without it the
  modifier shortcut stays inert.

## [1.0.0-beta.20] - 2026-07-09

### Fixed
- Speaker labels and per-line timestamps now actually work on long local
  transcriptions. beta.19 introduced a bug that shuffled words at the seams
  between processing windows; the app noticed the transcript no longer matched
  and threw the timed lines away, leaving one untimed block labelled with a
  single speaker. Anything past roughly half a minute was affected.
- Setting "key terms & context" on a recording no longer costs you timestamps
  and speaker labels. Locally transcribed recordings with key terms were always
  returned as one unlabelled block, at any length.
- Locally transcribing with key terms no longer silently drops pieces of the
  transcript — on a 15-minute recording it was losing a few hundred characters
  scattered through the middle.

## [1.0.0-beta.19] - 2026-07-09

### Added
- Speaker labels on local transcripts: Parakeet transcription can now work
  out who is speaking and tag each line, splitting lines where the speaker
  changes. Turn on "Speaker diarization" in Settings → Transcription and
  download the one-time ~35 MB model; it runs entirely on-device. Leave the
  model undownloaded and transcription works exactly as before, without
  labels.
- Parakeet transcripts now have per-line timestamps: local transcriptions
  split into timed lines, so playback follows the transcript and clicking a
  line jumps the audio — just like cloud transcripts. (Runs using key-term
  boosting keep single-block text for now.)

### Changed
- Privacy: local Parakeet transcription and dictation are now enforced
  offline. The app touches the network only for model downloads you start
  in Settings (and dictation's first-run model download). If a downloaded
  model is damaged, you'll now see an error asking you to re-download it
  instead of the app silently re-fetching it.

### Fixed
- Local (Parakeet) transcription and dictation of anything longer than about
  15 seconds could drop words, lose the ending, or come back empty — showing
  "No speech detected" after real speech. Long audio now runs through the
  streaming engine, and long transcripts get the same per-line timestamps and
  speaker labels as short ones (previously they collapsed to a single
  untimed block attributed to one speaker).
- Dark mode: the strip above the recordings list no longer lags behind the
  rest of the window while you drag the window or play video behind it. The
  same artifact is gone from the Thoughts window.

## [1.0.0-beta.18] - 2026-07-07

### Added
- Summaries: each recording can now carry its own optional "key terms &
  context" — names and their correct spellings, jargon, or topics to focus
  on — which is appended to whichever summary prompt you've selected. Set it
  from the book icon next to the summary controls; it stays with the
  recording, so re-summarizing keeps using it.

### Changed
- New installs now default AssemblyAI transcription to the newer
  `universal-3-5-pro` model, falling back to `universal-3-pro`. If you've
  customized the model list in Settings, your selection is unchanged.

## [1.0.0-beta.17] - 2026-07-06

### Fixed
- If the system audio capture stream dies mid-recording (a rare macOS
  capture-engine failure), Stenobar now stops cleanly, saves everything
  captured up to that point, and explains what happened in the menu-bar
  popover — previously the recording timer kept running while nothing was
  being captured.
- Auto-delete no longer removes a recording from the library when moving its
  files to the Trash fails: the recording stays fully intact and the cleanup
  is retried on the next sweep, instead of leaving orphaned audio files with
  their title, tags, and markers lost.
- Failures that previously printed only to the developer console (disk-full
  write errors, library save errors, screen-capture refresh errors) are now
  written to the diagnostics log, so problem reports are actually diagnosable.

### Changed
- Performance: smoother interface while recording and dictating, faster
  live-streaming summaries, and snappier typing and search in large libraries.

## [1.0.0-beta.16] - 2026-07-06

### Added
- Summary prompts: save a library of named prompts, assign one per project, and
  pick one ad-hoc when summarizing a recording.
- Per-project summary AI: each project can override the summary provider and
  model, and tags/title generation can use a separate provider and model (both
  globally and per project).

## [1.0.0-beta.15] - 2026-06-28

### Added
- List-aware Thoughts: when a dictated thought is clearly an enumeration,
  Stenobar formats its description as a list instead of a comma run-on, and
  each destination renders it natively — Todoist sub-tasks, TickTick and
  Things checklist items, Notion bulleted lists, Apple Notes bullet lists,
  and structured items in the Shortcuts payload. Obsidian and Bear get the
  markdown list for free, and Apple Reminders keeps it as a text list in the
  note. The Thoughts Inbox shows list descriptions with per-category glyphs
  (checkboxes for tasks and reminders, bullets for notes).

### Changed
- Captured thoughts now keep a cleaned-up description separate from your
  verbatim words. Each thought carries an editable, reformatted description
  plus the original spoken text shown read-only beneath it, in both the Inbox
  and the preview sheet. Synced notes send the tidied description by default; a
  new "Include original words in synced notes" setting appends the verbatim
  capture below it for apps like Reminders, Notes, and Todoist.
- The Thoughts Inbox now shows a processing spinner on a thought's row while it
  is still being classified and routed.

## [1.0.0-beta.14] - 2026-06-28

### Added
- Obsidian as a Thoughts destination: route a captured thought straight into
  your Obsidian vault as a new note via the obsidian:// URL scheme, configured
  in Settings → Integrations.
- Bear as a Thoughts destination: file a thought as a Bear note over Bear's
  x-callback-url API, with confirmation that the note was actually created.
- Notion as a Thoughts destination: connect your workspace with OAuth and send a
  thought as a new page under a parent page you choose.

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
