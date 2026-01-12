---
title: Upgrading
parent: Installation
has_children: false
nav_order: 2
---

# Upgrading existing installations

The procedure for upgrading existing installations involves following the change log notes for any updates or changes
that may affect your setup. When a new release is created, new packages are published
to [https://github.com/orgs/AarhusAI/packages](https://github.com/orgs/AarhusAI/packages).

The release process:

1. AarhusAI builds new images and updates the Docker Compose setup
   in [https://github.com/AarhusAI/aarhusai-docker](https://github.com/AarhusAI/aarhusai-docker) for development and
   staging environments.
2. We update the Helm charts
   at [https://github.com/AarhusAI/helm-deployments](https://github.com/AarhusAI/helm-deployments) and tag the
   repository, making an official release of AarhusAI.
3. The demo site at [https://demo.os2ai.dk/](https://demo.os2ai.dk/) is updated by manually applying the Helm chart
   changes from the template repository.

Currently, there is a manual step involved in upgrading where you need to update your site's deployment repository based
on the above changes.

## Where to find changes

* AarhusAI change log - [https://github.com/AarhusAI/helm-deployments/blob/main/CHANGELOG.md](https://github.com/AarhusAI/helm-deployments/blob/main/CHANGELOG.md)
* Open WebUI releases - [https://github.com/open-webui/open-webui/releases](https://github.com/open-webui/open-webui/releases)

## Release versions

We use semantic versioning for releases, based on Open WebUI. As we may release patches and changes to config and
application more often than Open WebUI, we suffix the Open WebUI version with a counter.

```text
0.6.43 -> 0.6.43-1 -> 0.6.43-2 -> 0.6.43-3 
0.7.0 -> 0.7.0-1
```
