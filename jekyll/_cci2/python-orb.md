---
layout: classic-docs
title: "Getting a Python Project Started with the Python Orb"
short-title: "Python Orb"
description: "Building and Testing with Python and the Python Orb"
categories: [language-guides]
order: 5
---

This guide walks through an example CircleCI configuration file (`.circleci/config.yml`) showing how the CircleCI [Python Orb][python-orb] can be used to build and test a Python project.
Orbs is a feature of CircleCI v2.1 and as such, CircleCI v2.1 configuration is needed in order to use orbs.

* TOC
{:toc}

## Example Configuration

{% raw %}
```yaml
version: 2.1  # CircleCI v2.1 - needed for orbs
orbs:  # list of orbs to use
  python: circleci/python@0.2  # imports the CircleCI Python orb as 'python'
jobs:  # a collection of steps that execute in the same environment
  build:  # a job named build
    # this job will run steps in a Docker container provided by the orb
    executor: python/default
    steps: # a collection of executable commands
      - checkout  # `git clone`s the source code to the current directory
      - python/load-cache  # loads the Pip cache, if available
      - python/install-deps  # Installs Pip packages from requirements.txt
      - python/save-cache  # saves the Pip cache
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
  python: circleci/python@0.2
```

The `orbs` key is where we list orbs we want to use in our CircleCI config.
In this example, we import the `circleci/python` orb under the alias of `python`.
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
    executor: python/default
```

This line is the first time we get to use the Python orb.
Instead of manually specifying an [executor][doc-executor] and image to use, the Python orb provides a "default" executor for us to use.
This default executor is the CircleCI [Python Docker image][python-img].

```yaml
    steps:
      - checkout
```

Now we start to list out our executions steps.
The first is the special step `checkout` which performs a `git clone` to download our source code to the container.

```yaml
      - python/load-cache
```

In this step we use a command from the `python` orb called `load-cache`.
This command restores the Pip package cache if it's available.

```yaml
      - python/install-deps
```

This runs `pip install`.
If the `load-cache` command from the previous step was able to recover a cache, this step will go by very fast since all of the Pip packages will already be installed.
If not, the packages will be installed now.

```yaml
      - python/save-cache
```

This command saves the installed Pip packages to CircleCI.
If the cache doesn't exist, or if `requirements.txt` has changed, this will save a new cache.
Otherwise it won't do anything and simply proceed to the next step.

This completes the walk-through of the Python example config with the Python orb.
This should be a good starting point for your Python app.



[python-orb]: https://circleci.com/orbs/registry/orb/circleci/python
[doc-version]: {{ site.baseurl }}/2.0/configuration-reference/#version
[doc-job]: {{ site.baseurl }}/2.0/configuration-reference/#jobs
[doc-executor]: {{ site.baseurl }}/2.0/executor-types/
[python-img]: https://hub.docker.com/r/circleci/python
