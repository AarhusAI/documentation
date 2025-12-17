---
title: Open Id Connect (OIDC)
parent: Installation
has_children: false
nav_order: 3
---

Open WebUI supports SSO with different providers. AarhusAI has been extended for better support for OIDC groups and role
claims from Microsoft OIDC. The default AarhusAI docker
image [ghcr.io/aarhusai/open-webui](https://ghcr.io/aarhusai/open-webui)
has been patched with [https://github.com/AarhusAI/open-webui/pull/33](https://github.com/AarhusAI/open-webui/pull/33).

This patch allows configurable mapping of groups and roles from OIDC claims to groups in Open WebUI. It also ensures that the `builder` role from claims is mapped
to the `Builder` group, as this group is responsible for giving permissions for assistant building and more.

## Configuration Steps

First step is to disable login form and enable group management, so groups are automatically created and users are
assigned to group on every login. Edit `applications/openwebui/values.yaml` and configure the following values:

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

Next update `applications/openwebui/local-secrets/openwebui-secrets.yaml` with the `OIDC_CLIENT_SECRET` value from Azure
AD. See the Deployment.md for more information.

Seal the secret using kubeseal and redeploy the application by committing the changes to the main branch:

```bash
kubectl create -f local-secrets/openwebui-secrets.yaml --dry-run=client -o yaml | kubeseal --format yaml > templates/sealed-openwebui-secrets.yaml
```

## Configuration Options

| Variable                               | Description                                                     | Default                                                                       |
|----------------------------------------|-----------------------------------------------------------------|-------------------------------------------------------------------------------|
| `AAK_OAUTH_ENABLE_ROLE_GROUPS_MAPPING` | Enable or disable role and group mappings from OAuth claims     | `False`                                                                       |
| `AAK_OAUTH_DEBUG_FORCE_ROLE`           | Override role from OAuth for local debugging purposes           | -                                                                             |
| `AAK_OAUTH_GROUP_CLAIMS`               | Array of claim names to use for group hierarchy (order matters) | `["companyname", "division", "department", "extensionAttribute12", "Office"]` |
| `AAK_OAUTH_GROUP_ID_CLAIM`             | Claim containing the group ID value                             | `extensionAttribute7`                                                         |
| `AAK_OAUTH_GROUP_ID_SEPARATOR`         | Separator character for parsing group IDs from claims           | `;`                                                                           |

The order of claims in `AAK_OAUTH_GROUP_CLAIMS` defines the group hierarchy depth and must match your organizational
structure.

### Example Configuration

Configure custom claims for group mapping (this is the default configuration):

```yaml
- name: AAK_OAUTH_ENABLE_ROLE_GROUPS_MAPPING
  value: "False" # Disabled as default
- name: AAK_OAUTH_GROUP_CLAIMS
  value: '["companyname", "division", "department", "extensionAttribute12", "Office"]'
- name: AAK_OAUTH_GROUP_ID_CLAIM
  value: "extensionAttribute7"
- name: AAK_OAUTH_GROUP_ID_SEPARATOR
  value: ";"
```

### Debugging

The `AAK_OAUTH_DEBUG_FORCE_ROLE` environment variable can be used to force a specific role for local debugging purposes.
So if you do not have the option to change your role in the AD as needed you can force a specific role.

**Disclaimer/Danger:** This is **ONLY** for local usage as it will make all logins have this role.

```yaml
- name: AAK_OAUTH_DEBUG_FORCE_ROLE
  value: "builder"
```
