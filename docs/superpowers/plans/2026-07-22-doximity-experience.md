# Doximity Experience Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add Lucas's Doximity tenure as two resume entries that show his May 2026 promotion.

**Architecture:** Add two `params.experience.list` records at the start of the existing chronological experience data. Keep all rendering logic and existing experience entries unchanged, then verify the generated order and dates through Hugo's rendered HTML.

**Tech Stack:** TOML, Hugo Extended

## Global Constraints

- Insert both Doximity entries before TrustD Solutions.
- Order Senior Software Engineer II before Senior Software Engineer I.
- Use `May 2026 - Present` for Senior II and `May 2025 - May 2026` for Senior I.
- Describe quarter-end AI runtime deliverables as ongoing Senior II work, not completed outcomes.
- Preserve every existing experience entry and all non-experience configuration.

---

### Task 1: Add Doximity Promotion History

**Files:**

- Modify: `config.toml:78`

**Interfaces:**

- Consumes: The existing `params.experience.list` TOML structure rendered by `themes/resume/layouts/partials/experience.html`.
- Produces: Two leading experience records with the approved titles, dates, descriptions, and items.

- [ ] **Step 1: Confirm the entries are absent**

Run:

```bash
if rg -n 'Doximity|Senior Software Engineer II|Senior Software Engineer I' config.toml; then exit 1; fi
```

Expected: exit 0 with no output, proving the new entries are not already present.

- [ ] **Step 2: Add the exact Doximity entries before TrustD Solutions**

Insert this block immediately after `[params.experience]` and `enable = true`, before the current TrustD Solutions record:

```toml
[[params.experience.list]]
title = "Senior Software Engineer II"
dates = "May 2026 - Present"
company = "Doximity"
details = "Doximity is a digital platform for U.S. medical professionals. Promoted to lead cross-team platform initiatives, define durable technical direction, and improve reliability, automation, and developer enablement."
[[params.experience.list.items]]
details = "Leading delivery of a GitOps-managed AI agent runtime on EKS, beginning with the slow-query-fixer workload."
[[params.experience.list.items]]
details = "Building image pipelines with dedicated ECR repositories and workflows for building, scanning, signing, and promoting images."
[[params.experience.list.items]]
details = "Establishing Cilium egress controls, workload SLOs, Grafana dashboards, structured Loki logging, and staged rollout gates."
[[params.experience.list.items]]
details = "Coordinating implementation across Infrastructure Services, Automation, Security, and SRE teams."

[[params.experience.list]]
title = "Senior Software Engineer I"
dates = "May 2025 - May 2026"
company = "Doximity"
details = "Joined Doximity's infrastructure organization to modernize CI systems, strengthen platform reliability, and improve operational self-service."
[[params.experience.list.items]]
details = "Led migration from EC2-based GitHub Actions runners to Kubernetes using GitHub ARC and decommissioned the legacy infrastructure."
[[params.experience.list.items]]
details = "Authored Nexus's first fixture-driven pytest suite, covering cleanup policies, permissions, users, realms, and SAML configuration."
[[params.experience.list.items]]
details = "Added structured logging and Grafana dashboards for runner availability, resource usage, and saturation."
[[params.experience.list.items]]
details = "Automated EKS upgrades, provisioning validation, teardown testing, and nightly scale-down of development clusters."
[[params.experience.list.items]]
details = "Improved contributor onboarding through architecture documentation, operational guides, examples, and reusable tooling."
```

- [ ] **Step 3: Build the resume**

Run:

```bash
mise x hugo-extended@0.162.1 -- hugo --minify --cleanDestinationDir
```

Expected: exit 0 with no warnings.

- [ ] **Step 4: Verify rendered order and dates**

Run:

```bash
rg -o 'Senior Software Engineer II|May 2026 - Present|Senior Software Engineer I|May 2025 - May 2026|TrustD Solutions' public/index.html
```

Expected output in this order:

```text
Senior Software Engineer II
May 2026 - Present
Senior Software Engineer I
May 2025 - May 2026
TrustD Solutions
```

- [ ] **Step 5: Review the focused change**

Run:

```bash
git diff --check
git diff -- config.toml
git status --short
```

Expected: no whitespace errors; `config.toml` contains only the two approved experience records; the approved design and plan documents are the only other changes.
