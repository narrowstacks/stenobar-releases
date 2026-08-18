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
3. Open it. That's it — no Terminal commands and no Gatekeeper detour.

Every build is signed with an Apple Developer ID certificate and
notarized by Apple, with the notarization ticket stapled to the DMG, so
macOS verifies it offline on first launch. (Builds before 1.0.0-beta.10
were unsigned and needed the old `xattr` workaround; that no longer
applies, and updating from one of those builds clears it.)

Updates arrive automatically through Sparkle. **Settings → General →
Software Update** controls whether it checks automatically and whether
you're on the beta channel — turn **Receive beta updates** on to get
`1.0.0-beta.N` builds as they ship, off to sit on stable releases only.

## Requirements

- macOS 26 or newer (the deployment target is macOS 26 so that Apple
  Intelligence / Foundation Models are available unconditionally for
  the Thoughts router)
- Apple Silicon recommended — required for the local transcription
  engines (Parakeet, WhisperKit) and for the on-device Apple
  Intelligence router and summarizer. Everything else runs fine on
  Intel.

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
- **Hold-to-dictate** — hold a global hotkey (or just one modifier key),
  speak, release. Stenobar transcribes and pastes the text into whatever
  app you're using — on-device by default, with streaming cloud engines
  available if you want them.
- **Thoughts** — a second dictation hotkey for fleeting ideas: speak,
  release, and Stenobar transcribes, uses an LLM (Apple Intelligence
  on-device by default, or your choice of Anthropic / OpenAI /
  OpenRouter / Ollama) to classify what you said as a task, note,
  reminder, or "review later," and routes it to the right destination —
  Apple Reminders, Apple Notes, Todoist, TickTick, Things, Obsidian,
  Bear, Notion, a macOS Shortcut, or a local Inbox.
- **Bring your own transcription** — pick a local engine (Parakeet,
  WhisperKit, Apple Speech) or a cloud one (AssemblyAI, Deepgram,
  OpenAI, Groq, Google). Keys are stored in the macOS Keychain.
- **Bring your own summaries** — Apple Intelligence on-device, or
  Anthropic, OpenAI, OpenRouter, a local Ollama instance, or any
  OpenAI-compatible endpoint. Swap and compare providers any time.
- **Searchable library** — full-text search across titles, tags, and
  transcript bodies; filter by date, sort, star, project, tag.
- **Scriptable** — a `stenobar://` URL scheme, App Intents for
  Shortcuts and Siri, and a JSON library index for Raycast, Alfred, or
  your own scripts.

## First run

On first launch the onboarding window will:

1. Request **Microphone** and **Screen Recording** permission (Screen
   Recording is what macOS calls the entitlement that lets an app
   capture system audio). Optionally, request **Accessibility** permission to
   allow the app to paste dictation results into the active app.
2. Let you pick where recordings should be saved (defaults to
   `~/Documents/Stenobar/recordings/`).
3. Set up **projects**, which are organized folders that group related
   recordings.
4. Optionally set up keyboard shortcuts for starting and stopping
   recordings, dropping markers, dictation, and Thoughts capture.
5. Configure your **transcription**, **dictation**, and **summary**
   providers — you can skip this and come back to it from **Settings**
   at any time.
6. Optionally configure **Thoughts** — pick a router (Apple Intelligence
   by default, or Anthropic / OpenAI / OpenRouter / Ollama) and the
   defaults for where tasks, notes, reminders, and review-later items
   should go.
7. Run a short test recording to confirm the capture pipeline works
   end to end before you rely on it.

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
- **Presets** — save a source + microphone + project combination by
  name and pick it from the menu bar next time instead of rebuilding
  the setup.
- **Projects** — assign a recording to a project at start time. Projects
  map to on-disk subfolders (e.g.
  `recordings/<project-slug>/<UUID>/`) and can carry per-project
  transcription and summary overrides.
- **Crash-safe capture** — always writes raw PCM WAV (`system.wav` +
  `mic.wav`) and flushes the WAV header every ~5 s, so a crash leaves
  usable audio behind. Format conversion (M4A / FLAC) happens at export
  time.
- **Configurable format** — 16/24/44.1/48 kHz, mono or stereo, set in
  **Settings → Recording → Format**.
- **Combined-track stitching** — produces a third `combined.wav` from
  system + mic, with a configurable layout: Mono, Stereo (L=System,
  R=Mic), or Stereo Mixed Down. Re-stitchable from the recording detail
  view if you change the default later.
- **Markers** — a **Drop marker** hotkey flags the current moment while
  you record; markers show up as tappable pins on the playback
  waveform, so "wait, say that again" is one keystroke instead of a
  note to yourself.
- **Global hotkey** (Settings → Shortcuts) — toggle recording from
  anywhere with the last-used source and mic.
- **Dictation and Thoughts hotkeys** (Settings → Shortcuts) — each can
  be held, tapped to toggle, or bound to a single modifier key (hold
  right-Option, say) instead of a chord; pressing any other key while
  it's down cancels silently, so your normal shortcuts keep working.
  See [Dictation](#dictation) and [Thoughts](#thoughts) below.
- **Crash recovery** — on launch the app scans the recordings folder
  for orphan in-progress folders and registers them as recovered
  recordings.
- **Audio import** — drop audio files (WAV, MP3, M4A, AIFF, FLAC, CAF)
  onto the Recordings window or use the Import toolbar button to add
  existing files to the library; they can then be transcribed, tagged, summarized and
  searched for. A pair of files can be imported as one recording's
  system and mic tracks.

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
- **Multi-select** rows to star, tag, move between projects, or delete
  in one pass.
- **Drag-and-drop** between projects in the sidebar — folders move on
  disk to follow the new project slug.
- **Empty/broken track detection** — an orange warning on a row or in
  the detail view means one of the WAV files is empty (≤ 44 bytes, just
  a header). Export options that depend on a missing track are
  disabled.
- **Playback** with a waveform scrubber (markers included) that follows
  along with the transcript, plays system, mic, or any combined layout,
  and keeps going in a mini player while you browse other recordings.
- **Find and replace** inside a transcript or summary, for fixing a
  misheard name everywhere at once.

### Transcription

Settings → Transcription. Each project can override the global provider
or model in Settings → Projects, and **Auto-transcribe when recording
ends** hands off automatically as soon as you stop.

Supported backends:

- **Parakeet** (local, Apple Silicon) — NVIDIA's Parakeet via
  FluidAudio; fast, fully on-device, and the default. Optional
  on-device speaker labels and per-line timestamps (one-time ~35 MB
  model download).
- **WhisperKit** (local, Apple Silicon) — pick a model variant; nothing
  leaves the device.
- **Apple Speech** (local) — no API key, no network. Per-locale
  models download on first use.
- **AssemblyAI** (cloud) — speaker diarization, language detection, and
  an optional **Transcribe channels separately** mode that sends system
  and mic as two channels so your own voice is reliably labelled
  **Me** instead of guessed at.
- **Deepgram** (cloud) — Nova-3 / Nova-2, diarization, key-term
  prompting (keyword boosting on the older models).
- **OpenAI** (cloud) — `gpt-4o-transcribe` / `gpt-4o-mini-transcribe` /
  `whisper-1` (only `whisper-1` returns segment timestamps).
- **Groq** (cloud) — Whisper Large v3 / Turbo on Groq's LPU, very fast.
- **Google Cloud Speech-to-Text v2** (cloud) — service-account JSON +
  project ID; sync recognize is capped at 60 s / 10 MB per request.

**Context** and **Key terms** are separate fields: a sentence or two
describing the recording goes in one, the names, product names, and
jargon you want recognised go in the other, and each is sent through
the provider's proper channel. Remaining term/word/token budget is
shown as you type, in that provider's own units. Both can be set
globally, per project, or per recording.

API keys live in the macOS Keychain. Post-processing options (strip
speaker tags, remove fillers, recapitalize, rename diarized A/B labels)
are applied before the transcript is shown or exported.

Transcripts can be **exported** as `.txt`, `.md`, `.srt`, or `.vtt` —
the timestamped formats are only available when the provider returned
segment timing.

### Summaries & tag suggestions

Settings → Summary. Pick one of:

- **Apple Intelligence** (on-device, no key, no network — requires a
  model-capable Mac with Apple Intelligence enabled)
- **Anthropic** (Claude Haiku / Sonnet / Opus)
- **OpenAI** (Chat Completions; reuses the transcription OpenAI key)
- **OpenRouter** (one key, many providers — model is `provider/model`)
- **Ollama** (local, requires a running Ollama instance with a pulled
  model)
- **OpenAI API compatible** (custom URL and API key)

Each recording gets a Summary pane in the detail view, plus a "Suggest"
button on the tags row that uses the same provider to pull tag
candidates out of the transcript.

- **Prompt library** — save named summary prompts ("meeting minutes",
  "interview pull-quotes", "lecture outline"), set one as a project's
  default, or pick one ad-hoc for a single run.
- **Per-project provider and model**, plus a separate, usually cheaper
  model for tag and title suggestions.
- **Per-recording key terms and context** so the summarizer knows the
  names and jargon in that particular recording.

### Dictation

Speak into whatever app you're already in. Hold the dictation hotkey (or
tap the toggle one, or hold a single modifier key like right-Option),
say your sentence, and let go — the transcript lands in the focused
text field. It never touches the library: dictation audio and text are
transient, and nothing is written to disk.

A floating HUD pill appears above every app while you talk, showing a
live waveform and partial text as it comes in. It never steals focus,
follows you across Spaces and to whichever screen your cursor is on,
and **Esc** cancels a take that's still transcribing.

**Where the text goes** (Settings → Dictation): paste into the focused
app, or copy to the clipboard only. Pasting synthesizes ⌘V and needs
Accessibility permission — without it the transcript still lands on the
clipboard, so a manual ⌘V works.

**Which engine transcribes it** (Settings → Speech, shared with
Thoughts). This is deliberately separate from the Library's
transcription provider, so a small fast English model can handle
dictation while a large multilingual one handles archived recordings —
they share the same on-disk model cache:

- **Parakeet** (local, default) — plus a one-time ~120 MB streaming
  bundle that drives the live HUD preview. The preview is English-only;
  other languages still get a correct final transcript, just no live
  text.
- **WhisperKit** (local) — its own model variant, independent of the
  Library's.
- **Apple Speech** (local) — no key, no network.
- **AssemblyAI** (cloud, streaming) — low-latency partials over a live
  socket.
- **Deepgram** (cloud, streaming) — Flux, Nova-3, or Nova-3 Medical.

Each engine gets its own key-terms / vocabulary list, kept separate
from the Library's so meeting jargon doesn't bias every dictated
sentence. Dictation also has its own microphone selection, independent
of what recordings capture.

Local models stay loaded between takes and start warming at launch, so
there's no long stall on the first dictation of the session. Takes
under 0.3 s are discarded as slips of the finger, and a transcription
that never comes back gives up after 60 seconds rather than leaving
dictation wedged until you relaunch.

Dictation can also be fired from a Shortcut, from Siri, or with
`stenobar://dictate` — see [Automation & integrations](#automation--integrations).

### Thoughts

Settings → Thoughts. A second dictation pipeline for capturing
fleeting ideas without losing flow. Press one of the dedicated global
hotkeys — hold-to-capture, tap-to-toggle, or a single held modifier
key — speak, and Stenobar:

1. Transcribes the utterance with the dictation speech engine
   (Settings → Speech).
2. Sends the transcript to a **router** that classifies it as one of
   four categories: Task, Note, Reminder, or Review later.
3. **Routes** the result to whichever destination you've mapped to that
   category, with the structured fields (title, body, due date) the
   destination wants. A thought that is plainly an enumeration —
   "shopping list: milk, eggs, coffee" — arrives as a real list in
   destinations that have one.

Routers:

- **Apple Intelligence** (on-device, default; requires a model-capable
  Mac and Apple Intelligence enabled in System Settings).
- **Anthropic**, **OpenAI**, **OpenRouter**, **Ollama** (local) as
  fallbacks.

Destinations:

- **Apple Reminders** and **Apple Notes** (native, no API key needed —
  uses EventKit and AppleScript respectively).
- **Todoist**, **TickTick** (sign in once with OAuth; tokens live in
  the Keychain).
- **Things** and **Bear** (via their own URL schemes — no account
  needed, just the app installed).
- **Obsidian** — a new note in a vault folder, or appended as a section
  to a running daily/inbox file.
- **Notion** — connect your workspace with OAuth and send pages to a
  chosen database or page.
- **macOS Shortcuts** (any Shortcut you have installed — Stenobar
  passes the transcript and classification as input).
- **Stenobar Inbox** — a local triage queue, always available.

Connections are managed from **Settings → Integrations**, which shows
each service's status in one place.

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
re-routed manually from the Inbox; thoughts interrupted by a quit or
crash are recovered on the next launch.

The Thoughts router is *separate* from the Summary provider — you can
mix and match, e.g. Apple Intelligence for routing thoughts and
Anthropic for transcript summaries.

### Automation & integrations

Stenobar can be driven by external tools (Shortcuts, Siri, Spotlight, a
Raycast extension, Alfred, shell scripts) through three surfaces:

- **App Intents** — start, stop, or toggle a recording, dictate,
  capture a thought, or fetch the last recording from Shortcuts, Siri,
  and Spotlight.
- **`stenobar://` URL scheme** — open the library, jump to a recording
  (`stenobar://recording/<uuid>`), open a project or the Thoughts
  Inbox, or fire a feature the same way a hotkey would
  (`stenobar://record/toggle`, `stenobar://dictate`,
  `stenobar://thought`, `stenobar://marker`). Right-click a recording →
  **Copy Link** to grab its deep link.
- **Library index** — a JSON snapshot of recordings and thoughts at
  `~/Library/Application Support/Stenobar/index.json`, refreshed on
  every change, for tools that need to *search* (the URL scheme can't
  return data). Each entry includes its `stenobar://` deep link.

### Storage & retention

- Default location: `~/Documents/Stenobar/recordings/`. Customizable
  from **Settings → Library → Recordings folder** (older builds used
  Application Support and that location is preserved on upgrade).
- SwiftData store: `library.sqlite` next to the recordings folder.
- **Library size** — see what the library is using on disk and
  optionally re-encode existing recordings to a lossless **FLAC** or
  **ALAC** container. Capture keeps writing WAV either way; this only
  re-compresses files already on disk, replacing each atomically and
  keeping the original if it wouldn't actually shrink.
- **Auto-delete retention** — optionally Trash unstarred recordings
  older than N days. Starred recordings are always kept; deletions go
  to the macOS Trash so they can be recovered.
- **Diagnostics log** — always-on errors and lifecycle events plus an
  optional verbose toggle (Settings → Diagnostics) write to
  `~/Library/Application Support/Stenobar/diagnostics.log`. The
  Diagnostics tab can reveal or clear it.

## Privacy

- Recordings stay on your Mac unless you choose a cloud transcription,
  summary, or Thoughts router provider. Local options (Parakeet,
  WhisperKit, Apple Speech, Apple Intelligence, Ollama) never send
  audio or text off the device.
- API keys for cloud providers (and the Todoist / TickTick / Notion
  connections) are stored in the macOS Keychain, not in plain text.
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

An iOS companion app is in closed TestFlight and isn't publicly
available yet.

## License

See [LICENSE.md](LICENSE.md), or read it online at
[stenobar.app/license](https://www.stenobar.app/license).

---

Made by [narrowstacks](https://www.stenobar.app/) · [stenobar.app](https://www.stenobar.app/)
