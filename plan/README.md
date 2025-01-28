# Plan

## Behavior

This action installs the OpenTofu CLI with either the given or the `latest` version by default. It then executes the
`plan` command in the given infrastructure directory.

## Example usage

```yaml
name: OpenTofu plan

on:
  pull-request:
    branches:
      - main

permissions:
  contents: read
  id-token: write # Required for OIDC
  pull-requests: write # Required for pull request comment

jobs:
  plan:
    name: OpenTofu plan
    runs-on: ubuntu-latest
    environment: production
    env:
      ARM_CLIENT_ID: ${{ vars.AZURE_CLIENT_ID }}
      ARM_SUBSCRIPTION_ID: ${{ vars.AZURE_SUBSCRIPTION_ID }}
      ARM_TENANT_ID: ${{ vars.AZURE_TENANT_ID }}
      ARM_USE_AZUREAD: true
      ARM_USE_OIDC: true
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: OpenTofu plan
        uses: ThorstenSauter/opentofu-actions/plan@v1
        with:
          environment-name: 'Production'
          github-token: ${{ secrets.GITHUB_TOKEN }}
          infra-directory: ${{ vars.INFRA_DIRECTORY }}
          resource-group: ${{ vars.TF_BACKEND_RESOURCE_GROUP_NAME }}
          storage-account: ${{ vars.TF_BACKEND_STORAGE_ACCOUNT_NAME }}
          container: ${{ vars.TF_BACKEND_STATE_CONTAINER_NAME }}
          state-file: ${{ vars.TF_BACKEND_STATE_FILE_NAME }}
```
