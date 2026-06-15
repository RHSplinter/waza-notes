---
applyTo: '.github/workflows/**/*.yml,.github/workflows/**/*.yaml'
---

# GitHub Actions Workflow Instructions

## Workflow Structure

- Place workflows in `.github/workflows/`
- Use descriptive workflow names
- Document workflow purpose with comments
- Use consistent naming: `<action>-<component>.yml`

## Naming Conventions

- Job names: lowercase with hyphens
- Step names: descriptive action descriptions
- Environment variables: UPPER_CASE

## Security Best Practices

- Use GitHub secrets for sensitive values
- Never hardcode credentials or tokens
- Use minimal permissions (`permissions:` block)
- Use `secrets: inherit` for reusable workflows
- Always get the latest release of an action
- Pin action versions to specific SHAs or versions

## Common Patterns

### .NET Build and Deploy

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-dotnet@v5
        with:
          dotnet-version: '9.0.x'
      - run: dotnet restore
      - run: dotnet build --no-restore
      - run: dotnet test --no-build
```


## Environment Configuration

- Use GitHub environments for deployment targets
- Configure environment protection rules
- Use environment secrets for environment-specific values

## Azure Authentication

Use Azure OIDC for authentication:

```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: azure/login@v2
    with:
      client-id: ${{ secrets.AZURE_CLIENT_ID }}
      tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

## Reusable Workflows

- Define reusable workflows with `workflow_call`
- Use inputs for configuration
- Use secrets for sensitive values
- Document required inputs and outputs

## Composite Actions

- Place in `.github/actions/<action-name>/`
- Include `action.yml` with inputs/outputs
- Use separate scripts for complex logic
- Document usage in `README.md`