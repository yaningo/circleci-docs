---
layout: classic-docs
title: "Getting a Go (Golang) Project Started with the Go Orb"
short-title: "Go Orb"
description: "Building and Testing with Go and the Go Orb"
categories: [language-guides]
order: 5
---

This guide walks through an example CircleCI configuration file (`.circleci/config.yml`) showing how the CircleCI [Go Orb][go-orb] can be used to build and test a Go project.
Orbs is a feature of CircleCI v2.1 and as such, CircleCI v2.1 configuration is needed in order to use orbs.

* TOC
{:toc}

## Example Configuration

{% raw %}
```yaml
version: 2.1  # CircleCI v2.1 - needed for orbs
orbs:  # list of orbs to use
  go: circleci/go@0.2  # imports the CircleCI Go orb as 'go'
jobs:  # a collection of steps that execute in the same environment
  build:  # a job named build
    # this job will run steps in a Docker container provided by the orb
    executor: go/default
    steps: # a collection of executable commands
      - checkout  # `git clone`s the source code to the current directory
      - go/load-cache  # loads the go mod cache, if available
      - go/mod-download  # Downloads modules from go.mod
      - go/save-cache  # saves the go mod cache
      - go/test  # Runs `go test` for the project
```
{% endraw %}


## Walk-through

```yaml
version: 2.1
```

Every CircleCI config should specify a version via the [version][doc-version] key.
In order to use orbs, version 2.1 or later is required.

```yaml
orbs:
  go: circleci/go@0.2
```

The `orbs` key is where we list orbs we want to use in our CircleCI config.
In this example, we import the `circleci/go` orb under the alias of `go`.
Using this orb later, we just need to refer to it by its alias.
A full or partial SemVer version can be used here.

```yaml
jobs:
  build:
```

The `jobs` key list all the jobs that will run in parallel or sequentially for a pipeline.
In this case we have a single job named `build`.
A [job][doc-job] is a collection of steps that run sequentially in the same execution environment.

```yaml
    executor: go/default
```

This line is the first time we get to use the Go orb.
Instead of manually specifying an [executor][doc-executor] and image to use, the Go orb provides a "default" executor for us to us.
This default executor is the CircleCI [Go Docker image][go-img].

```yaml
    steps:
      - checkout
```

Now we start to list out our executions steps.
The first is the special step `checkout` which performs a `git clone` to download our source code to the container.

```yaml
      - go/load-cache
```

In this step we use a command from the `go` orb called `load-cache`.
This command restores the Go module cache if it's available.

```yaml
      - go/mod-download
```

This runs `go mod download`.
If the `load-cache` command from the previous step was able to recover a cache, this step will go by very fast since all of the Go packages will already be installed.
If not, all packages will be downloaded to the GOPATH.

```yaml
      - go/save-cache
```

This command saves the downloaded Go packages/modules to CircleCI.
If the cache doesn't exist, or if `go.mod` has changed, this will save a new cache.
Otherwise it won't do anything and simply proceed to the next step.

```yaml
      - go/test
```

Runs `go test` for you.

This completes the walk-through of the GO example config with the Go orb.
This should be a good starting point for your Go app.


## Installing Go

In the example config above, we used an executor that was provided by the Go orb.
This executor is a Docker image that has Go pre-installed.
The Go orb makes installing Go in an environment that doesn't already have it very easy.

For example, let's say we had a Python project that also needs Go installed.
We can use the Python Docker image and use the Go orb in order to install Go in that image.
Below is an example of how that would work:

```yaml
version: 2.1
orbs:
  go: circleci/go@0.2
jobs:
  build:
    docker:
      - image: circleci/python:3
    steps:
      - checkout
      - go/install
      - run:
          name: "Check Versions"
          command: |
            python --version
            go version
```



[go-orb]: https://circleci.com/orbs/registry/orb/circleci/go
[doc-version]: {{ site.baseurl }}/2.0/configuration-reference/#version
[doc-job]: {{ site.baseurl }}/2.0/configuration-reference/#jobs
[doc-executor]: {{ site.baseurl }}/2.0/executor-types/
[go-img]: https://hub.docker.com/r/circleci/golang
