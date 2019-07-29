---
layout: classic-docs
title: "Getting a Node.js Project Started with the Node Orb"
short-title: "Node Orb"
description: "Building and Testing with Node.js and the Node Orb"
categories: [language-guides]
order: 5
---

This guide walks through an example CircleCI configuration file (`.circleci/config.yml`) showing how the CircleCI [Node Orb][node-orb] can be used to build and test a Node.js project.
Orbs is a feature of CircleCI v2.1 and as such, CircleCI v2.1 configuration is needed in order to use orbs.

* TOC
{:toc}

## Example Configuration

{% raw %}
```yaml
version: 2.1  # CircleCI v2.1 - needed for orbs
orbs:  # list of orbs to use
  node: circleci/node@1.1.4  # imports the CircleCI Node.js orb as 'node'
jobs:  # a collection of steps that execute in the same environment
  build:  # a job named build
    # this job will run steps in a Docker container provided by the orb
    executor: node/default
    steps: # a collection of executable commands
      - checkout  # `git clone`s the source code to the current directory
      - node/with-cache:
          # Loads the Node cache, if available, runs the below step, and then caches the results
          steps:
            - run: npm install
      - run: # run tests
          name: test
          command: npm test
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
  node: circleci/node@1.1.4
```

The `orbs` key is where we list orbs we want to use in our CircleCI config.
In this example, we import the `circleci/node` orb under the alias of `node`.
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
    executor: node/default
```

This line is the first time we get to use the Node orb.
Instead of manually specifying an [executor][doc-executor] and image to use, the Node orb provides a "default" executor for us to us.
This default executor is the CircleCI [Node.js Docker image][node-img].

```yaml
    steps:
      - checkout
```

Now we start to list out our executions steps.
The first is the special step `checkout` which performs a `git clone` to download our source code to the container.

```yaml
      - node/with-cache:
          steps:
            - run: npm install
```

In this step we use a command from the `node` orb called `with-cache`.
This command restores the NPM cache, allows running one or more steps, and then saves the NPM cache.
The best usecase for this is to run `npm install`.
This allows us to cache the NPM cache on CircleCI to considerably speed up future builds for projects with a large number of dependencies.

```yaml
      - run:
          name: "Test"
          command: npm test
```

A step that runs `npm test` which will run any tests that our NPM project has.

This completes the walk-through of the Node.js example config with the Node orb.
This should be a good starting point for your Node.js app.


## Installing Node.js

In the example config above, we used an executor that was provided by the Node orb.
This executor is a Node.js Docker image that includes Node.js pre-installed.
The Node orb makes installing Node.js in an environment that doesn't already have it very easy.

For example, let's say we had a Go project that also needs Node.js installed.
We can use the Go (Golang) Docker image and use the Node orb in order to install Node.js in that image.
Below is an example of how that would work:

```yaml
version: 2.1
orbs:
  node: circleci/node@1.1.4
jobs:
  build:
    docker:
      - image: circleci/golang:1.12
    steps:
      - checkout
      - node/install
      - run:
          name: "Check Versions"
          command: |
            go version
            node --version
```



[node-orb]: https://circleci.com/orbs/registry/orb/circleci/node
[doc-version]: {{ site.baseurl }}/2.0/configuration-reference/#version
[doc-job]: {{ site.baseurl }}/2.0/configuration-reference/#jobs
[doc-executor]: {{ site.baseurl }}/2.0/executor-types/
[node-img]: https://hub.docker.com/r/circleci/node
