# npm-publish-from-semver-increment-workflow
[![Git Tag Semver From Label](https://github.com/infrastructure-blocks/npm-publish-from-semver-increment-workflow/actions/workflows/git-tag-semver-from-label.yml/badge.svg)](https://github.com/infrastructure-blocks/npm-publish-from-semver-increment-workflow/actions/workflows/git-tag-semver-from-label.yml)
[![Update From Template](https://github.com/infrastructure-blocks/npm-publish-from-semver-increment-workflow/actions/workflows/update-from-template.yml/badge.svg)](https://github.com/infrastructure-blocks/npm-publish-from-semver-increment-workflow/actions/workflows/update-from-template.yml)

<<<<<<< HEAD
This workflow publishes npm packages based on a semver increment. It is meant to be used in conjunction with the
[check-has-semver-label-workflow](https://github.com/infrastructure-blocks/check-has-semver-label-workflow).

- The workflow dispatches to
[npm-publish-prerelease-workflow](https://github.com/infrastructure-blocks/npm-publish-prerelease-workflow) if
`prerelease` is set to true.
  - The distribution tags used for pre releases are `git-sha-<sha>` and `gh-pr-<current-pr-number>`.  
- The workflow dispatches to [npm-publish-workflow](https://github.com/infrastructure-blocks/npm-publish-workflow) if
`prerelease` is set to false.
  - The distribution tags used for releases are `latest`, `git-sha-<sha>` and `gh-pr-<current-pr-number>`.
- The git SHA is taken from the `github.even.pull_request.head.sha` if the event triggering this workflow is of type
`pull_request`. Otherwise, it defaults to the `github.sha`. We expect pre release flows to be triggered by PRs and
release flows to be triggered by pushes.

The outcome of the publication is reported as a
[status report](https://github.com/infrastructure-blocks/status-report-action).
=======
This repository is a template for creating reusable GitHub Actions Workflows. Go through the below checklist
upon instantiating this template:
- Remove the [trigger update from template workflow](.github/workflows/trigger-update-from-template.yml)
- Edit the content of [the placeholder](.github/workflows/workflow.yml) for your reusable workflow.
- Update the status badges:
    - Remove the `Trigger Update From Template` status badge.
    - Add the `Update From Template` status badge.
    - Rename the rest of the links to point to the right repository.
- Edit this document and update the relevant sections
- Prepare the [changelog](CHANGELOG.md) for the first version of the module that will be released.
>>>>>>> template/master

## Inputs

|       Name       | Required | Description                                                                                                                                                                                                                                                |
|:----------------:|:--------:|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    prerelease    |  false   | Whether the label is to be interpreted as a prerelease or a full release. Defaults to false.                                                                                                                                                               |
| semver-increment |   true   | The semver increment that indicates which release version will be produced. Should be one of "major", "minor" or "patch". When `prerelease` is `true`, the actual semver increment will be `pre<semver-increment`. So `premajor` for `major`, for example. |
|       skip       |  false   | A boolean indicating whether to skip the workflow. This is to workaround the required checks discrepancy when the workflow is skipped from the caller. It defaults to false.                                                                               |
|     skip-ci      |  false   | Whether to include `[skip ci]` in the commit created by `npm version` when `prerelease` is false. This is especially useful if using this workflow on a push event with a GitHub PAT, for example. Defaults to false.                                      |

## Secrets

|     Name     | Required | Description                                                                                                                                                       |
|:------------:|:--------:|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| github-token |   true   | The GitHub token used to configure the Git CLI. It should have the rights to push code and tags. When the branch or the tags are protected, this should be a PAT. |
|  npm-token   |   true   | The NPM token used to publish the package.                                                                                                                        |

## Outputs

N/A

## Permissions

|     Scope     | Level | Reason                                                                                                   |
|:-------------:|:-----:|----------------------------------------------------------------------------------------------------------|
|   contents    | write | Required to push code when `prerelease` is false and the `github-token` provided is ${{ github.token }}. |
| pull-requests | write | Required to post comments about the status of this workflow.                                             |

## Concurrency controls

N/A

## Timeouts

N/A

## Usage

### Prerelease

```yaml
name: PR Checks

on:
  pull_request:
    types:
      - opened
      - reopened
      - synchronize
      - labeled
      - unlabeled

jobs:
  check-has-semver-label:
    permissions:
      pull-requests: write
    uses: infrastructure-blocks/check-has-semver-label-workflow/.github/workflows/workflow.yml@v1
  npm-publish-prerelease:
    needs:
      - check-has-semver-label
    uses: infrastructure-blocks/npm-publish-from-semver-increment-workflow/.github/workflows/workflow.yml@v3
    permissions:
      contents: write
      pull-requests: write
    with:
      prerelease: true
      semver-increment: ${{ needs.check-has-semver-label.outputs.matched-label }}
      skip: ${{ needs.check-has-semver-label.outputs.matched-label == 'no version' }}
    secrets:
      # Should use the default token here, since we are not pushing anything.
      github-token: ${{ github.token }}
      npm-token: ${{ secrets.NPM_PUBLISH_TOKEN }}
```

### Release

```yaml
name: NPM Publish Release From Label

on:
  push:
    branches:
      - master

jobs:
  check-has-semver-label:
    permissions:
      pull-requests: write
    uses: infrastructure-blocks/check-has-semver-label-workflow/.github/workflows/workflow.yml@v1
  npm-publish-release:
    needs:
      - check-has-semver-label
    uses: infrastructure-blocks/npm-publish-from-semver-increment-workflow/.github/workflows/workflow.yml@v3
    permissions:
      contents: write
      pull-requests: write
    with:
      semver-increment: ${{ needs.check-has-semver-label.outputs.matched-label }}
      skip: ${{ needs.check-has-semver-label.outputs.matched-label == 'no version' }}
      skip-ci: true
    secrets:
      github-token: ${{ secrets.PAT }}
      npm-token: ${{ secrets.NPM_PUBLISH_TOKEN }}
```

### Releasing

The releasing is handled at git level with semantic versioning tags. Those are automatically generated and managed
by the [git-tag-semver-from-label-workflow](https://github.com/infrastructure-blocks/git-tag-semver-from-label-workflow).
