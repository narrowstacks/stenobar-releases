# Privacy Policy

**Effective date:** 2026-05-10
**Publisher:** narrowstacks, the maintainers of Stenobar ("we", "our", "us")
**Contact:** aaron+stenobar@affords.art · [open an issue](https://github.com/narrowstacks/stenobar/issues)

This policy describes what data Stenobar handles and what we, the publisher, do with it.

**The two things we want to be explicit about up front:**

1. **We do not retain your audio.** Stenobar is a local-first macOS application. Your recordings, transcripts, and summaries are saved to your own Mac and are never transmitted to any server operated by us. We have no servers, no backend, no accounts, no analytics, and no telemetry. We could not retain your audio even if we wanted to, because Stenobar never sends it to us.
2. **Any audio or transcripts you send to third-party transcription or summary services are subject to those services' own privacy policies, not ours.** When you choose a cloud provider in Settings, Stenobar transmits the relevant data directly from your Mac to that provider, using credentials you supply. We have no relationship with the provider, no visibility into the request, and no ability to control or audit their retention.

The rest of this document elaborates.

## What we collect from you

**Nothing.** Stenobar makes no network calls to any server we operate. We do not run a backend, we do not have user accounts, we do not have analytics, telemetry, crash reporting, or any other channel for your data to reach us.

## What Stenobar captures on your device

While running, Stenobar can capture:

- **System audio** via ScreenCaptureKit, optionally filtered to specific apps you choose.
- **Microphone audio** from the input device you select. Optional.
- **No video.** ScreenCaptureKit is used for its audio path; video frames are never read, written, or transmitted.

Recordings are saved as raw 16-bit PCM WAV files in the folder you choose during onboarding (default: `~/Documents/Stenobar/recordings/`). You can change the location at any time in Settings → General.

## Meeting detection

Stenobar can notice when you join a call and offer to record it. New installations ask by default; an installation upgraded from an earlier version keeps the previous behaviour, which only fills in the source when you open the recording panel. Either way it can be switched off entirely in Settings → Recording → Source ("Do nothing"), which stops every read described below.

To decide whether a call is happening, Stenobar reads three things about **other applications on your Mac**:

- **Which processes are using the microphone**, via CoreAudio's process properties. This reports only that an app has the microphone open, never any audio from it. Stenobar does not listen to, capture, or analyse other apps' microphone input.
- **Window titles of those apps**, via ScreenCaptureKit, under the Screen Recording permission Stenobar already holds to capture system audio. This is what distinguishes a video call from a browser tab playing music. **No screen contents, screenshots, or video frames are read**, only the title string.
- **Whether the camera is in use**, as a single system-wide yes/no flag. macOS does not attribute this to a process, and Stenobar never accesses the camera itself. It has no camera permission and requests none.

Stenobar does **not** read browser tab contents, page URLs, or anything requiring Accessibility permission for this feature.

All three signals are evaluated on your Mac and used only to decide whether to show the prompt. **None of them is transmitted anywhere, and window titles are never written to the diagnostics log.** The log records only the verdict (call, waiting room, not a call) and the app's bundle identifier, because a title can name a person or an appointment.

### Optional: naming the meeting from your calendar

Off by default. If you connect your calendar in Settings → Calendar, Stenobar reads events from the macOS Calendar app so the prompt can name the meeting rather than only the app. This covers whatever accounts you have added to Calendar, including Google, Outlook/Exchange and other CalDAV accounts, because macOS syncs them into the same local database. Stenobar does not connect to Google, Microsoft, or any calendar provider directly, and holds no account credentials.

- **You choose which calendars are read.** Settings → Calendar lists them grouped by account. Stenobar asks the system only for events in the calendars you ticked, so an unticked calendar is never read, not merely filtered out afterwards. Ticking nothing reads nothing.
- **Disconnecting clears every calendar setting**, including which calendars were selected, so reconnecting starts fresh. It cannot revoke the permission macOS granted; that stays until you remove it in System Settings → Privacy & Security → Calendars, and the pane says so.

- **Calendar access is requested only when you press Connect, and never at launch or during onboarding.** Leave it disconnected and Stenobar never asks, never creates a connection to the calendar database, and reads nothing from it.
- Only events within 15 minutes of the call are considered, and only events with two or more participants, so a block you put on your own calendar is not treated as a meeting. Settings → Calendar can relax the participant rule for events that carry a meeting link, for appointments booked by the other side.
- **A call is only named when the calendar corroborates it.** Stenobar compares the meeting service named in the window title against the service the event's link points at. If they disagree, or if it cannot tell, the prompt names the app instead. It never assumes a call is a meeting just because that meeting is scheduled nearby.
- For each such event Stenobar reads the title, the start and end times, the number of participants, and whether the URL, notes or location field contains a link to a conferencing service. It reads no other event content, no participant names or addresses, and nothing outside that 15-minute window. It never writes to your calendar.
- The meeting name is used only to label the prompt on screen. **It is never written to the diagnostics log and never transmitted anywhere.** The log records only whether a name was found.

#### Optional: reminders before a meeting starts

A second setting, "Remind me before a meeting starts", is also off by default and requires the calendar to be connected. When it is on, Stenobar offers to join and record a meeting shortly before it begins, at a lead time you choose.

- This is the one case where Stenobar looks at your calendar while nothing is using the microphone, because a reminder has to arrive before the call does. That read happens at most once every 30 seconds, only while the reminder setting is on, and never at all when meeting detection is set to "Do nothing".
- The reminder appears only for events that pass both filters: two or more participants, and a link to a conferencing service in the event's URL, notes or location field.
- Choosing "Open and record" opens that link in your default browser or app, exactly as clicking it in Calendar would, and arms Stenobar to record once it detects the call has started. **Nothing is recorded before the call is detected.**
- While Stenobar is waiting for the meeting, its name is shown in the menu bar's tooltip and in the menu itself. Both are on-screen only.
- The meeting's name is never written to the diagnostics log. The log records only how many minutes away the meeting was and whether it carried a link.

## Third-party transcription and summary providers

Stenobar lets you select a transcription backend (Settings → Transcription) and a summary backend (Settings → Summary). Some run entirely on your Mac; some are cloud services operated by third parties.

**If you select a cloud provider, the relevant data — audio for transcription, transcript text for summarization — is transmitted directly from your Mac to that provider over HTTPS, authenticated with credentials you supply. That data is governed by the third-party provider's privacy policy and terms, not ours.** Stenobar is the messenger; the provider is the data processor. To understand how a given provider retains, uses, or shares your data, read their privacy policy, linked below.

### Local providers (data stays on your Mac)

- **WhisperKit** — runs Whisper locally via Apple's MLX. Models are downloaded from Hugging Face once, then run entirely on-device.
- **Parakeet** — runs NVIDIA Parakeet TDT locally via [FluidAudio](https://github.com/FluidInference/FluidAudio). Models are downloaded from Hugging Face once, then run entirely on-device.
- **Apple Speech** — uses Apple's on-device speech recognizer.
- **Ollama (summary)** — calls a locally-running Ollama instance (default `http://localhost:11434`).

### Cloud providers (data leaves your Mac)

When you select one of these, you are sending data to a third party governed by their own policy:

| Provider | Endpoint | Sent | Provider's privacy policy |
|---|---|---|---|
| AssemblyAI | `api.assemblyai.com` | Audio | https://www.assemblyai.com/legal/privacy-policy |
| Deepgram | `api.deepgram.com` | Audio | https://deepgram.com/privacy |
| OpenAI (transcription) | `api.openai.com` | Audio | https://openai.com/policies/row-privacy-policy/ |
| Groq | `api.groq.com` | Audio | https://groq.com/privacy-policy/ |
| Google Cloud | `speech.googleapis.com` | Audio | https://cloud.google.com/terms/data-processing-addendum |
| OpenAI (summary) | `api.openai.com` | Transcript text | https://openai.com/policies/row-privacy-policy/ |
| Anthropic | `api.anthropic.com` | Transcript text | https://www.anthropic.com/legal/privacy |
| OpenRouter | `openrouter.ai` | Transcript text | https://openrouter.ai/privacy (plus the upstream model provider you select, each with its own policy) |
| Custom OpenAI-compatible endpoint | URL you configure | Transcript text | Whatever that endpoint publishes — we cannot know in advance |

API keys you provide for these services are stored in your macOS Keychain, scoped to Stenobar (service prefix `com.narrowstacks.Stenobar`). They are not transmitted anywhere except to the corresponding provider when you make a request.

## Connecting accounts (OAuth)

Some Thoughts destinations (Notion, TickTick) connect with OAuth instead of a pasted API key. Two of these providers require an HTTPS redirect URL and will not redirect to a local address, so the sign-in flow routes through a small page we host at `https://stenobar.app/oauth/<provider>`.

**What this page does and does not see:**

- When you approve access, the provider redirects your browser to that page with a **one-time authorization code**. That code momentarily passes through stenobar.app's hosting (and may appear in standard edge/CDN request logs) before the page hands it back to Stenobar running on your Mac.
- The page **never receives your access token, and never receives any of your account data.** It forwards only the short-lived code.
- The actual exchange — swapping that code for an access token — happens **directly from your Mac to the provider**. The resulting token is stored only in your macOS Keychain. The code is single-use and useless without the application's client secret, which is built into the app and never leaves your Mac.

This is the one point where our infrastructure touches the connect flow at all; it carries no audio, no transcripts, no tokens, and no readable account data.

## macOS permissions Stenobar requests

| Permission | Why | Required? |
|---|---|---|
| Microphone | To record your voice | Yes, if microphone capture is enabled |
| Screen Recording | ScreenCaptureKit needs this to capture system audio, and to read window titles for meeting detection. No video is recorded and no screen contents are read. | Yes |
| Speech Recognition | Powers the live transcript inside the dictation HUD when using Apple's on-device model | Optional |
| Accessibility | Lets dictation paste the result into the focused app. Without it, dictation falls back to clipboard copy. | Optional |
| Notifications | Banner when a transcription or summary completes in the background | Optional |
| Login Items | "Open Stenobar at login" toggle — registered through `SMAppService` | Optional |

You can revoke any of these in System Settings → Privacy & Security at any time.

## Diagnostics

Stenobar writes a local diagnostics log at `~/Library/Application Support/Stenobar/diagnostics.log`. This file stays on your machine. It is never uploaded. Verbose diagnostics are off by default and can be toggled in Settings. Window titles read for meeting detection are never written to it.

## Deleting your data

Because we hold none of your data, "deletion" means deleting it from your own machine:

- **Recordings, transcripts, summaries** — delete from the Recordings library, or remove the recording folder from your configured storage path.
- **API keys** — Settings → Transcription / Summary, clear the field and Save. Or remove via Keychain Access.app under the service prefix `com.narrowstacks.Stenobar`.
- **Diagnostics log** — delete `~/Library/Application Support/Stenobar/diagnostics.log`.

If you have previously sent audio or transcripts to a third-party cloud provider, you must contact that provider directly to request deletion from their systems — we have no access.

## Changes to this policy

If we change this policy, the updated version will be published at the same location with a new effective date. Material changes will also be mentioned in the release notes for the version that introduces them.

## Contact

Questions about this policy: aaron+stenobar@affords.art. You can also open an issue at https://github.com/narrowstacks/stenobar/issues.
