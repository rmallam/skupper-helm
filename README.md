# Skupper Helm Charts - Enterprise Multi-Cluster Connectivity

[![Kubernetes](https://img.shields.io/badge/Kubernetes-1.21+-blue?logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![OpenShift](https://img.shields.io/badge/OpenShift-4.x-red?logo=redhat&logoColor=white)](https://www.redhat.com/en/technologies/cloud-computing/openshift)
[![Helm](https://img.shields.io/badge/Helm-3.x-0F1689?logo=helm&logoColor=white)](https://helm.sh/)
[![Vault](https://img.shields.io/badge/HashiCorp_Vault-Integrated-000?logo=vault&logoColor=white)](https://www.vaultproject.io/)

> **Production-ready Helm charts for deploying [Skupper](https://skupper.io/) with HashiCorp Vault integration for enterprise Kubernetes/OpenShift environments.**

Skupper enables secure, layer-7 service connectivity across Kubernetes clusters without VPNs, special firewall rules, or cluster admin privileges.

---

## 🏗️ Architecture

\`\`\`
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SKUPPER MULTI-CLUSTER MESH                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────┐           ┌─────────────────────────┐        │
│   │   CLUSTER A (Site 1)    │           │   CLUSTER B (Site 2)    │        │
│   │   ┌─────────────────┐   │           │   ┌─────────────────┐   │        │
│   │   │ skupper-router  │◄──┼───────────┼──►│ skupper-router  │   │        │
│   │   └────────┬────────┘   │  AMQP 1.0 │   └────────┬────────┘   │        │
│   │            │            │  over TLS │            │            │        │
│   │   ┌────────▼────────┐   │           │   ┌────────▼────────┐   │        │
│   │   │service-controller│  │           │   │service-controller│  │        │
│   │   └─────────────────┘   │           │   └─────────────────┘   │        │
│   │   ┌─────────────────┐   │           │   ┌─────────────────┐   │        │
│   │   │ site-controller │   │           │   │ site-controller │   │        │
│   │   └─────────────────┘   │           │   └─────────────────┘   │        │
│   │                         │           │                         │        │
│   │   ┌─────────────────┐   │           │   ┌─────────────────┐   │        │
│   │   │   Your Service  │   │           │   │   Your Service  │   │        │
│   │   │   (Backend)     │   │           │   │   (Frontend)    │   │        │
│   │   └─────────────────┘   │           │   └─────────────────┘   │        │
│   └─────────────────────────┘           └─────────────────────────┘        │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐  │
│   │                     HASHICORP VAULT                                  │  │
│   │   • mTLS Certificates (auto-rotated)                                │  │
│   │   • Inter-router authentication                                     │  │
│   │   • Secrets management                                              │  │
│   └─────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
\`\`\`

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🔐 **Vault Integration** | Certificates pulled from HashiCorp Vault at deploy time |
| 🌐 **Multi-Cluster** | Connect services across any Kubernetes clusters |
| 📦 **Modular Charts** | Three separate Helm charts for flexible deployment |
| 🔄 **GitOps Ready** | Fully declarative, works with ArgoCD/Flux |
| ⚡ **Layer 7 Routing** | Application-level traffic management |
| 🛡️ **Zero Trust** | mTLS encryption for all inter-cluster traffic |
| 📊 **Resource Controls** | CPU/memory limits and autoscaling support |

---

## 📁 Chart Structure

\`\`\`
skupper-helm/
├── skupper-site/        # Site configuration (deploy first)
├── skupper/             # Router + core components  
├── skupper-controllers/ # Service & Site controllers
├── values.yaml          # Global configuration
└── install.sh           # Quick install script
\`\`\`

---

## 🚀 Quick Start

### Prerequisites

- Kubernetes 1.21+ or OpenShift 4.x
- Helm 3.x
- HashiCorp Vault with certificates configured

### Installation

\`\`\`bash
# 1. Deploy site configuration
helm upgrade --install skupper-site ./skupper-site --namespace my-namespace

# 2. Deploy router and core components
helm upgrade --install skupper ./skupper --namespace my-namespace -f values.yaml

# 3. Deploy controllers
helm upgrade --install skupper-controllers ./skupper-controllers --namespace my-namespace
\`\`\`

---

## ⚙️ Configuration

### Key Values

| Parameter | Description | Default |
|-----------|-------------|---------|
| \`skupper.router.replicaCount\` | Number of router replicas | \`1\` |
| \`skupper.router.resources.limits.memory\` | Router memory limit | \`256Mi\` |
| \`ingress.enabled\` | Enable inter-router ingress | \`true\` |
| \`remoteSites\` | List of remote Skupper sites | \`[]\` |

### Connecting to Remote Sites

\`\`\`yaml
remoteSites:
  - name: production-east
    host: skupper-inter-router.prod-east.example.com
\`\`\`

---

## 📖 Use Cases

- **Hybrid Cloud Connectivity** - Connect on-prem to cloud without VPNs
- **Multi-Cluster Service Mesh** - Lightweight Istio alternative
- **Database Access** - Expose Redis/PostgreSQL across clusters
- **Gradual Migration** - Run services in both environments seamlessly

---

## 🔧 Components

| Component | Purpose |
|-----------|---------|
| **skupper-router** | AMQP router for inter-cluster traffic |
| **service-controller** | Service exposure and sync |
| **site-controller** | Annotation-based automation |
| **config-sync** | Configuration synchronization |

---

## 📚 Resources

- [Skupper Docs](https://skupper.io/docs/) • [Skupper GitHub](https://github.com/skupperproject/skupper) • [Vault Docs](https://www.vaultproject.io/docs)

---

## 👤 Author

**Rakesh Kumar Mallam** - Senior Architect @ Red Hat  
[LinkedIn](https://linkedin.com/in/rakeshkumarmallam) • [GitHub](https://github.com/rmallam) • [Portfolio](https://rmallam.github.io)
