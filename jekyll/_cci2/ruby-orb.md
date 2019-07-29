---
layout: classic-docs
title: "Getting a Ruby Project Started with the Ruby Orb"
short-title: "Ruby Orb"
description: "Building and Testing with Ruby and the Ruby Orb"
categories: [language-guides]
order: 5
---

This guide walks through an example CircleCI configuration file (`.circleci/config.yml`) showing how the CircleCI [Ruby Orb][ruby-orb] can be used to build and test a Ruby project.
Orbs is a feature of CircleCI v2.1 and as such, CircleCI v2.1 configuration is needed in order to use orbs.

* TOC
{:toc}

## Example Configuration

{% raw %}
```yaml
version: 2.1  # CircleCI v2.1 - needed for orbs
orbs:  # list of orbs to use
  ruby: circleci/ruby@0.1.0  # imports the CircleCI Ruby orb as 'ruby'
jobs:  # a collection of steps that execute in the same environment
  build:  # a job named build
    # this job will run steps in a Docker container provided by the orb
    executor: ruby/default
    steps: # a collection of executable commands
      - checkout  # `git clone`s the source code to the current directory
      - ruby/load-cache  # loads the Rubygem cache, if available
      - ruby/bundle-install  # installs gems from `Gemfile` with Bundler
      - ruby/save-cache  # saves the Rubygem cache
      - ruby/test  # Run RSpec tests for the project
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
  ruby: circleci/ruby@0.1.0
```

The `orbs` key is where we list orbs we want to use in our CircleCI config.
In this example, we import the `circleci/ruby` orb under the alias of `ruby`.
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
    executor: ruby/default
```

This line is the first time we get to use the Ruby orb.
Instead of manually specifying an [executor][doc-executor] and image to use, the Ruby orb provides a "default" executor for us to us.
This default executor is the CircleCI [Ruby Docker image][ruby-img].

```yaml
    steps:
      - checkout
```

Now we start to list out our executions steps.
The first is the special step `checkout` which performs a `git clone` to download our source code to the container.

```yaml
      - ruby/load-cache
```

In this step we use a command from the `ruby` orb called `load-cache`.
This command restores the Rubygem cache if it's available.

```yaml
      - ruby/bundle-install
```

This runs `bundle install`.
If the `load-cache` command from the previous step was able to recover a cache, this step will go by very fast since all of the gems will already be installed.
If not, we do a normal installation of gems for our project.

```yaml
      - ruby/save-cache
```

This command saves the Gem cache to CircleCI.
If the cache doesn't exist, or the Gemfile has changed, this will save a new cache.
Otherwise it won't do anything and simply proceed to the next step.

```yaml
      - ruby/test
```

Runs rspec via bundler to run tests.
This command will also store your test results to CircleCI.
More information on how CircleCI stores results can be found [here][doc-test-results].

This completes the walk-through of the Ruby example config with the Ruby orb.
This should be a good starting point for your Ruby app.


## Installing Ruby

In the example config above, we used an executor that was provided by the Ruby orb.
This executor is a Docker image that has Ruby pre-installed.
The Ruby orb makes installing Ruby in an environment that doesn't already have it very easy.

For example, let's say we had a Python project that also needs Ruby installed.
We can use the Python Docker image and use the Ruby orb in order to install Ruby in that image.
Below is an example of how that would work:

```yaml
version: 2.1
orbs:
  node: circleci/ruby@0.1.0
jobs:
  build:
    docker:
      - image: circleci/python:3
    steps:
      - checkout
      - ruby/install
      - run:
          name: "Check Versions"
          command: |
            python --version
            ruby --version
```



[ruby-orb]: https://circleci.com/orbs/registry/orb/circleci/ruby
[doc-version]: {{ site.baseurl }}/2.0/configuration-reference/#version
[doc-job]: {{ site.baseurl }}/2.0/configuration-reference/#jobs
[doc-executor]: {{ site.baseurl }}/2.0/executor-types/
[ruby-img]: https://hub.docker.com/r/circleci/ruby
[doc-test-results]: https://circleci.com/docs/2.0/collect-test-data/
