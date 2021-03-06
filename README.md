# Git Branch `HEAD`s Resource

Tracks changes made to all branches (or branches matching a filter). This will
skip commits on individual branches, but ensure a version is emitted for each
change to the branches.

**This resource is meant to be used with [`version:
every`](https://concourse.ci/get-step.html#get-version).**

## Installation

Add the following `resource_types` entry to your pipeline:

```yaml
---
resource_types:
- name: git-branch-heads
  type: docker-image
  source: {repository: vito/git-branch-heads-resource}
```

## Source Configuration

All source configuration is based on the [Git
resource](https://github.com/concourse/git-resource), with the addition of the
following property:

* `branches`: *Optional.* An array of branch name filters. If not specified,
  all branches are tracked.

The `branch` configuration from the original resource is ignored for `check`.


### Example

Resource configuration for a repo with a bunch of branches named `wip-*`:

``` yaml
resources:
- name: my-repo-with-feature-branches
  type: git-branch-heads
  source:
    uri: https://github.com/concourse/atc
    branches: [wip-*]
```

## Behavior


### `check`: Check for changes to all branches.

The repository is cloned (or pulled if already present), all remote branches
are enumerated, and compared to the existing set of branches.

If any branches are new or removed, a new version is emitted including the
delta.

### `in`: Fetch the commit that changed the branch.

This resource delegates entirely to the `in` of the original Git resource, by
specifying `source.branch` as the branch that changed, and `version.ref` as the
commit on the branch.

All `params` and `source` configuration of the original resource will be
respected.


### `out`: No-op.

*Not implemented.*
