---
title: Text-to-speech and speech-to-text
parent: Installation
has_children: false
nav_order: 3
---

The text-to-speech and speech-to-text engines are optional components for AARhus-AI and fully supported by the Open
WebUI. It is optional as it requires an extra GPU to run the two systems.

The two required setups are located in the following repos, so the first step is to set up the two projects or use
solutions that support the OpenAI Audio API. See the links below for more information about the installation process
and options.

* [https://github.com/AarhusAI/whisper-api](https://github.com/AarhusAI/whisper-api)
* [https://github.com/AarhusAI/piper-tts]([https://github.com/AarhusAI/piper-tts)

Edit the `applications/openwebui/values.yaml` in the helm deployment and adding the two secrets (
`AUDIO_STT_OPENAI_API_KEY`, `AUDIO_TTS_OPENAI_API_KEY`) to your sealed secrets:

```yaml
# STT (whisper) - OPTIONAL IF YOU WANT TO USE SPEECH-TO-TEXT (https://github.com/AarhusAI/whisper-api)
- name: AUDIO_STT_ENGINE
  value: "openai"
- name: AUDIO_STT_OPENAI_API_BASE_URL
  value: "https://whisper.itkdev.dk/"
- name: AUDIO_STT_OPENAI_API_KEY
  valueFrom:
    secretKeyRef:
      name: openwebui-secrets
      key: STT_API_KEY

# TTS (piper) - OPTIONAL IF YOU WANT TO USE TEXT-TO-SPEECH (https://github.com/AarhusAI/piper-tts)
- name: AUDIO_TTS_ENGINE
  value: "openai"
- name: AUDIO_TTS_OPENAI_API_BASE_URL
  value: "https://tts.itkdev.dk/"
- name: AUDIO_TTS_OPENAI_API_KEY
  valueFrom:
    secretKeyRef:
      name: openwebui-secrets
      key: TTS_API_KEY
```
