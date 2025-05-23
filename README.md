﻿# OpenTofu Actions

This repository contains common OpenTofu GitHub Actions to be reused across `ThorstenSauter` projects.

## General

All the included actions assume a OpenTofu backend of type `azurerm`, meaning the OpenTofu state file is being stored
in an Azure Blob Storage container.

They also assume that for actions interacting with the OpenTofu
state [OIDC federation between GitHub Actions and Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure)
will be used to authenticate to the storage backend.
This requires a few environment variables to be set up before the actions are used:

- `ARM_CLIENT_ID=<client id of the service principal or managed identity>`
- `ARM_SUBSCRIPTION_ID=<id of the Azure subscription>`
- `ARM_TENANT_ID=<tenant id of the Microsoft Entra ID tenant>`
- `ARM_USE_AZUREAD=true`
- `ARM_USE_OIDC=true`

## Input variables

The actions themselves do not accept OpenTofu input variables.
However, they can be injected
using [environment variables](https://developer.hashicorp.com/opentofu/language/values/variables#environment-variables)
as usual.

E.g., to set a variable with a name of `example`, specifying an environment variable named `TF_VAR_example`
will make the value available to the actions.

### Example

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
        uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
      - name: OpenTofu plan
        uses: ThorstenSauter/opentofu-actions/plan@v1
        env:
          TF_VAR_example: 'example value'
        with:
          environment-name: 'Production'
          github-token: ${{ secrets.GITHUB_TOKEN }}
          infra-directory: ${{ vars.INFRA_DIRECTORY }}
          resource-group: ${{ vars.TF_BACKEND_RESOURCE_GROUP_NAME }}
          storage-account: ${{ vars.TF_BACKEND_STORAGE_ACCOUNT_NAME }}
          container: ${{ vars.TF_BACKEND_STATE_CONTAINER_NAME }}
          state-file: ${{ vars.TF_BACKEND_STATE_FILE_NAME }}
```

## Actions

The repository currently contains four actions.

- [Init](./init) `ThorstenSauter/opentofu-actions/init@v1`
- [Validate](./validate) `ThorstenSauter/opentofu-actions/validate@v1`
- [Plan](./plan) `ThorstenSauter/opentofu-actions/plan@v1`
- [Apply](./apply) `ThorstenSauter/opentofu-actions/apply@v1`
