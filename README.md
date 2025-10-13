# ArgoCD Getting Started Guide

This repository demonstrates how to get started with ArgoCD for GitOps-based application deployment on Kubernetes.

## Prerequisites

- Kubernetes cluster (minikube, kind, or cloud provider)
- kubectl configured to access your cluster
- Git repository access

## What's Included

This repository contains:

- **ArgoCD Application Configuration** (`apps/argocd/application.yaml`)
- **Sample Application** (`apps/sample-app/`) - A simple nginx-based web application
  - Kubernetes Deployment
  - Service
  - Kustomization configuration

## Quick Start

### 1. Install ArgoCD

First, install ArgoCD on your Kubernetes cluster:

```bash
# Create argocd namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Access ArgoCD UI

```bash
# Get the ArgoCD server pod
kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server

# Forward port to access ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Access the UI at: `https://localhost:8080`

### 3. Get Initial Admin Password

```bash
# Get the initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Login with:

- Username: `admin`
- Password: (from the command above)

### 4. Deploy the Sample Application

Apply the ArgoCD application configuration:

```bash
# Create the sample-app namespace
kubectl create namespace sample-app

# Deploy the ArgoCD application
kubectl apply -f apps/argocd/application.yaml
```

### 5. Access the Sample Application

Once deployed, you can access the sample application:

```bash
# Port forward to the sample app service
kubectl port-forward svc/sample-app -n sample-app 8081:80
```

Visit: `http://localhost:8081`

## Application Structure

### Sample App Configuration

The sample application includes:

- **Deployment**: Nginx 1.27-alpine with 2 replicas
- **Service**: ClusterIP service exposing port 80
- **Resources**: CPU and memory limits configured for production use
- **Environment**: Set to production mode

### ArgoCD Application

The ArgoCD application is configured with:

- **Repository**: This GitHub repository
- **Path**: `apps/sample-app`
- **Sync Policy**: Automated with pruning and self-healing enabled
- **Destination**: `sample-app` namespace

## GitOps Workflow

1. **Make Changes**: Modify the Kubernetes manifests in `apps/sample-app/`
2. **Commit & Push**: Push changes to the Git repository
3. **Auto-Sync**: ArgoCD automatically detects changes and applies them
4. **Monitor**: Check the ArgoCD UI for deployment status

## Useful Commands

```bash
# Check ArgoCD applications
kubectl get applications -n argocd

# View application details
kubectl describe application sample-app -n argocd

# Check sample app pods
kubectl get pods -n sample-app

# View sample app logs
kubectl logs -l app=sample-app -n sample-app

# Force sync an application (if auto-sync is disabled)
argocd app sync sample-app
```

## ArgoCD CLI (Optional)

Install the ArgoCD CLI for advanced operations:

```bash
# Download and install ArgoCD CLI
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

# Login to ArgoCD
argocd login localhost:8080
```

## Troubleshooting

### Common Issues

1. **Application not syncing**: Check the repository URL and ensure ArgoCD has access
2. **Pods not starting**: Verify resource requests and limits
3. **Service not accessible**: Check service and ingress configurations

### Logs and Debugging

```bash
# Check ArgoCD application controller logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-application-controller

# Check ArgoCD server logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-server

# Describe the application for events
kubectl describe application sample-app -n argocd
```

## Next Steps

- Explore ArgoCD's [official documentation](https://argo-cd.readthedocs.io/)
- Learn about [App of Apps pattern](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)
- Configure [RBAC and SSO](https://argo-cd.readthedocs.io/en/stable/operator-manual/rbac/)
- Set up [notifications and webhooks](https://argocd-notifications.readthedocs.io/)

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the deployment
5. Submit a pull request

## License

This project is open source and available under the MIT License.