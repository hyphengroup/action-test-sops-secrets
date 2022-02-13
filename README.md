# `test-sops-secrets` GitHub Action

<!-- action-docs-description -->

## Usage

Sample workflow using `hyphengroup/action-test-sops-secrets` to detect and validate changed
[isindir/sops-secrets-operator](https://github.com/isindir/sops-secrets-operator) Custom Resources.

```yaml
name: PR tests
on:
  pull_request:
    branches: [ master ]
jobs:
  test-secrets:
    name: Test modified sops secrets
    runs-on: ubuntu-20.04
    permissions:
      id-token: write
      contents: read
    steps:
    - uses: actions/checkout@master
    - id: foo-secrets
      uses: hyphengroup/action-modified-sops-secrets@v0.1.0
      with:
        files: |
          ^foo/.*\.yaml$
    - uses: mdgreenwald/mozilla-sops-action@v1.1.0
      if: steps.foo-secrets.outputs.any-modified == 'true'
      with:
        version: v3.7.1
    - run: kind create cluster
      if: steps.foo-secrets.outputs.any-modified == 'true'
    - uses: aws-actions/configure-aws-credentials@v1
      if: steps.foo-secrets.outputs.any-modified == 'true'
      with:
        role-to-assume: arn:aws:iam::1234567890:role/foo-kms-decrypt
        aws-region: ap-southeast-1
    - uses: hyphengroup/test-modified-sops-secrets@v0.1.0
      if: steps.foo-secrets.outputs.any-modified == 'true'
      with:
        files: ${{ steps.foo-secrets.outputs.modified-secrets }}
```

<!-- action-docs-inputs -->

<!-- action-docs-outputs -->

<!-- action-docs-runs -->
