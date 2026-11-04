# Strategic & Technical Architecture Document (TAD)
## Red Hat Developer Hub (Backstage) Plugin for Red Hat Lightwell
### DevSecOps & Software Supply Chain Offering for STARTX | Part of Devoteam

- **Authors:** Maxime Gaillard (Managing Director & Lead Architect) & Atlas (STARTX Architecture & Systems AI Agent)
- **Recipients:** Christophe Larue & Devoteam Group Leadership
- **Date:** November 4, 2026
- **Classification:** Confidential Internal — Strategic Offering Proposal (Red Hat & IBM Partner Ecosystem)
- **Official Repository:** <https://github.com/maximegaillard-devoteam/backstage-plugin-lightwell>
- **npm Registry:** <https://www.npmjs.com/package/backstage-plugin-lightwell>
- **Enterprise Portal:** <https://lightwell.startx.fr>

---

## Executive Summary

The enterprise software security ecosystem faces a critical bottleneck: **security scanning tools (SCA, SAST) detect vulnerabilities faster than ever, but engineering teams are frequently unable to remediate them without breaking mission-critical applications**. This industry challenge is formally identified by Red Hat and IBM as the **"Vulnerability Remediation Gap"**.

To solve this, Red Hat launched **Lightwell** in 2026: a sovereign offering providing enterprise open-source libraries (Java, Python) with security fixes (CVEs) backported and cryptographically signed on the exact versions in production, completely eliminating breaking changes and costly major-version refactorings.

Concurrently, **Red Hat Developer Hub (RHDH)**, based on the open-source **Spotify Backstage** framework, has established itself as the standard Internal Developer Portal (IDP) across Global 2000 enterprises deploying Red Hat OpenShift.

**The Strategic Opportunity for STARTX & Devoteam:**
Build and publish the world's first Backstage / RHDH dynamic plugin dedicated to Lightwell: **`plugin-lightwell`**. This plugin empowers developers to view vulnerability status directly on their component card within their daily developer portal, and automatically consume Red Hat-remediated and signed packages without disrupting application code.

---

## 1. Market Problem: The Vulnerability Remediation Gap

*(Source: Red Hat Sales Hub — "Lightwell _ Ecosystem Messaging Guide", September 2026, pages 1 & 2)*

### 1.1. The Upgrade Deadlock
In highly regulated sectors (Banking, Financial Services, Telecommunications, Public Sector, Healthcare), core applications rely on thousands of legacy open-source dependencies (e.g., Java Spring, Hibernate, Jackson, Python requests, etc.).
When a critical CVE is disclosed:
1. Conventional scanners (Snyk, SonarQube, GitHub Dependabot, Trivy) flag the vulnerability.
2. Their standard recommendation is invariably: *"Upgrade to the latest major release (e.g., Spring 4.x to 6.x or Python 2 to 3)"*.
3. **The Roadblock:** Major version bumps introduce breaking API changes, extensive regression testing cycles, and lengthy PSSI recertification delays.
4. **The Consequence:** Enterprises carry persistent security exceptions (*risk acceptance*), maintain precarious private forks, or leave critical systems exposed.

---

## 2. What is Red Hat Lightwell?

*(Sources: "Lightwell _ Ecosystem Messaging Guide", pages 2, 4, 6; "Lightwell _ Ecosystem Sales Qualification Guidance", pages 1-3)*

Jointly engineered by **Red Hat and IBM**, Lightwell introduces an authoritative remediation path:

* **Active Remediation, Not Passive Scanning:** Red Hat maintains an enterprise catalog of open-source packages with security fixes backported directly onto specific, older version branches.
* **Red Hat Engineering & Supply Chain Guarantees:**
  * Cryptographically signed and attested binaries (via Red Hat Trusted Artifact Signer / Sigstore).
  * Complete Software Supply Chain provenance: comprehensive **SBOMs (CycloneDX / SPDX)** and SLSA attestations.
  * Zero API regressions for consuming engineering teams.
* **Distribution Model:**
  * **Lightwell Network:** Self-service access to the verified catalog of remediated packages delivered via secure repositories (Maven, PyPI, OCI registries).
  * **Lightwell Clearinghouse:** Premium tier for strategic and regulated accounts, featuring package nomination, dedicated security SLAs, and custom engineering scopes.

---

## 3. Ecosystem Audit & Competitive Analysis: Uncontested First-Mover Advantage

*(Empirical ecosystem verification conducted on November 4, 2026 across npm Registry, GitHub API, Janus-IDP, and Red Hat Sales Hub)*

### 3.1. Current Backstage Security Plugin Landscape
An exhaustive audit of public Backstage and Janus-IDP plugins reveals four main categories:

1. **SCA & Code Scanning Plugins (Snyk, GitHub Security Insights, Dependabot):**
   * *Capabilities:* Detect and display repository CVEs.
   * *Limitation:* They merely enforce standard major version upgrades, creating severe application disruption.
2. **Container & Infrastructure Security Plugins (RHACS / StackRox):**
   * *Capabilities:* `@redhatinsights/backstage-plugin-advanced-cluster-security` monitors runtime container images and host RPMs.
   * *Limitation:* Focused on Kubernetes/OS layer, leaving application source dependencies unmanaged.
3. **Artifact Repository Plugins (JFrog Artifactory, Sonatype Nexus):**
   * *Capabilities:* Display raw binary metadata from corporate registries.
   * *Limitation:* Pure inventory browsers lacking remediation intelligence.
4. **Red Hat Trusted Software Supply Chain (RHTSSC / Trustification / TPA):**
   * *Capabilities:* Aggregate SBOMs and VEX advisories.
   * *Limitation:* Focuses on transparency and inventory, without providing remediated dependency packages.

### 3.2. Verifiable Findings
* **No Backstage or RHDH plugin exists for Lightwell today** (`backstage-plugin-lightwell` was completely unclaimed until STARTX registered it on npm).
* Red Hat explicitly highlights the demand for **"Technology Providers"** in the *Lightwell Ecosystem Messaging Guide* (September 2026, Slides 4 & 6):
  > *"Connect supported security, repository, or development workflows with Lightwell remediation information. Use supported APIs and security metadata to complement existing customer workflows."*
* **Conclusion:** STARTX | Devoteam holds a definitive **first-mover opportunity** to author the canonical Lightwell integration for the Red Hat ecosystem.

---

## 4. Technical Feasibility & Engineering Complexity Assessment

### 4.1. Why Backstage Plugin Development is Highly Accelerated (The "80/20" Rule)
Developing a Backstage plugin does **not** mean writing a full web application from scratch. The Spotify Backstage framework and the Red Hat Developer Hub SDK provide over **80% of the UI and plumbing out of the box**:

* **Pre-Built Design System (`@backstage/core-components`):**
  * Sortable and filterable data grids (`<Table>`).
  * Structured summary containers (`<InfoCard>`).
  * Industry-standard severity indicators (`<StatusError>`, `<StatusWarning>`, `<StatusOK>`).
  * Automatic dark/light theme switching and responsive layout.
* **Declarative Entity Attachment:**
  * Injecting the plugin into any microservice or component requires only a single declarative annotation in the target repository's `catalog-info.yaml` (e.g., `lightwell.redhat.com/enabled: true`).
* **Automated Scaffolding:**
  * Red Hat's official CLI tool (`@janus-idp/cli`) scaffolds the entire project structure (TypeScript configs, Jest test runners, linting, packaging) in a single command.

### 4.2. Core Engineering Effort (Where Value is Actually Built)
The bespoke engineering effort is strictly focused on three specific, well-defined technical modules:
1. **Dependency Extraction & SBOM Ingestion (Backend):**
   * Parsing project dependency manifests (`pom.xml` for Maven, `requirements.txt` / `Pipfile` for Python) or reading standard **CycloneDX / SPDX SBOMs** generated during CI.
   * *Accelerator:* Leveraging mature open-source parsers (e.g., `@cyclonedx/cyclonedx-library`, `fast-xml-parser`).
2. **Lightwell API Integration & Caching Layer (Backend):**
   * A resilient Node.js / Express service that sends `(package, version)` tuples to the Lightwell Network REST API.
   * In-memory or Redis caching to ensure sub-100ms response times and prevent rate-limiting.
3. **One-Click Remediation Component (Frontend):**
   * UI modal generating copyable dependency replacement snippets (Maven XML, pip commands) pointing to the enterprise's private Lightwell repository.

---

## 5. Open-Source Architectural Blueprints & References

We do not need to invent new architectural patterns. The project directly builds upon proven, production-grade open-source blueprints:

1. **UI & Data Presentation Blueprint: `@roadiehq/backstage-plugin-security-insights`**
   * The benchmark open-source plugin for vulnerability visualization in Backstage.
   * *What we borrow:* Vulnerability table design, severity filtering tabs, and entity overview summary cards.
2. **Red Hat Enterprise Architecture Blueprint: `@redhatinsights/backstage-plugin-advanced-cluster-security` (RHACS)**
   * Red Hat's official plugin for OpenShift container security.
   * *What we borrow:* Red Hat's canonical backend-to-frontend communication pattern, secure API token handling, and RHDH configuration schema.
3. **Packaging & CI/CD Blueprint: `github.com/janus-idp/backstage-plugins`**
   * The upstream monorepo maintained by Red Hat engineering for all official RHDH dynamic plugins (Tekton, Quay, Keycloak, Topology).
   * *What we borrow:* GitHub Actions build pipelines, automated linting, and `@janus-idp/cli package export-dynamic-plugin` bundling scripts.

---

## 6. Functional Architecture & Developer Experience

### 6.1. System Architecture Diagram

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

### 6.2. Developer Experience (DX)
1. The developer inspects their microservice in Red Hat Developer Hub.
2. The backend plugin analyzes dependencies from the Git repository or CI-generated SBOM.
3. If an eligible vulnerability is detected (e.g., `org.apache.logging.log4j:log4j-core:2.14.0`):
   * The plugin queries the Lightwell API.
   * It alerts: **"Critical CVE detected. Backported Red Hat remediation available without major upgrade."**
   * It provides the exact configuration snippet ready to copy-paste:
     ```xml
     <!-- Consume official backported and signed Lightwell artifact -->
     <dependency>
         <groupId>com.redhat.lightwell.remediated</groupId>
         <artifactId>log4j-core</artifactId>
         <version>2.14.0-redhat-00001</version>
     </dependency>
     ```
4. The developer merges the fix with zero API breaking changes and full compliance.

---

## 7. Strategic Alignment with Devoteam & Christophe Larue

### 7.1. Devoteam Institute & Open Source Center of Excellence
* **Christophe Larue** oversees official technical education (Red Hat Global Learning Services) and high-level certifications within Devoteam Institute.
* This initiative elevates Devoteam from a conventional reseller or staffing firm to a **first-tier software solution partner and innovator in the Red Hat ecosystem**.

### 7.2. Commercial Monetization & Enterprise Revenue
1. **Target Account Penetration:** The Ideal Customer Profile (ICP) for Lightwell matches Devoteam's enterprise accounts (Banking, Telecoms, Public Sector).
2. **High-Margin Consulting Services:**
   * *Lightwell Readiness Assessment* (dependency debt & audit).
   * Red Hat Developer Hub deployment and OpenShift platform engineering.
   * End-to-end Secure Software Supply Chain integration (Tekton, Quay, Sigstore, Backstage).
3. **Alliance Visibility:**
   * Global showcase at **Red Hat Summit 2027**.
   * Nomination for **Red Hat Partner Innovation Awards**.

---

## 8. Sizing & Implementation Roadmap (15 to 20 Man-Days)

For a senior full-stack TypeScript / React engineer familiar with OpenShift and Backstage:

* **Phase 1 — PoC & UI Scaffolding (3–4 days):**
  * Scaffold dynamic plugin structure using `@janus-idp/cli`.
  * Emulate Lightwell API data with realistic mock fixtures (real-world Log4j / Spring Boot CVEs).
  * Validate UI components inside a local Backstage / RHDH instance.
* **Phase 2 — Connected MVP & SBOM Parser (8–10 days):**
  * Implement backend client connecting to live Lightwell Network API endpoints.
  * Ingest Maven (`pom.xml`), Python (`requirements.txt`), and CycloneDX SBOMs.
  * Implement caching and error resilience.
  * Validate dynamic plugin injection on a live Red Hat OpenShift cluster.
* **Phase 3 — Enterprise Hardening & Ecosystem Launch (3–4 days):**
  * Automated unit & integration testing suite (Jest).
  * Technical Architecture Documentation (TAD).
  * Joint Devoteam / STARTX commercial pitch deck.
  * Formal presentation to Red Hat Developer Hub & Lightwell product leadership.

**Total Project Effort: 15 to 20 Man-Days** (< 1 month of senior engineering).

---

## 9. Official Verifiable References

All technical and commercial claims in this document are grounded in the following verified source materials:

1. **Red Hat Inc. & IBM Corporation:**
   * *"Lightwell _ Ecosystem Messaging Guide"* (Internal Red Hat & NDA Partner Material, September 2026, 7 pages):
     * Remediation Gap Definition (p. 1)
     * Lightwell Network & Clearinghouse Tiers (p. 2)
     * Technology Provider & Services Partner Opportunities (p. 4–6).
   * *"Lightwell _ Ecosystem Sales Qualification Guidance"* (Partner Qualification Guide, September 2026, 3 pages).
   * *"Lightwell - Ecosystem Reseller Call Script"* (Technical Discovery Script, 2026, 4 pages).
2. **Janus-IDP & Red Hat Developer Hub Ecosystem:**
   * Janus IDP Open Source Project: <https://github.com/janus-idp>
   * Red Hat Developer Hub Dynamic Plugins Architecture: <https://developers.redhat.com/products/developer-hub/overview>
   * Spotify Backstage Plugin Architecture: <https://backstage.io/docs/plugins/>
3. **Namespace Staking & Registries:**
   * Official npm packages registered by STARTX on November 4, 2026:
     * `backstage-plugin-lightwell`: <https://www.npmjs.com/package/backstage-plugin-lightwell>
     * `backstage-plugin-lightwell-backend`: <https://www.npmjs.com/package/backstage-plugin-lightwell-backend>
   * Verified domain reservation: <https://lightwell.startx.fr>
