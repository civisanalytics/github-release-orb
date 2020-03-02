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

## Commands
### Publish
Publish a new GitHub Release if the last version does not match the current version.

| Parameter | Type | Description |
| --- | --- | --- |
| `current-version` | string | Current tagged version of the new release |
| `title` | string | Name of the release |
| `description` | string | Description of the release |
| `file-path` | string | File directory to be uploaded in addition to the full repository. |
