# Terragrunt Template Catalog for GCP

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![GitHub Release](https://img.shields.io/github/release/ConsciousML/terragrunt-template-catalog-gcp.svg?style=flat)]()
[![CI](https://github.com/ConsciousML/terragrunt-template-catalog-gcp/actions/workflows/ci.yaml/badge.svg)](https://github.com/ConsciousML/terragrunt-template-catalog-gcp/actions/workflows/ci.yaml)
[![PR's Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat)](http://makeapullrequest.com)

A Terragrunt Template Catalog for production Infrastructure as Code (IaC) on Google Cloud Platform (GCP).

## Catalog vs Live Infrastructure

This is a **catalog repository**, a collection of reusable IaC components.

This toolkit uses two template repositories:
- **This repository** (catalog): Build a collection of reusable [modules](./modules), [units](./units/), and [stacks](./stacks/)
- **Live repository**: Reference these components your the [infrastructure-live repository](https://github.com/ConsciousML/terragrunt-template-live-gcp) to deploy them in a multi-environment ecosystem with production CI/CD

You're new to Terragrunt best practices? Read [Gruntwork's official production patterns](https://github.com/gruntwork-io/terragrunt-infrastructure-catalog-example) to get the foundations required to use this extended repository.

## What's Inside

The catalog follows a layered architecture where each layer builds upon the previous one:

```
Modules (modules/) → Units (units/) → Stacks (stacks/) → Examples (examples/)
```

- **[Modules](modules/README.md)**: Reusable Terraform modules that declare GCP resources (VPC, databases, compute instances, etc.)
- **[Units](units/README.md)**: Terragrunt wrappers around modules that add configuration and dependencies
- **[Stacks](stacks/README.md)**: Collections of units arranged in dependency graphs for pattern level re-use
- **[Examples](examples/README.md)**: Simple configuration for testing and development
- **[Bootstrap](bootstrap/README.md)**: GitHub Actions GCP authentication setup
- **[CI](docs/continuous-integration.md)**: Automated configuration validation, testing (`terratest`) and documatentation (`terraform-docs`).

## Getting Started
### Prerequisites
- GCP account with billing enabled
- GitHub account
- GCP IAM permissions to create service accounts, network, compute, and workload identity pools

### Fork the Repository
First, you'll need to fork this repository and make a few changes:
1. Click on `Use this template` to create your own repository
2. Use your IDE of choice to replace every occurrence of `github.com/ConsciousML/terragrunt-template-catalog-gcp` and `git::git@github.com:ConsciousML/terragrunt-template-catalog-gcp.git` by your GitHub repo URL following the same format
3. In `examples/` change `region.hcl` and `project.hcl` to match your GCP settings

**Warning**: If you skip step 2, the TG source links will still point to the original repository (on `github.com/ConsciousML/`).

### Installation

**Option 1: Use mise (recommended)**

First, `cd` at the root of this repository. 

Next, install mise:
```bash
curl https://mise.run | sh
```

Then, install all the tools in the `mise.toml` file:
```bash
mise install
```

Finally, run the following to automatically activate mise when starting a shell:
- For zsh: 
```bash
echo 'eval "$(~/.local/bin/mise activate zsh)"' >> ~/.zshrc && source ~/.zshrc
```
- For bash:
```bash
echo 'eval "$(~/.local/bin/mise activate bash)"' >> ~/.bashrc && source ~/.bashrc
```

For more information on how to use mise, read their [getting started guide](https://mise.jdx.dev/getting-started.html).


**Option 2: Install Tools Manually**
- [Terragrunt](https://terragrunt.gruntwork.io/docs/getting-started/install/)
- [OpenTofu](https://opentofu.org/docs/intro/install/) (or [Terraform](https://developer.hashicorp.com/terraform/install))
- [Go](https://go.dev/doc/install)
- [Python3.13.1](https://www.python.org/downloads/)
- [tflint](https://github.com/terraform-linters/tflint)
- [GitHub CLI](https://github.com/cli/cli#installation)
- [gcloud CLI](https://cloud.google.com/sdk/docs/install)

See [mise.toml](./mise.toml) for specific versions.

### Authenticate with GCP
```bash
gcloud auth login
gcloud auth application-default login
```

### Deploy an Example Architecture

Deploy a stack that activates GCP APIs, sets up a VPC, and spawns a Compute Engine:

```bash
cd examples/stacks/vpc_gce
terragrunt stack generate
terragrunt stack run apply --backend-bootstrap --non-interactive
```

- Go into the GCP console and check that your resources have been created
- Then cleanup by destroying the infrastructure:

```bash
terragrunt stack generate
terragrunt stack run destroy --non-interactive
```

**Caution**: This workflow is for development and testing. Reference your catalog components in the [infrastructure-live repository](https://github.com/ConsciousML/terragrunt-template-live-gcp) for multi-environment IaC, and production CI/CD.

## Development Workflow

1. Create a feature branch
2. Write/modify modules, units, and stacks
3. Test locally using examples
4. Create pull request
5. Merge when CI passes

See the [development guide](docs/development.md) for a detailed workflow with a step-by-step example on how to modify this template.

## Continuous Integration (CI)

After creating your repository from this template, run the [bootstrap process](bootstrap/README.md) once to configure GitHub Actions authentication with GCP.

The CI provides automated checks and testing:
1. Create a branch and make changes
2. Open a pull request to trigger code quality checks
3. Add the `run-terratest` label for full infrastructure testing
4. Merge when all checks pass

Read more in our [CI workflow guide](docs/continuous-integration.md).

### Infrastructure Testing

The `run-terratest` label triggers automated infrastructure tests that deploy real GCP resources, validate functionality, and clean up automatically.

See the [testing guide](tests/README.md) for writing custom tests.

### Pre-commit Setup (recommended)
```bash
pre-commit install
```

This runs the same checks as CI locally, catching issues before you push.

## License
This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
