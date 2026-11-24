# Pratik Karandikar — DevSecOps & Cloud Infrastructure Portfolio

[![Live Portfolio](https://img.shields.io/badge/Live_Portfolio-pratikkarandikar.github.io-10b981?style=for-the-badge&logo=githubpages&logoColor=white)](https://pratikkarandikar.github.io)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Pratik_Karandikar-0ea5e9?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/pratikkarandikar)
[![Certifications](https://img.shields.io/badge/Credentials-CCNA_%7C_AWS_SAA_(In_Progress)_%7C_CKA_(Lab_Track)-f59e0b?style=for-the-badge)](#certifications--lab-roadmap)

> **NDA & Security Notice:** All architecture blueprints, CI/CD templates, and SRE postmortems in this repository are strictly sanitized. Employer infrastructure identifiers, IP addresses, credentials, and proprietary business logic have been stripped and replaced with generic, reusable patterns.

---

## Executive Impact Summary

Sole DevOps/DevSecOps owner transitioning multi-tenant SaaS infrastructure across **AWS** and **on-premises Kubernetes** from manual operations to policy-gated automation aligned with **NIST SSDF (SP 800-218)** and **SLSA v1.2**.

| Domain | Engineering Intervention | Quantifiable Outcome |
| :--- | :--- | :--- |
| **CI/CD Velocity** | BuildKit registry layer caching, lockfile-pinned multi-stage Dockerfiles & weekly auto-refreshing base-image mirror across 16 pipelines | **95% faster backend builds** (`18m → 49s`); **65% faster frontend builds** (`17.5m → 6.2m`) |
| **Hybrid Cloud FinOps** | Dual AWS Site-to-Site IPsec VPNs (4 tunnels, `/32` static routes) + private SCRAM-only Kafka 4.x listener replacing NAT Gateway egress | **~90% lower path cost** vs. Transit Gateway across **~5 TB/month** telemetry stream |
| **Cloud Rationalization** | 46-resource AWS ownership audit, 7-day zero-traffic verification & snapshot guardrails | **25–40% monthly spend identified as reducible**; **480 action-less alarms** & **81% registry bloat** purged |
| **DevSecOps Gates** | Blocking SAST, secret, container, Dockerfile & manifest gates + ephemeral PostgreSQL schema-drift checks | **0 standing exceptions** across ~20 DEV/UAT/PROD pipelines; automated CycloneDX SBOM generation |
| **Kubernetes & Mesh** | Shared Istio Gateway consolidation (15 hosts) & no-surge rollouts (`maxSurge: 0`) on CPU-constrained nodes (`94–98%` requests) | **Zero HTTP/2 coalescing 404s**; unblocked deployments with zero emergency hardware spend |

---

## Hybrid AWS-to-On-Premises Telemetry Architecture

```mermaid
flowchart LR
    subgraph AWS["AWS Cloud (EKS Telemetry VPC)"]
        EKS["EKS Producers / Consumers"]
        VGW["Virtual Private Gateway\n(Static /32 Routes)"]
        EKS --> VGW
    end

    subgraph VPN["Redundant IPsec Connectivity (4 Tunnels)"]
        T1["Tunnel 1 (Equal Metric)"]
        T2["Tunnel 2 (Equal Metric)"]
    end

    subgraph ONPREM["Colocation Private Cloud"]
        FW["Next-Gen Firewall\n(Unified Zone • Symmetric Return)"]
        KAFKA["Apache Kafka 4.x (KRaft)\nPrivate Listener • SASL/SCRAM-SHA-512"]
        KUI["Kafka UI\n(Microsoft Entra ID OIDC SSO)"]
        FW --> KAFKA
        KUI --> KAFKA
    end

    VGW --- T1 --- FW
    VGW --- T2 --- FW
