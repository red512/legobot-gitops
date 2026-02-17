# legobot-gitops

GitOps repository for legobot platform deployments using Helm charts and ArgoCD.

## Structure

```
legobot-gitops/
├── argocd/           # ArgoCD application definitions
│   └── apps/         # Individual app manifests
├── helm/             # Helm charts for all services
│   ├── bnfrbot-helm-chart/       # Slack bot application
│   ├── bnfrmcp-helm-chart/       # MCP server
│   ├── kagent-helm-chart/        # K8s agent
│   ├── backend-helm-chart/       # Backend service
│   ├── ollama-helm-chart/        # Ollama LLM service
│   └── prometheus-helm-chart/    # Monitoring stack
└── README.md
```

## Quick Start

### Prerequisites

- Kubernetes cluster
- kubectl configured
- Helm 3.x installed
- kubeseal CLI (for sealed secrets)

### Working with Sealed Secrets

All sensitive configuration is managed through Bitnami Sealed Secrets for secure GitOps workflows.

**Important:** Never commit plain secret files. They are in `.gitignore`:
- `*-secrets.yaml` (plain secrets)
- `sealed-*-secrets.yaml` (sealed secrets with encrypted data)

For detailed instructions on creating and sealing secrets, see the README in each Helm chart directory.

### Quick Command Reference

```bash
# Create secret (don't apply)
kubectl create secret generic <name> -n <namespace> \
  --from-literal=KEY="value" \
  --dry-run=client -o yaml > secret.yaml

# Seal the secret
kubeseal --controller-name sealed-secrets \
  --controller-namespace sealed-secrets \
  --format yaml < secret.yaml > sealed-secret.yaml

# Extract and add encrypted values to Helm values.yaml
```

## Helm Charts

Each component has its own Helm chart with detailed configuration options:

- **[bnfrbot-helm-chart](helm/bnfrbot-helm-chart/README.md)** - Slack bot with LLM integration
- **bnfrmcp-helm-chart** - MCP server for vector store and tool orchestration
- **backend-helm-chart** - Backend API service
- **kagent-helm-chart** - Kubernetes management agent
- **ollama-helm-chart** - Self-hosted LLM inference

## ArgoCD

ArgoCD applications are defined in `argocd/apps/` and manage automated deployment from this repository.

## Contributing

When adding new secrets:
1. Generate sealed secrets locally
2. Update only the `values.yaml` with encrypted strings
3. Never commit plain text secrets
4. Ensure secret files are in `.gitignore`
