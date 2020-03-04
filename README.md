# github-release-orb
Civis Analytics' CircleCI Orb repo.

## Usage

Example Config:

```
version: 2.1

orbs:
  github-release: civisanalytics/github-release@x.y.z

jobs:
  release:
    executor: github-release/github
  steps:
    - github-release/<command>
```

## Build
In order to build the orb you can run the following command in the root directory of this repository.

`circleci config pack src > orb.yml`

Do not commit the created `orb.yml` file. This file is for publishing to CircleCI when finished.

## Required Environment Variables
In order for this orb to work, you must have three Environment Variables set:
1. `GITHUB_TOKEN` - Your GitHub personal access token.
2. `CIRCLE_PROJECT_USERNAME` - GitHub repository's user or organization. This should be set automatically.
3. `CIRCLE_PROJECT_REPONAME` - GitHub repository name. This should be set automatically.

## Commands

### Publish
Publish a new GitHub Release if the last version does not match the current version. This command is useful for running within a custom job. If no custom job is needed, we recommend using the built-in `publish-github-release` job provided by this orb.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `current-version` | string | Current tagged version of the new release | true | |
| `title` | string | Name of the release | true | |
| `description` | string | Description of the release | true | |
| `file-path` | string | File directory to be uploaded in addition to the full repository. | false | `""`|
| `is-draft` | boolean | If true, then the release is a draft. | false | `false` |
| `is-pre-release` | boolean | If true, then the release is a pre-release. | false| `false` |

## Jobs

### publish-github-release
This is a job to publish a new GitHub Release if the last version does not match the current version.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `current-version` | string | Current tagged version of the new release | true | |
| `title` | string | Name of the release | true | |
| `description` | string | Description of the release | true | |
| `file-path` | string | File directory to be uploaded in addition to the full repository. | false | `""`|
| `is-draft` | boolean | If true, then the release is a draft. | false | `false` |
| `is-pre-release` | boolean | If true, then the release is a pre-release. | false| `false` |

### release-by-version
This is a job to publish a new GitHub Release by checking the version file and CHANGELOG files provided. This is to help ease the process of updating the description for the automated releases. Since this orb is intended for use with Python repositories, the `version-path` parameter defaults to `version.py`.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `version-path` | string | File path to file containing the version. If not specified will default to version.py. We will split the line matching version=x.y.z to get the version. | false |  `version.py`|
| `changelog-path` | string | File path to the changelog containing the description of the release. | false | `CHANGELOG.md` |
| `file-path` | string | File directory to be uploaded in addition to the full repository. | false | `""`|
| `is-draft` | boolean | If true, then the release is a draft. | false | `false` |
| `is-pre-release` | boolean | If true, then the release is a pre-release. | false |`false` |

## Dependencies / Requirements
We have checks in place to make sure the following are installed within the container otherwise an error message will be logged and task failed. If your job uses the `github` executor, these dependencies will be installed. Otherwise, we recommend the following Docker images that will ensure these are downloaded:

* cibuilds/github:0.13


### Bash Shell
These commands require `Bash` in order to function. `Bash` is the default shell for CircleCI; therfore, this Orb should be compatible with most images.

### cURL
cURL is used to get TAG data for the GitHub releases and must be installed in the container to function.

### ghr
The tool that does the creation of the GitHub Release.

### awk
Only the `release-by-version` is dependent upon `awk` to function and it is already installed with the executor for this job. We use this in order to parse the version and changelog files.
