# Helm Charts Repository

Centralized repository for Kubernetes Helm charts supporting the 40docs Documentation as Code platform.

## Overview

This repository serves as the helm chart registry for the 40docs multi-repository ecosystem. Charts are automatically compiled, packaged, and published to GitHub Pages, enabling GitOps deployment via Flux v2 in the platform's AKS cluster.

### Purpose

- **Chart Distribution**: Centralized location for all platform Helm charts
- **GitOps Integration**: Charts consumed by infrastructure repository for automated deployments  
- **Application Support**: Provides charts for documentation sites, security tools, and platform services
- **Version Management**: Semantic versioning and automated chart publishing

## Repository Structure

```
helm-charts/
├── .github/
│   └── workflows/
│       └── helm-charts.yml    # Automated chart packaging and publishing
├── chart-name/                # Individual helm charts
│   ├── Chart.yaml            # Chart metadata
│   ├── values.yaml           # Default configuration
│   ├── templates/            # Kubernetes manifest templates
│   └── README.md             # Chart-specific documentation
└── charts/                    # Published charts (auto-generated)
```

## Quick Start

### Creating a New Chart

```bash
# Generate new chart structure
helm create my-app

# Lint the chart
helm lint my-app

# Test template rendering
helm template my-app-release my-app --debug
```

### Chart Development Workflow

1. **Create**: Use `helm create` or copy existing chart template
2. **Develop**: Edit Chart.yaml, values.yaml, and templates/
3. **Lint**: Validate with `helm lint chart-name`
4. **Test**: Render templates with `helm template`
5. **Commit**: Push changes to trigger automated packaging
6. **Publish**: GitHub workflow automatically publishes to GitHub Pages

## Chart Standards

### Required Files
- `Chart.yaml` - Chart metadata with proper versioning
- `values.yaml` - Comprehensive default configuration
- `templates/` - Kubernetes manifest templates
- `README.md` - Usage documentation and examples

### Configuration Standards
- **Semantic Versioning**: Follow SemVer for chart versions
- **Resource Management**: Include resource requests/limits
- **Security Context**: Non-root users and appropriate security contexts
- **Platform Integration**: Support FortiWeb ingress and Azure services

## Automated Publishing

### GitHub Workflow
The `.github/workflows/helm-charts.yml` workflow automatically:
- Validates all charts using `helm lint`
- Packages charts to the `charts/` directory
- Publishes packaged charts to GitHub Pages (gh-pages branch)
- Creates a Helm repository index accessible via HTTPS

### Chart Repository Access
Published charts are available at: `https://[organization].github.io/helm-charts/`

## Platform Integration

### GitOps Deployment
1. Charts published to GitHub Pages helm repository
2. Infrastructure repository references charts in HelmRelease resources
3. Flux v2 automatically deploys applications using published charts
4. Applications exposed through FortiWeb NVA with dedicated IPs

### Common Chart Types
- **MkDocs Sites**: Documentation hosting with Material theme
- **Security Tools**: DVWA, vulnerability scanners, penetration testing tools
- **Platform Services**: Monitoring, logging, artifact management, build systems
- **Development Tools**: CI/CD components, development environments

## Contributing

### Chart Requirements
- All charts must pass `helm lint` validation
- Include comprehensive `values.yaml` with comments
- Provide working examples in README.md
- Follow Kubernetes and Helm best practices
- Test in development environment before submission

### Security Considerations
- No hardcoded secrets in charts or values
- Use appropriate security contexts and RBAC
- Support Azure Workload Identity where applicable
- Include network policies for pod-to-pod communication

## Documentation

For detailed development guidance, see [CLAUDE.md](./CLAUDE.md) which includes:
- Comprehensive Helm development commands
- Chart structure and standards
- Integration with 40docs platform
- Testing and troubleshooting procedures

## Support

This repository is part of the 40docs platform ecosystem. For issues or questions:
1. Check existing charts for similar patterns
2. Review platform integration requirements
3. Test changes in development environment
4. Follow established chart standards and security practices
