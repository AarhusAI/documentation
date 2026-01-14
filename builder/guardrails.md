---
title: Guardrails
parent: Builder
---

# Guardrails

Guardrails er software der hjælper med at sikre output fra sprogmodellen. Det kan f.eks. anvendes til at sikre at
sprogmoddelen overholder etiske, sproglige og persondatamæssige begrænsninger.

## Guardrails i AAK og AarhusAI

I AarhusAI findes der pt. kun en guardrail der sikrer et flydende context vindue (så systemet ikke laver en fejl
når contexten bliver fyldt).

Man kan finde dem her:
[https://github.com/AarhusAI/helm-deployments/blob/develop/applications/litellm/templates/message-trimming-config.yaml#L8](https://github.com/AarhusAI/helm-deployments/blob/develop/applications/litellm/templates/message-trimming-config.yaml#L8)
[https://github.com/AarhusAI/helm-deployments/blob/develop/applications/litellm/litellm-values.yaml#L105](https://github.com/AarhusAI/helm-deployments/blob/develop/applications/litellm/litellm-values.yaml#L105)
