# legobot-terraform# legobot-gitops


## Structure

```
legobot-gitops/
├── argocd
│   └── apps
└── helm
    ├── backend-helm-chart
    └── k2sobot-helm-chart

legobot-terrafrom/
.
├── argocd.tf
├── aws-provider.tf
├── data.tf
├── eks.tf
├── helm-provider.tf
├── helm-values
│   |── argocd-values.yaml
├── iam.tf
├── load-balancer-controller.tf
├── locals.tf
├── metrics-server.tf
├── terraform.tfstate
├── terraform.tfstate.backup
├── variables.tf
└── vpc.tf

```

## Quick Start

### Prerequisites

- Kubernetes cluster
- kubectl configured
- Helm 3.x installed
- kubeseal CLI (for sealed secrets)

### Working with Sealed Secrets

All sensitive configuration is managed through Bitnami Sealed Secrets for secure GitOps workflows.

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


  example:
    kubectl create secret generic k2sobot-secrets -n k2so \
  --from-literal=SLACK_BOT_TOKEN="xoxb-example_password" \
  --from-literal=SLACK_SIGNING_SECRET="example_password" \
  --from-literal=VERIFICATION_TOKEN="example_password" \
  --from-literal=GEMINI_API_KEY="example_password" \
  --from-literal=ARGOCD_PASSWORD="example_password" \
  --dry-run=client -o yaml > k2sobot-secrets.yaml

  kubeseal --controller-name sealed-secrets \            
  --controller-namespace sealed-secrets \
  --format yaml < k2sobot-secrets.yaml > sealed-k2sobot-secrets.yaml

# Extract and add encrypted values to Helm values.yaml
```

