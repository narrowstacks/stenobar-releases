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

## macOS permissions Stenobar requests

| Permission | Why | Required? |
|---|---|---|
| Microphone | To record your voice | Yes, if microphone capture is enabled |
| Screen Recording | ScreenCaptureKit needs this to capture system audio. No video is recorded. | Yes |
| Speech Recognition | Powers the live transcript inside the dictation HUD when using Apple's on-device model | Optional |
| Accessibility | Lets dictation paste the result into the focused app. Without it, dictation falls back to clipboard copy. | Optional |
| Notifications | Banner when a transcription or summary completes in the background | Optional |
| Login Items | "Open Stenobar at login" toggle — registered through `SMAppService` | Optional |

You can revoke any of these in System Settings → Privacy & Security at any time.

## Diagnostics

Stenobar writes a local diagnostics log at `~/Library/Application Support/Stenobar/diagnostics.log`. This file stays on your machine. It is never uploaded. Verbose diagnostics are off by default and can be toggled in Settings.

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
