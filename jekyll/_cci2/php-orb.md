---
layout: classic-docs
title: "Getting a PHP Project Started with the PHP Orb"
short-title: "PHP Orb"
description: "Building and Testing with PHP and the PHP Orb"
categories: [language-guides]
order: 5
---

This guide walks through an example CircleCI configuration file (`.circleci/config.yml`) showing how the CircleCI [PHP Orb][php-orb] can be used to build and test a PHP project.
Orbs is a feature of CircleCI v2.1 and as such, CircleCI v2.1 configuration is needed in order to use orbs.

* TOC
{:toc}

## Example Configuration

{% raw %}
```yaml
version: 2.1  # CircleCI v2.1 - needed for orbs
orbs:  # list of orbs to use
  php: circleci/php@0.1  # imports the CircleCI PHP orb as 'php'
jobs:  # a collection of steps that execute in the same environment
  build:  # a job named build
    # this job will run steps in a Docker container provided by the orb
    executor: php/default
    steps: # a collection of executable commands
      - checkout  # `git clone`s the source code to the current directory
      - php/load-cache  # loads the Composer cache, if available
      - php/save-cache  # saves the Composer cache
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
  php: circleci/php@0.1
```

The `orbs` key is where we list orbs we want to use in our CircleCI config.
In this example, we import the `circleci/php` orb under the alias of `php`.
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
    executor: php/default
```

This line is the first time we get to use the PHP orb.
Instead of manually specifying an [executor][doc-executor] and image to use, the PHP orb provides a "default" executor for us to use.
This default executor is the CircleCI [Go Docker image][php-img].

```yaml
    steps:
      - checkout
```

Now we start to list out our executions steps.
The first is the special step `checkout` which performs a `git clone` to download our source code to the container.

```yaml
      - php/load-cache
```

In this step we use a command from the `php` orb called `load-cache`.
This command restores the Composer cache if it's available.

```yaml
      - php/save-cache
```

This command saves the downloaded Composer packages to CircleCI.
If the cache doesn't exist, or if `composer.json` has changed, this will save a new cache.
Otherwise it won't do anything and simply proceed to the next step.

This completes the walk-through of the PHP example config with the PHP orb.
This should be a good starting point for your PHP app.


## Installing PHP

In the example config above, we used an executor that was provided by the PHP orb.
This executor is a Docker image that has PHP pre-installed.
The PHP orb makes installing PHP in an environment that doesn't already have it very easy.
The following works for any recent release of Ubuntu or Debian.

For example, let's say we had a Python projiect that also needs PHP installed.
We can use the Python Docker image and use the PHP orb in order to install PHP in that image.
Below is an example of how that would work:

```yaml
version: 2.1
orbs:
  php: circleci/php@0.1
jobs:
  build:
    docker:
      - image: circleci/python:3
    steps:
      - checkout
      - php/install
      - run:
          name: "Check Versions"
          command: |
            python --version
            php --version
```


## Installing Composer

Similar to the previous section, the PHP orb can be used to install Composer in an image that doesn't already have it.
This has the same requirements and works similarly.

Building on our previous example, we add an additional step to install Composer.

```yaml
version: 2.1
orbs:
  php: circleci/php@0.1
jobs:
  build:
    docker:
      - image: circleci/python:3
    steps:
      - checkout
      - php/install
      - php/install-composer
      - run:
          name: "Check Versions"
          command: |
            python --version
            php --version
            composer --version
```

As Composer is written in PHP, the `php/install` step **must** come before the `php/install-composer` step in order for it to work.



[php-orb]: https://circleci.com/orbs/registry/orb/circleci/php
[doc-version]: {{ site.baseurl }}/2.0/configuration-reference/#version
[doc-job]: {{ site.baseurl }}/2.0/configuration-reference/#jobs
[doc-executor]: {{ site.baseurl }}/2.0/executor-types/
[php-img]: https://hub.docker.com/r/circleci/php
