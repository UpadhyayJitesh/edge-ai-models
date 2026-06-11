# edge-ai-models

OTA model catalog and model artifacts for the TANUH_AI Smart Voice Notes Android
demo.

Repository:
[`UpadhyayJitesh/edge-ai-models`](https://github.com/UpadhyayJitesh/edge-ai-models)

The Android application installs without AI model files. On first setup it
downloads this repository's `model-manifest.json`, verifies each artifact, and
caches the models in app-private storage for fully on-device inference.

## Hosted models

| Model | Purpose | Runtime | Format | Size |
| --- | --- | --- | --- | ---: |
| Vosk Small English 0.15 | Offline streaming speech-to-text | Vosk Android | ZIP | 41,205,931 bytes |
| MobileBERT text classifier v1 | Positive/negative transcript sentiment | MediaPipe Tasks Text | TFLite | 25,707,538 bytes |

The MobileBERT model performs binary sentiment analysis. It does not identify
memo intents such as reminders, tasks, ideas, or notes.

## OTA manifest

The application reads:

```text
https://raw.githubusercontent.com/UpadhyayJitesh/edge-ai-models/main/model-manifest.json
```

Each model entry defines:

```json
{
  "id": "mobilebert-text-classifier",
  "version": "1",
  "runtime": "mediapipe-text",
  "format": "tflite",
  "url": "https://raw.githubusercontent.com/UpadhyayJitesh/edge-ai-models/main/releases/download/voice-memo-v1/mobilebert-text-classifier-v1.tflite",
  "sha256": "9b45012ab143d88d61e10ea501d6c8763f7202b86fa987711519d89bfa2a88b1",
  "size": 25707538
}
```

- `id`: stable identifier used by the Android application.
- `version`: version compared with the locally active model.
- `runtime`: Android inference adapter required to load the artifact.
- `format`: artifact packaging or file format.
- `url`: direct HTTPS download URL.
- `sha256`: expected SHA-256 digest of the complete artifact.
- `size`: expected byte count.

## Repository layout

```text
edge-ai-models/
|-- README.md
|-- model-manifest.json
`-- releases/download/voice-memo-v1/
    |-- vosk-model-small-en-us-0.15.zip
    `-- mobilebert-text-classifier-v1.tflite
```

## Integrity metadata

| Artifact | SHA-256 |
| --- | --- |
| `vosk-model-small-en-us-0.15.zip` | `30f26242c4eb449f948e42cb302dd7a686cb29a3423a8367f99ff41780942498` |
| `mobilebert-text-classifier-v1.tflite` | `9b45012ab143d88d61e10ea501d6c8763f7202b86fa987711519d89bfa2a88b1` |

The Android model manager checks both the declared byte size and SHA-256 before
staging and activating a model. An invalid or interrupted download is never
marked active.

## Direct download URLs

Model consumers must use `raw.githubusercontent.com` URLs. GitHub links
containing `/blob/` return an HTML page, not the model bytes.

```text
https://raw.githubusercontent.com/UpadhyayJitesh/edge-ai-models/main/releases/download/voice-memo-v1/vosk-model-small-en-us-0.15.zip

https://raw.githubusercontent.com/UpadhyayJitesh/edge-ai-models/main/releases/download/voice-memo-v1/mobilebert-text-classifier-v1.tflite
```

## Publishing an update

1. Add the new artifact without overwriting the currently active version.
2. Calculate its exact byte size and SHA-256.
3. Add or update its manifest entry with a higher `version`.
4. Confirm the raw artifact URL returns the expected bytes.
5. Publish the manifest change only after the artifact is available.

Keeping the old artifact available preserves the option for last-known-good
operation and future rollback support.

## Consumer

The models are consumed by the
[`UpadhyayJitesh/TANUH_AI`](https://github.com/UpadhyayJitesh/TANUH_AI)
repository's `TANUHDemo` Android project. Vosk transcribes microphone audio,
then MobileBERT analyzes the transcript's sentiment. Both inference stages run
locally after OTA preparation.
