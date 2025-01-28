# Validate

## Behavior

This action installs the OpenTofu CLI with either the given or the `latest` version by default. It then checks the
following items for the OpenTofu configuration files in the given infrastructure directory:

- File formatting using `tofu fmt`
- Validation using `tofu validate`
- Linting using `TFLint`
- Security using `trivy` (can be skipped by setting `skip-trivy` to `true`)

If any of the steps fail, the entire action fails. If the action is run in a pull request, a comment displaying the
validation result will be created or updated.

> [!IMPORTANT]
> This action disables the OpenTofu CLI wrapper.

## Example usage

```yaml
name: OpenTofu validate

on:
  pull-request:
    branches:
      - main

jobs:
  validate:
    name: OpenTofu validate
    runs-on: ubuntu-latest
    env:
      OPENTOFU_VERSION: '~> 1.9.0'
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: OpenTofu validate
        uses: ThorstenSauter/opentofu-actions/validate@v1
        with:
          opentofu-version: ${{ env.OPENTOFU_VERSION }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          infra-directory: ${{ vars.INFRA_DIRECTORY }}
```
