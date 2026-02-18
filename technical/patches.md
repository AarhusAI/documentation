---
title: Patches
parent: Technical documentation
---

# Patches

We have created a number of patches for our use of OpenWebUI. The ones relevant for all users of Aarhus AI is already
in the installation.

The ones not applied are Aarhus specific patches like our OIDC patch for signing in using SSO.

The patches can be found here [https://github.com/AarhusAI/open-webui/pulls](patches).

Patches

- name: 'Pinned info banner'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/32.diff]
  - branch: 'feature/pinned-info-banner'
- name: 'Add token to TTS requests'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/38.diff]
  - branch: 'feature/tts-token'
- name: 'Extra permissions'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/40.diff]
  - branch: 'feature/extra-permissions'
- name: 'Fixed null error in auth database session handling'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/43.diff]
  - branch: 'feature/oauth_token_expire'
- name: 'Fix search model drop-down'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/45.diff]
  - branch: 'feature/model-search'
- name: 'Ensured that role names from OIDC is kept'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/34.diff]
  - branch: 'feature/keep-role-names'
- name: 'OIDC aak roles and groups'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/33.diff]
  - branch: 'feature/oidc-group-claims'
- name: 'Group sharing permissions'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/47.diff]
  - branch: 'feature/group-sharing-permissions'
  
Patches AAK:

- name: 'Allow only admin users to generate API-KEY for the backend'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/41.diff]
  - branch: 'feature/admin-api-keys'
- name: 'Frontend changes - login banner and sidebar logo'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/42.diff]
  - branch: 'feature/front-end-ii'
- name: 'Expose file metadata URL in citation modal'
  - url: [https://patch-diff.githubusercontent.com/raw/AarhusAI/open-webui/pull/44.diff]
  - branch: 'feature/citation-modal-url'
