# Apply

## Behavior

This action installs the OpenTofu CLI with either the given or the `latest` version by default. It then executes the
`init` command in the given infrastructure directory. One example of its usage is retrieving output variables.

> [!IMPORTANT]
> This action disables the OpenTofu CLI wrapper.

## Example usage

```yaml
name: Retrieve OpenTofu output

on:
  pull-request:
    branches:
      - main

permissions:
  contents: read
  id-token: write # Required for OIDC

jobs:
  retrieve-output:
    name: Retrieve OpenTofu output
    runs-on: ubuntu-latest
    environment: production
    env:
      ARM_CLIENT_ID: ${{ vars.AZURE_CLIENT_ID }}
      ARM_SUBSCRIPTION_ID: ${{ vars.AZURE_SUBSCRIPTION_ID }}
      ARM_TENANT_ID: ${{ vars.AZURE_TENANT_ID }}
      ARM_USE_AZUREAD: true
      ARM_USE_OIDC: true
      OPENTOFU_VERSION: '~> 1.9.0'
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: OpenTofu init
        uses: ThorstenSauter/opentofu-actions/init@v1
        with:
          opentofu-version: ${{ env.OPENTOFU_VERSION }}
          infra-directory: ${{ vars.INFRA_DIRECTORY }}
          resource-group: ${{ vars.TF_BACKEND_RESOURCE_GROUP_NAME }}
          storage-account: ${{ vars.TF_BACKEND_STORAGE_ACCOUNT_NAME }}
          container: ${{ vars.TF_BACKEND_STATE_CONTAINER_NAME }}
          state-file: ${{ vars.TF_BACKEND_STATE_FILE_NAME }}
      - name: OpenTofu apply refresh only
        working-directory: ${{ vars.TF_WORKING_DIRECTORY }}
        run: tofuu apply -refresh-only -auto-approve -input=false
      - name: Retrieve deployment token
        id: token-retrieval
        working-directory: ${{ vars.TF_WORKING_DIRECTORY }}
        run: |
          token=$(opentofu output -raw deployment_token)
          echo "::add-mask::$token"
          echo "deployment-token=$token" >> "$GITHUB_OUTPUT"
```
