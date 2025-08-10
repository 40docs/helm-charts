# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the **helm-charts** repository within the 40docs platform - a specialized repository for managing Kubernetes Helm charts that support the enterprise Documentation as Code ecosystem. This repository is a Git submodule within the larger 40docs multi-repository architecture.

### Purpose and Architecture

**Primary Function**: Centralized Helm chart repository for Kubernetes applications deployed across the 40docs platform infrastructure.

**Integration Role**:
- Provides packaged Helm charts for applications like MkDocs documentation sites, security tools, and infrastructure components
- Charts are automatically compiled and published to GitHub Pages via CI/CD workflow
- Supports the GitOps deployment model using Flux v2 in the AKS cluster
- Charts consumed by the infrastructure repository for application deployments

## Common Development Commands

### Helm Chart Development
```bash
# Create a new Helm chart
helm create <chart-name>

# Lint and validate charts
helm lint <chart-directory>               # Lint specific chart
find . -maxdepth 1 -type d -name "*" -exec helm lint {} \; 2>/dev/null  # Lint all charts

# Package charts for distribution
helm package <chart-directory>           # Package single chart
helm package <chart-directory> --destination charts/  # Package to specific directory

# Test chart rendering
helm template <release-name> <chart-directory>  # Render templates locally
helm template <release-name> <chart-directory> --debug --dry-run  # Debug mode

# Dependency management
helm dependency update <chart-directory>  # Update chart dependencies
helm dependency build <chart-directory>   # Build dependencies locally
```

### Local Development and Testing
```bash
# Install chart locally for testing (requires kubectl context)
helm install <release-name> <chart-directory> --dry-run  # Test without installing
helm install <release-name> <chart-directory> --namespace <namespace>

# Upgrade existing releases
helm upgrade <release-name> <chart-directory>

# Uninstall releases
helm uninstall <release-name>

# List releases
helm list --all-namespaces
```

### CI/CD Workflow
```bash
# The GitHub workflow automatically:
# 1. Lints all charts with Chart.yaml files
# 2. Packages charts to 'charts/' directory
# 3. Publishes to GitHub Pages (gh-pages branch)

# Manual workflow trigger
gh workflow run "Compile and Publish Helm Charts"

# Check workflow status
gh run list --workflow="Compile and Publish Helm Charts"
```

## Chart Structure and Standards

### Required Chart Structure
```
chart-name/
├── Chart.yaml          # Chart metadata and version info
├── values.yaml         # Default configuration values
├── templates/          # Kubernetes manifest templates
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── configmap.yaml
│   └── _helpers.tpl    # Template helpers
└── README.md          # Chart documentation
```

### Chart.yaml Requirements
```yaml
apiVersion: v2
name: chart-name
description: A Helm chart for [description]
type: application
version: 0.1.0          # Chart version (SemVer)
appVersion: "1.0.0"     # Application version
keywords:
  - 40docs
  - documentation
maintainers:
  - name: [Maintainer Name]
    email: [email]
```

### Values.yaml Best Practices
- Use descriptive comments for all configuration options
- Provide sensible defaults for all values
- Group related configuration under parent keys
- Follow Kubernetes naming conventions
- Include resource limits and requests
- Support both ingress and service mesh configurations

### Template Standards
- Use `_helpers.tpl` for common template functions
- Include proper labels using `helm.sh/chart`, `app.kubernetes.io/name`, etc.
- Support multiple replicas and scaling
- Include liveness and readiness probes
- Use consistent indentation (2 spaces)
- Include resource quotas and security contexts

## Integration with 40docs Platform

### GitOps Deployment Flow
1. Charts are developed and tested in this repository
2. GitHub workflow packages and publishes charts to GitHub Pages
3. Infrastructure repository references published charts via Helm repository
4. Flux v2 in AKS cluster automatically deploys applications using these charts
5. Applications are exposed through FortiWeb NVA with dedicated public IPs

### Common Application Types
- **Documentation Sites**: MkDocs-based documentation with Material theme
- **Security Tools**: DVWA, security scanners, vulnerability assessment tools
- **Infrastructure Services**: Monitoring, logging, artifact management
- **Development Tools**: Build systems, CI/CD components

### Platform-Specific Configuration
- **Ingress**: Configure for FortiWeb NVA load balancing
- **Networking**: Support hub-spoke network topology
- **Security**: Include RBAC, network policies, security contexts
- **Monitoring**: Integrate with Lacework agent and platform monitoring
- **Storage**: Use Azure-specific storage classes and persistent volumes
- **DNS**: Support automatic CNAME record creation for applications

## Chart Dependencies and External Resources

### Common Dependencies
- **cert-manager**: For automatic TLS certificate management
- **ingress-nginx**: For ingress controller functionality (if not using FortiWeb)
- **prometheus**: For monitoring and alerting
- **postgresql/mysql**: For database requirements

### Azure Integration
- Support for Azure AD authentication via Workload Identity
- Azure DNS integration for certificate challenges
- Azure Storage for persistent volumes
- Azure Key Vault for secret management

## Quality and Security Standards

### Security Requirements
- All charts must include security contexts with non-root users
- Network policies should be defined for pod-to-pod communication
- RBAC configurations must follow principle of least privilege
- Secrets should never be stored in plain text in values.yaml

### Testing Requirements
- All charts must pass `helm lint` validation
- Charts should be tested with `helm template` to verify rendering
- Test deployments should be validated in development environment
- Include unit tests for complex template logic using helm-unittest

### Documentation Standards
- Each chart requires comprehensive README.md with usage examples
- Document all values.yaml configuration options
- Include troubleshooting guide for common issues
- Provide upgrade instructions between chart versions

## Multi-Repository Context

### Repository Relationships
- **Parent Repository**: Part of 40docs multi-repository ecosystem
- **Infrastructure Repository**: Consumes charts for application deployment
- **Application Repositories**: May reference these charts for deployment configurations
- **GitOps**: Flux monitors this repository for chart updates

### Communication with Orchestrator
When working within the 40docs Claude orchestrator system:
- Report chart development progress via structured logging
- Coordinate with infrastructure repository for deployment testing
- Communicate chart version updates for dependent repositories
- Provide status updates for chart compilation and publishing workflows

## Troubleshooting

### Common Issues
- **Chart Validation Failures**: Check Chart.yaml syntax and required fields
- **Template Rendering Errors**: Use `helm template --debug` to identify issues
- **Dependency Issues**: Run `helm dependency update` to refresh dependencies
- **Workflow Failures**: Check GitHub Actions logs for packaging or publishing errors

### Debug Commands
```bash
# Debug chart rendering
helm template release-name . --debug

# Validate chart locally
helm install release-name . --dry-run --debug

# Check chart dependencies
helm dependency list

# Verify chart metadata
helm show chart .
helm show values .
```