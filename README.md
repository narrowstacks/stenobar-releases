# Stenobar

A native macOS menu-bar app that records what you hear and what you say —
at the same time — then transcribes, summarizes, and tags it so you can
find it again.

Stenobar lives in your menu bar. Click it to start capturing system audio
(or just one app, like Zoom or your browser) alongside your microphone.
When you're done, the recording lands in a searchable library where you
can play it back, read the transcript, ask an LLM for a summary, and
export to common audio or transcript formats.

Use it for meeting notes, lecture capture, podcast interviews,
voice-memos for yourself, or anything else where you want a clean
recording plus searchable text without juggling separate tools.

This repository hosts the public downloads (DMGs) and the Sparkle appcast
for in-app updates.

## Download & install

1. Download the latest **Stenobar.dmg** from the
   [Releases page](https://github.com/narrowstacks/stenobar-releases/releases/latest).
2. Open the DMG and drag **Stenobar** into your **Applications** folder.
3. Stenobar is currently distributed without an Apple Developer ID
   signature, so macOS Gatekeeper blocks the very first launch. To open it
   once, do **either** of:
   - Run this in Terminal:
     `xattr -dr com.apple.quarantine /Applications/Stenobar.app`, then open
     Stenobar normally, **or**
   - Try to open Stenobar, then go to **System Settings → Privacy &
     Security**, scroll to the message about Stenobar being blocked, and
     click **Open Anyway**.

After the first launch it opens normally, and future updates are delivered
automatically through Sparkle.

## Requirements

- macOS 26 or newer (the deployment target is macOS 26 so that Apple
  Intelligence / Foundation Models are available unconditionally for
  the Thoughts router)
- Apple Silicon recommended (required for WhisperKit and for the
  on-device Apple Intelligence router; everything else runs fine on
  Intel)

## Highlights

- **Two tracks, one click** — system audio and mic captured separately,
  so you can mix them later or transcribe just one side.
- **Per-app capture** — record only Zoom, only Safari, or Zoom AND Safari — only the app(s) you
  pick. Or grab everything the system is playing.
- **Live in the menu bar** — the icon shows a record indicator and the
  running clock while you're capturing. No Dock clutter when idle.
- **Crash-safe** — audio is written as raw PCM WAV with the header
  flushed every few seconds. If something crashes mid-recording, the
  audio is still there when you come back.
- **Hold-to-dictate** — hold a global hotkey, speak, release. Stenobar
  transcribes locally using Whisper or the macOS's SpeechAnalyzer and pastes the text into whatever app you're using.
- **Thoughts** — a second dictation hotkey for fleeting ideas: speak,
  release, and Stenobar transcribes locally, uses an LLM (Apple
  Intelligence on-device by default, or your choice of Anthropic /
  OpenAI / OpenRouter / Ollama) to classify what you said as a task,
  note, reminder, or "review later," and routes it to the right
  destination — Apple Reminders, Apple Notes, Todoist, a macOS
  Shortcut, or a local Inbox.
- **Bring your own transcription** — pick a local engine (Apple
  SpeechAnalyzer, WhisperKit) or a cloud one (AssemblyAI, Deepgram,
  OpenAI, Groq, Google). Keys are stored in the macOS Keychain.
- **Bring your own summaries** — Anthropic, OpenAI, OpenRouter, or a
  local Ollama instance. Swap and compare providers any time.
- **Searchable library** — full-text search across titles, tags, and
  transcript bodies; filter by date, sort, star, project, tag.

## First run

On first launch the onboarding window will:

1. Request **Microphone** and **Screen Recording** permission (Screen
   Recording is what macOS calls the entitlement that lets an app
   capture system audio). Optionally, request **Accessibility** permission to
   allow the app to paste dictation results into the active app.
2. Let you pick where recordings should be saved (defaults to
   `~/Documents/Stenobar/recordings/`).
3. Optionally set up keyboard shortcuts for starting and stopping
   recordings, dictation, and Thoughts capture.
4. Set up **projects**, which are organized folders that group related
   recordings.
5. Configure your **transcription** and **summary** providers — you can
   skip this and come back to it from **Settings** at any time.
6. Optionally configure **Thoughts** — pick a router (Apple Intelligence
   by default, or Anthropic / OpenAI / OpenRouter / Ollama) and the
   defaults for where tasks, notes, reminders, and review-later items
   should go.

You can revisit any of these steps from **Stenobar → Settings**, or `Cmd + ,`.

## Features

### Recording

- **Menu-bar app** — no Dock icon while idle. Stenobar also exists as
  a regular Dock-icon app while the recordings library window is open, then closes
  to the menu bar when you close it.
- **Live status in the menu bar** — the icon turns into a record
  indicator and shows running duration `MM:SS` (or `H:MM:SS`) while a
  recording is active.
- **Source picker** — "All System Audio" or one or more specific running
  apps. The app list is searchable, shows real app icons, and remembers
  the last-used selection.
- **Microphone picker** — any Core Audio input device, with the system
  default pre-selected.
- **Projects** — assign a recording to a project at start time. Projects
  map to on-disk subfolders (e.g.
  `recordings/<project-slug>/<UUID>/`) and can carry per-project
  transcription overrides.
- **Crash-safe capture** — always writes raw PCM WAV (`system.wav` +
  `mic.wav`) and flushes the WAV header every ~5 s, so a crash leaves
  usable audio behind. Format conversion (M4A / FLAC) happens at export
  time.
- **Configurable format** — 16/24/44.1/48 kHz, mono or stereo, set in
  **Settings → General → Recording format**.
- **Combined-track stitching** — produces a third `combined.wav` from
  system + mic, with a configurable layout: Mono, Stereo (L=System,
  R=Mic), or Stereo Mixed Down. Re-stitchable from the recording detail
  view if you change the default later.
- **Global hotkey** (Settings → General → Global shortcut) — toggle
  recording from anywhere with the last-used source and mic.
- **Hold-to-dictate hotkey** (Settings → Dictation → Hotkey) — hold a
  shortcut to record from the mic only; on release the audio is
  transcribed locally via Apple Speech or WhisperKit and either pasted
  into the focused app or copied to the clipboard. A floating HUD pill
  shows live partial text.
- **Crash recovery** — on launch the app scans the recordings folder
  for orphan in-progress folders and registers them as recovered
  recordings.
- **Audio import** — drop audio files (WAV, MP3, M4A, AIFF, FLAC, CAF)
  onto the Recordings window or use the Import toolbar button to add
  existing files to the library; they can then be transcribed, tagged, summarized and
  searched for.

### Library window

- Three-pane split view: sidebar (All / Starred / Ungrouped / Projects /
  Tags) → recording list → detail view.
- **Search** across titles, source labels, app bundle IDs, tags, summaries, and
  transcript bodies — matching transcript snippets show inline under
  the row.
- **Filter** by date range (All / Today / Week / Month) and **sort** by
  date / duration / title.
- **Star** recordings to keep them past the retention window.
- **Tags** — add, remove, search by, and (with a configured summary
  provider) ask the LLM to suggest tags from the transcript.
- **Drag-and-drop** between projects in the sidebar — folders move on
  disk to follow the new project slug.
- **Empty/broken track detection** — an orange warning on a row or in
  the detail view means one of the WAV files is empty (≤ 44 bytes, just
  a header). Export options that depend on a missing track are
  disabled.
- **Playback** with a follow-along player that supports system, mic, or
  any combined layout.

### Transcription

Settings → Transcription. Each project can override the global provider
or model in Settings → Projects.

Supported backends:

- **WhisperKit** (local, Apple Silicon) — pick a model variant; nothing
  leaves the device.
- **Apple SpeechAnalyzer** (local) — no API key, no network. Per-locale
  models download on first use.
- **AssemblyAI** (cloud) — speaker diarization, language detection,
  custom prompts.
- **Deepgram** (cloud) — Nova-3 / Nova-2, diarization, key-term
  prompting.
- **OpenAI** (cloud) — `gpt-4o-transcribe` / `gpt-4o-mini-transcribe` /
  `whisper-1` (only `whisper-1` returns segment timestamps).
- **Groq** (cloud) — Whisper Large v3 / Turbo on Groq's LPU, very fast.
- **Google Cloud Speech-to-Text v2** (cloud) — service-account JSON +
  project ID; sync recognize is capped at 60 s / 10 MB per request.

API keys live in the macOS Keychain. Post-processing options (strip
speaker tags, remove fillers, recapitalize, rename diarized A/B labels)
are applied before the transcript is shown or exported.

Transcripts can be **exported** as `.txt`, `.md`, `.srt`, or `.vtt` —
the timestamped formats are only available when the provider returned
segment timing.

### Summaries & tag suggestions

Settings → Summary. Pick one of:

- **Anthropic** (Claude Haiku / Sonnet / Opus)
- **OpenAI** (Chat Completions; reuses the transcription OpenAI key)
- **OpenRouter** (one key, many providers — model is `provider/model`)
- **Ollama** (local, requires a running Ollama instance with a pulled
  model)
- **OpenAI API compatible** (custom URL and API key)

Each recording gets a Summary pane in the detail view, plus a "Suggest"
button on the tags row that uses the same provider to pull tag
candidates out of the transcript.

### Thoughts

Settings → Thoughts. A second dictation pipeline for capturing
fleeting ideas without losing flow. Press one of two dedicated global
hotkeys — hold-to-capture or tap-to-toggle — speak, and Stenobar:

1. Transcribes the utterance locally (same engine as the dictation
   hotkey).
2. Sends the transcript to a **router** that classifies it as one of
   four categories: Task, Note, Reminder, or Review later.
3. **Routes** the result to whichever destination you've mapped to that
   category, with the structured fields (title, body, due date) the
   destination wants.

Routers:

- **Apple Intelligence** (on-device, default; requires a model-capable
  Mac and Apple Intelligence enabled in System Settings).
- **Anthropic**, **OpenAI**, **OpenRouter**, **Ollama** (local) as
  fallbacks.

Destinations:

- **Apple Reminders** and **Apple Notes** (native, no API key needed —
  uses EventKit and AppleScript respectively).
- **Todoist** (REST API; token in Keychain).
- **macOS Shortcuts** (any Shortcut you have installed — Stenobar
  passes the transcript and classification as input).
- **Stenobar Inbox** — a local triage queue, always available.

Three **confirmation modes** trade speed for control:

- **Silent** — send immediately, show a "Sent → Reminders ✓" toast.
- **Grace window** (default) — 2.5-second confirmation banner with
  Esc-to-cancel and ⌘E-to-edit before the send commits.
- **Always preview** — open an editable sheet (destination, title,
  body, due date) and wait for ⏎. Per-category overrides let you mix,
  e.g. preview reminders but send tasks silently.

Every captured thought is persisted in an **Inbox window** with full
status (sent / pending / failed / review-later), classifier output,
and a deep link back to the destination item where the destination
provides one. Failed sends retry with exponential backoff and can be
re-routed manually from the Inbox.

The Thoughts router is *separate* from the Summary provider — you can
mix and match, e.g. Apple Intelligence for routing thoughts and
Anthropic for transcript summaries.

### Storage & retention

- Default location: `~/Documents/Stenobar/recordings/`. Customizable
  from **Settings → General → Recordings folder** (older builds used
  Application Support and that location is preserved on upgrade).
- SwiftData store: `library.sqlite` next to the recordings folder.
- **Auto-delete retention** — optionally Trash unstarred recordings
  older than N days. Starred recordings are always kept; deletions go
  to the macOS Trash so they can be recovered.
- **Diagnostics log** — always-on errors and lifecycle events plus an
  optional verbose toggle (Settings → Diagnostics) write to
  `~/Library/Application Support/Stenobar/diagnostics.log`. The
  Diagnostics tab can reveal or clear it.

## Privacy

- Recordings stay on your Mac unless you choose a cloud transcription,
  summary, or Thoughts router provider. Local options (WhisperKit,
  Parakeet, Apple SpeechAnalyzer, Apple Intelligence, Ollama) never
  send audio or text off the device.
- API keys for cloud providers (and the Todoist destination token) are
  stored in the macOS Keychain, not in plain text.
- Cloud transcription uploads only the audio for the recording you ask
  to transcribe; it doesn't sync your library.
- Per-provider data-flow disclosures are shown inline in onboarding
  and in each Settings tab — see the **Privacy** info box wherever a
  cloud provider is selected.

See [PRIVACY.md](PRIVACY.md) for the full per-provider data-flow
breakdown, retention policies, and how to delete your data, or read it
online at [stenobar.app/privacy](https://www.stenobar.app/privacy).

## Feedback

Stenobar is in active beta. If you hit something on a real recording
setup, please [open an issue](https://github.com/narrowstacks/stenobar-releases/issues).

## License

See [LICENSE.md](LICENSE.md), or read it online at
[stenobar.app/license](https://www.stenobar.app/license).

---

Made by [narrowstacks](https://www.stenobar.app/) · [stenobar.app](https://www.stenobar.app/)
