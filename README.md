# Backstage Plugin for Red Hat Lightwell

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![npm version](https://img.shields.io/npm/v/backstage-plugin-lightwell.svg?color=blue&label=npm%20frontend)](https://www.npmjs.com/package/backstage-plugin-lightwell)
[![npm version](https://img.shields.io/npm/v/backstage-plugin-lightwell-backend.svg?color=blue&label=npm%20backend)](https://www.npmjs.com/package/backstage-plugin-lightwell-backend)
[![Ecosystem](https://img.shields.io/badge/Ecosystem-Red_Hat_Developer_Hub_(RHDH)-red.svg)](https://developers.redhat.com/products/developer-hub/overview)
[![Status](https://img.shields.io/badge/Status-Under_Active_Development-orange.svg)]()

> The official Backstage & Red Hat Developer Hub (RHDH) dynamic plugin for enterprise open-source vulnerability remediation powered by **Red Hat Lightwell**.

Initiated, architected, and maintained by **Maxime Gaillard** (<maxime.gaillard@devoteam.com>) — [STARTX | Part of Devoteam](https://startx.fr).

---

> 🚧 **Notice: Upcoming Code Delivery**  
> This plugin is currently under active engineering by STARTX | Part of Devoteam.  
> The core implementation, UI components, and Lightwell Network API integrations will be delivered and released shortly in the upcoming `v0.1.0` milestone.

---

## 🎯 Overview

Enterprise application security tools (SCA, SAST) detect open-source vulnerabilities at rapid scale, but engineering teams are frequently unable to remediate them. The standard recommendation—bumping to a major new version—introduces breaking API changes, extensive regression testing, and lengthy PSSI recertification delays. This operational bottleneck is defined by Red Hat and IBM as the **"Vulnerability Remediation Gap"**.

**Red Hat Lightwell** provides an authoritative third path: version-specific, cryptographically signed backported patches for enterprise dependencies (Java, Python) with zero breaking changes and full SBOM provenance.

This plugin bridges **Spotify Backstage / Red Hat Developer Hub** and the **Red Hat Lightwell Network**, giving developers in-context security intelligence and one-click backported remediation directly within their internal developer portal.

---

## 🏛️ Architecture

```text
       [ Developer on Backstage / RHDH ]
                       │
       ┌───────────────┴───────────────┐
       ▼                               ▼
[ Frontend Plugin UI ]       [ Software Catalog ]
  • Supply Chain Badge         (Service / Repo Entity)
  • "Lightwell Security" Tab           │
  • CVEs + One-Click Remediation       │
       │                               │
       └───────────────┬───────────────┘
                       │
                       ▼
        [ Backend Plugin (Node.js/TS) ]
                       │
       ┌───────────────┴───────────────┐
       ▼                               ▼
[ Dependency Extraction ]     [ Lightwell API Client ]
 • Git Manifests (pom, req)    • REST Lightwell Network API
 • CI SBOMs (CycloneDX)        • Vulnerability ↔ Fix Matching
                                       │
                                       ▼
                       [ Secured Lightwell Repository ]
                       (Signed Red Hat Java / PyPI Packages)
```

---

## 📦 Packages in this Repository

| Package | npm | Description |
| :--- | :--- | :--- |
| **`backstage-plugin-lightwell`** | [![npm](https://img.shields.io/npm/v/backstage-plugin-lightwell.svg)](https://www.npmjs.com/package/backstage-plugin-lightwell) | Frontend UI plugin (Entity tab, cards, and remediation workflows) |
| **`backstage-plugin-lightwell-backend`** | [![npm](https://img.shields.io/npm/v/backstage-plugin-lightwell-backend.svg)](https://www.npmjs.com/package/backstage-plugin-lightwell-backend) | Backend plugin (Git manifests, SBOM ingestion, and Lightwell API client) |

---

## 🚀 Red Hat Developer Hub (Dynamic Plugin)

This plugin conforms to the **RHDH Dynamic Plugin specification**, enabling zero-compilation runtime installation on Red Hat OpenShift:

```yaml
# dynamic-plugins.yaml
plugins:
  - package: 'backstage-plugin-lightwell'
    disabled: false
  - package: 'backstage-plugin-lightwell-backend'
    disabled: false
    pluginConfig:
      lightwell:
        baseUrl: https://api.lightwell.example.com (or official Red Hat partner endpoint)
        token: ***}
```

---

## 👤 Author & Lead Architect

- **Maxime Gaillard** — Managing Director & Lead Cloud Architect
- **Organization:** STARTX | Part of Devoteam
- **Email:** <maxime.gaillard@devoteam.com>
- **Enterprise Portal:** <https://devoteam.com>
- **Official Gateway:** <https://lightwell.startx.fr>

---

## 📜 License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.  
Copyright © 2026 Maxime Gaillard — STARTX | Part of Devoteam
