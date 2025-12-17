---
title: Text-to-speech and speech-to-text
parent: Installation
has_children: false
nav_order: 3
---

# Text-to-Speech (TTS) and Speech-to-Text (STT)

The TTS and STT engines are optional components for AarhusAI, fully supported by Open WebUI. These components are
optional because they require a dedicated GPU to run.

## Installation

Deploy these services from the following repositories or use any solution that supports the OpenAI Audio API:

* [https://github.com/AarhusAI/whisper-api](https://github.com/AarhusAI/whisper-api) - Speech-to-Text
* [https://github.com/AarhusAI/piper-tts](https://github.com/AarhusAI/piper-tts) - Text-to-Speech

See the links above for detailed installation instructions and configuration options.

## Configuration

### Create and Seal the Secrets

First, add the secrets for the TTS and STT API keys to `local-secrets/openwebui-secrets.yaml` and seal it:

```bash
kubectl create -f local-secrets/openwebui-secrets.yaml --dry-run=client -o yaml | kubeseal --format yaml > templates/sealed-openwebui-secrets.yaml
```

### Update Open WebUI Configuration

Edit `applications/openwebui/values.yaml` in the Helm chart and add the following environment variables:

```yaml
# STT (whisper) - OPTIONAL IF YOU WANT TO USE SPEECH-TO-TEXT (https://github.com/AarhusAI/whisper-api)
- name: AUDIO_STT_ENGINE
  value: "openai"
- name: AUDIO_STT_OPENAI_API_BASE_URL
  value: "<YOUR_WHISPER_API_URL>"  # e.g., https://whisper.example.com/
- name: AUDIO_STT_OPENAI_API_KEY
  valueFrom:
    secretKeyRef:
      name: openwebui-secrets
      key: STT_API_KEY

# TTS (piper) - OPTIONAL IF YOU WANT TO USE TEXT-TO-SPEECH (https://github.com/AarhusAI/piper-tts)
- name: AUDIO_TTS_ENGINE
  value: "openai"
- name: AUDIO_TTS_OPENAI_API_BASE_URL
  value: "<YOUR_TTS_API_URL>"  # e.g., https://tts.example.com/
- name: AUDIO_TTS_OPENAI_API_KEY
  valueFrom:
    secretKeyRef:
      name: openwebui-secrets
      key: TTS_API_KEY
```

Replace `<YOUR_WHISPER_API_URL>` and `<YOUR_TTS_API_URL>` with your deployed service URLs.

## Deployment

Commit and push your changes. Argo will deploy the new version of Open WebUI.
