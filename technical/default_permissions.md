---
title: Permissions (recommended)
parent: Technical documentation
---

These permissions are extracted using the
tool [https://github.com/AarhusAI/open-webui-permissions-manager](https://github.com/AarhusAI/open-webui-permissions-manager).

## Default permissions (standard tilladelser)

This is the default permission for all new groups, it is under "Groups", below the groups list, in Open WebUI.

```json
{
  "workspace": {
    "models": false,
    "knowledge": false,
    "prompts": false,
    "tools": false,
    "models_import": false,
    "models_export": false,
    "prompts_import": false,
    "prompts_export": false,
    "tools_import": false,
    "tools_export": false
  },
  "sharing": {
    "models": false,
    "public_models": false,
    "knowledge": false,
    "public_knowledge": false,
    "prompts": false,
    "public_prompts": false,
    "tools": false,
    "public_tools": false,
    "notes": false,
    "public_notes": false
  },
  "chat": {
    "controls": false,
    "valves": true,
    "system_prompt": false,
    "image_capture": false,
    "attach_knowledge": false,
    "params": true,
    "file_upload": true,
    "delete": true,
    "delete_message": true,
    "continue_response": true,
    "regenerate_response": true,
    "rate_response": true,
    "edit": true,
    "share": false,
    "export": true,
    "stt": true,
    "tts": true,
    "call": true,
    "multiple_models": false,
    "temporary": true,
    "temporary_enforced": false
  },
  "features": {
    "api_keys": false,
    "notes": false,
    "channels": true,
    "folders": true,
    "direct_tool_servers": false,
    "web_search": true,
    "image_generation": false,
    "code_interpreter": true
  }
}
```

## Build group permissions

AarhusAI recommended permissions for your `Builder` group.

```json
{
  "workspace": {
    "models": true,
    "knowledge": true,
    "prompts": true,
    "tools": false,
    "models_import": true,
    "models_export": true,
    "prompts_import": false,
    "prompts_export": false,
    "tools_import": false,
    "tools_export": false
  },
  "sharing": {
    "models": true,
    "public_models": false,
    "knowledge": true,
    "public_knowledge": false,
    "prompts": false,
    "public_prompts": false,
    "tools": false,
    "public_tools": false,
    "notes": false,
    "public_notes": false
  },
  "chat": {
    "controls": true,
    "valves": true,
    "system_prompt": true,
    "params": true,
    "image_capture": false,
    "attach_knowledge": true,
    "file_upload": true,
    "delete": true,
    "delete_message": true,
    "continue_response": true,
    "regenerate_response": true,
    "rate_response": true,
    "edit": true,
    "share": true,
    "export": true,
    "stt": true,
    "tts": true,
    "call": true,
    "multiple_models": true,
    "temporary": true,
    "temporary_enforced": false,
    "export_import_model": true
  },
  "features": {
    "api_keys": false,
    "notes": false,
    "channels": true,
    "folders": true,
    "direct_tool_servers": false,
    "web_search": true,
    "image_generation": false,
    "code_interpreter": false
  }
}
```
