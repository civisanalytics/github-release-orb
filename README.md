# github-release-orb
Civis Analytics' CircleCI Orb repo.

## Using the Orb

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
workflows:
  workflow:
    jobs:
      - github-release/<job>:
          <job-parameter>: <value>
```

See `examples` folder for more concrete examples on how to use this orb.

### Required Environment Variables
In order for this orb to work, you must have three Environment Variables set:
1. `GITHUB_TOKEN` - Your GitHub personal access token.
2. `CIRCLE_PROJECT_USERNAME` - GitHub repository's user or organization. This should be set automatically.
3. `CIRCLE_PROJECT_REPONAME` - GitHub repository name. This should be set automatically.

### Commands

#### publish
Publish a new GitHub Release if the last version does not match the current version. This command is useful for running within a custom job. If no custom job is needed, we recommend using the built-in `publish-github-release` job provided by this orb.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `version` | string | Version of the codebase. Orb will create a new github release only if different from the previous version. | true | |
| `title` | string | Name of the release | true | |
| `description` | string | Description of the release | true | |
| `file-path` | string | File directory to be uploaded in addition to the full repository. | false | `""`|
| `is-draft` | boolean | If true, then the release is a draft. | false | `false` |
| `is-pre-release` | boolean | If true, then the release is a pre-release. | false| `false` |

#### set-release-details
Find and set environment variables for a potential github release by parsing the given `version-path` and `changelog-path` parameters.

This will set the following environment variables:
* CURRENT_VERSION - The version of the release
* DESCRIPTION - The description of the release
* TITLE - The title of the release.

Since this orb is intended for use with Python repositories, the `version-path` parameter defaults to `version.py`.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `version-path` | string | File path to file containing the version. If not specified will default to version.py. We will split the line matching `version=x.y.z` to get the version. | false |  `version.py`|
| `changelog-path` | string | File path to the changelog containing the description of the release. The format should follow typical [Changlog Symantics](https://keepachangelog.com/en/1.0.0/). | false | `CHANGELOG.md` |


### Jobs

#### publish-github-release
This is a job to publish a new GitHub Release if the last version does not match the current version.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `version` | string | Version of the codebase. Orb will create a new github release only if different from the previous version. | true | |
| `title` | string | Name of the release | true | |
| `description` | string | Description of the release | true | |
| `file-path` | string | File directory to be uploaded in addition to the full repository. | false | `""`|
| `is-draft` | boolean | If true, then the release is a draft. | false | `false` |
| `is-pre-release` | boolean | If true, then the release is a pre-release. | false| `false` |

#### release-by-version
This is a job to publish a new GitHub Release by checking the version file and CHANGELOG files provided. This is to help ease the process of updating the description for the automated releases. Since this orb is intended for use with Python repositories, the `version-path` parameter defaults to `version.py`.

| Parameter | Type | Description | Required | Default |
| --- | --- | --- | --- | --- |
| `version-path` | string | File path to file containing the version. If not specified will default to version.py. We will split the line matching `version=x.y.z` to get the version. | false |  `version.py`|
| `changelog-path` | string | File path to the changelog containing the description of the release. The format should follow typical [Changlog Symantics](https://keepachangelog.com/en/1.0.0/). | false | `CHANGELOG.md` |
| `file-path` | string | File directory to be uploaded in addition to the full repository. | false | `""`|
| `is-draft` | boolean | If true, then the release is a draft. | false | `false` |
| `is-pre-release` | boolean | If true, then the release is a pre-release. | false |`false` |

### Dependencies / Requirements
We have checks in place to make sure the following are installed within the container otherwise an error message will be logged and task failed. If your job uses the `github` executor, these dependencies will be installed. Otherwise, we recommend the following Docker images that will ensure these are downloaded:

* [cibuilds/github:0.13](https://github.com/cibuilds/github/blob/master/0.13/Dockerfile)


#### Bash Shell
These commands require `Bash` in order to function. `Bash` is the default shell for CircleCI; therfore, this Orb should be compatible with most images.

#### cURL
cURL is used to get TAG data for the GitHub releases and must be installed in the container to function.

#### ghr
The tool that does the creation of the GitHub Release.

#### awk
Only the `release-by-version` is dependent upon `awk` to function and it is already installed with the executor for this job. We use this in order to parse the version and changelog files.

## Development

#### Prerequisites
Make sure you have CircleCI CLI installed on your machine. If you don't, you can follow [Setting Up the CircleCI CLI](https://circleci.com/docs/2.0/orb-author-cli/#section=configuration) to get setup.

### Building and Publishing the Orb
Once ready to publish a new version of the orb you can follow these steps:

1. Build the orb by running: `circleci config pack src > orb.yml`. Do not commit the created `orb.yml` file. This file is for publishing to CircleCI when finished.
2. Once the orb is built, we can verify the orb is valid by running `circleci orb validate orb.yml`.
3. Publish a dev version of the orb by running `circleci orb publish orb.yml civisanalytics/github-release@dev:<version>`. This dev version is available only to civisanalytics and will expire 90 days after each publish. This can be used for testing the orb before a final release.
4. When ready to publish the orb, the orb owner can run `circleci orb publish promote civisanalytics/github-release@dev:<version> <segment>` where `segment` is either `major`, `minor`, or `patch` and it will auto-increment the published version of the orb.

For more information about publishing orbs see the CircleCI docs on [Publishing Orbs](https://circleci.com/docs/2.0/creating-orbs/#section=configuration).
