# Doximity Experience Design

## Goal

Add Lucas's Doximity tenure to the resume as two chronological entries that make the May 2026 promotion explicit.

## Placement

Insert both entries at the start of `params.experience.list`, before TrustD Solutions. Order the promoted role first:

1. Senior Software Engineer II, May 2026 - Present.
2. Senior Software Engineer I, May 2025 - May 2026.

## Senior Software Engineer II

**Company:** Doximity

**Details:** Doximity is a digital platform for U.S. medical professionals. Promoted to lead cross-team platform initiatives, define durable technical direction, and improve reliability, automation, and developer enablement.

**Items:**

- Leading delivery of a GitOps-managed AI agent runtime on EKS, beginning with the slow-query-fixer workload.
- Building image pipelines with dedicated ECR repositories and workflows for building, scanning, signing, and promoting images.
- Establishing Cilium egress controls, workload SLOs, Grafana dashboards, structured Loki logging, and staged rollout gates.
- Coordinating implementation across Infrastructure Services, Automation, Security, and SRE teams.

## Senior Software Engineer I

**Company:** Doximity

**Details:** Joined Doximity's infrastructure organization to modernize CI systems, strengthen platform reliability, and improve operational self-service.

**Items:**

- Led migration from EC2-based GitHub Actions runners to Kubernetes using GitHub ARC and decommissioned the legacy infrastructure.
- Authored Nexus's first fixture-driven pytest suite, covering cleanup policies, permissions, users, realms, and SAML configuration.
- Added structured logging and Grafana dashboards for runner availability, resource usage, and saturation.
- Automated EKS upgrades, provisioning validation, teardown testing, and nightly scale-down of development clusters.
- Improved contributor onboarding through architecture documentation, operational guides, examples, and reusable tooling.

## Verification

- Build with `mise x hugo-extended@0.162.1 -- hugo --minify --cleanDestinationDir` without warnings.
- Confirm both Doximity entries render before TrustD Solutions in `public/index.html`.
- Confirm the Senior II entry shows `May 2026 - Present` and the Senior I entry shows `May 2025 - May 2026`.
