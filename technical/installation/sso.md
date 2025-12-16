---
title: SSO - OpenWebui
parent: Installation
has_children: false
nav_order: 3
---


https://github.com/AarhusAI/open-webui/pull/33


`applications/openwebui/values.yaml`

```yaml
- name: ENABLE_LOGIN_FORM
  value: "False"
- name: ENABLE_OAUTH_GROUP_MANAGEMENT
  value: "True"
- name: ENABLE_OAUTH_GROUP_CREATION
  value: "True"

 sso:
    enabled: true
    
    oidc:
      enabled: true
      clientId: "<ID>"
```

Sealed:

```yaml
OIDC_CLIENT_SECRET
```
