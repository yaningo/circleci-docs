# Local Development Instructions

This document covers how to locally develop our Jekyll-based docs as well as the Slate API documentation.
There are two ways to work on CircleCI docs locally: with Docker and with [Ruby](https://www.ruby-lang.org/en/)/[Bundler](http://bundler.io/).

## 1. Local Development with Docker (recommended)

**Prerequisites:**

- [Install Docker](https://docs.docker.com/engine/installation/) for your platform.
- [Install node/npm](https://nodejs.org/en/) for your platform

Run the following commands:

```sh
git clone https://github.com/circleci/circleci-docs.git # clone the repo
cd circleci-docs # change directory into the repo

# This generates a bundle file to get compiled into jekyll.
# this currently isn't ideal, but works
# TODO - this should get handled by docker-compose.
npm install
npm run webpack-watch
docker-compose up # boot up docker

# docs are now running at `http://localhost:4000/docs`
```


**Note:** If you want to submit a pull request to update the docs, you'll need to [make a fork](https://github.com/circleci/circleci-docs#fork-destination-box) of this repo and clone _your version_ instead of the original repo as listed above. Then, when you push your changes to your fork you can submit a pull request to us.


## 2. Local Development with Ruby and Bundler (alternative to Docker)

**Prerequisites:**

- Ruby (currently ruby '2.5.1') - consider using [rvm](https://github.com/rvm/rvm) or [rbenv](https://github.com/rbenv/rbenv) to manage ruby versions.
- An installation of Node
- An installation of [Jekyll](https://jekyllrb.com/docs/installation/)

First, install js dependencies and create the app.bundle.js file jekyll requires.

```sh
git clone https://github.com/circleci/circleci-docs.git
cd circleci-docs
npm install
npm run webpack-prod

# if you are developing JavaScript for the docs sight, run the following instead:
# npm run webpack-watch
```

In another terminal, run the jekyll server:

```sh
# in circleci-docs/jekyll
jekyll serve -Iw
```


Jekyll will build the site and start a web server, which can be viewed in your browser at <http://localhost:4000/docs/>. `-w` tells Jekyll to watch for changes and rebuild, while `-I` enables an incremental rebuild to keep things efficient.

## Additional Notes

- We use a gem called [HTMLProofer](https://github.com/gjtorikian/html-proofer) to test links, images, and HTML. The docs site will need a passing build to be deployed; consider using HTMLProofer locally to test everything before you push changes to GitHub.

## Editing Docs Locally

The docs site includes Bootstrap 3, JS, and CSS, so you'll have access to all of its [reusable components](https://v4-alpha.getbootstrap.com/components/alerts/).

All docs live in folders named after the version of CircleCI. The only two you need to worry about are `jekyll/_cci1` and `jekyll/_cci2`. These correspond to CircleCI Classic and CircleCI 2.0, respectively.

1. Create a branch and switch to it:

    `git checkout -b <branch-name>`
    
Please consider naming your branch based on the type of work you are doing, or group it under your name, for example `tyler/fix-nav` or `feature/new-footer`

2. Add or modify Markdown files in these directories according to our [style guide](CONTRIBUTING#style-guide).

3. When you're happy with your changes, commit them with a message summarizing what you did:

    `git commit -am "commit message"`

4. Push your branch up:

    `git push origin <branch-name>`

## Adding New Articles

New articles can be added to the [jekyll/_cci2](https://github.com/circleci/circleci-docs/tree/master/jekyll/_cci2) directory in this repo.

When you make a new article, you'll need to add [**front matter**](https://jekyllrb.com/docs/frontmatter/). This contains metadata about the article you're writing and is required so everything works on our site.

Front matter for our docs will look something like:

```
---
layout: classic-docs
title: "Your Doc Title"
short-title: "Short Title"
categories: [category-slug]
order: 10
---
```

`layout` and `title` are the only required variables. `layout` describes visual settings shared across our docs. `title` will appear at the top of your article and appear in hypenated form for the URL.

The remaining variables (`categories`, `short-title`, and `order`) are deprecated and no longer used in documentation. Navigation links to each article are manually added to category landing pages. If you're having trouble deciding where to put an article, a CircleCI docs lead can help.

### Headings & Tables of Contents

Jekyll will automatically convert your article's title into a level one heading (#), so we recommend using level two (##), level three (###) and level four (####) headings when structuring your article.

If your article has more than three headings after the title, please use a table of contents. To add a table of contents, use the following reference name:

```
* TOC
{:toc}
```

This will create an unordered list for every heading level in your article (the `* TOC` line will not display).

If you want to exclude a heading from a TOC, you can specify that with another reference name:

```
# Not in the TOC
{:.no_toc}
```

## Submitting Pull Requests

After you are finished with your changes, please follow our [Contributing Guide](./CONTRIBUTING.md) to submit a pull request.

## Docker Tag List for CircleCI Convenience Images

The Docker tag list for convenience images, located in ./jekyll/_cci2/circleci-images.md, is dynamically updated during a CircleCI build.
There's usually no need to touch this.
If you'd like to see an updated list generated locally however, you can do so by running `./scripts/pull-docker-image-tags.sh` from the root of this repo.
Note that you'll need the command-line tool [jq](https://stedolan.github.io/jq/) installed.

## Updating the API Reference

Our API is handled in two possible places currently:
- [Old version](https://circleci.com/docs/api/v1-reference/) - This currently
  accessible via the CircleCI landing page > Developers Dropdown > "Api"
- [New Version using Slate](https://circleci.com/docs/api/#section=reference) -
  A newer API guide, built with [Slate](https://github.com/lord/slate)
  
**What is Slate?**

Slate is a tool for generating API documentation. Slate works by having a user
clone or fork it's Github Repo, having the user fill in the API spec into a
`index.html.md` file, and then generating the static documentation using Ruby
(via `bundler`).

**How do we use Slate?**

We have cloned slate into our docs repo ("vendored" it) so that the whole
project is available under `circleci-docs/src-api`. Because Slate is not a
library, it is required that we vendor it and use its respective build
steps to create our API documentation.

**Making changes to the documentation**

When it comes time to make changes to our API, start with the following:

- All changes to the API happen in `circleci-docs/src-api/source/` folder.
- Our API documentation is broken up into several documents in the `source/includes` folder. For example, all API requests related to `Projects` are found in the `circleci-docs/src-api/source/includes/_projects.md` file.
- Within the `/source` folder, the `index.html.md` has an `includes` key in the front matter. The includes key gathers the separated files in the `includes` folder and merges them into a single file at build time.
- Because Slate builds the entire API guide into a _single html_ file, we can view the artifact file on CircleCI whenever a build is run.

The following is an example workflow to contribute to a document (from Github, no less):

- Navigate to the file you want to edit (example: [`src-api/source/includes/_projects.md`](https://github.com/circleci/circleci-docs/blob/master/src-api/source/includes/_projects.md))
- Click the `edit` button on GitHub and make your changes.
- Commit your changes and submit a PR.
- Go to the CircleCI web app, find the build for the latest commit for your PR
- Go to the `Artifacts` tab and navigate to `circleci-docs/api/index.html` to view the built file.

**Local Development with Slate**

- If you want to see your changes live before committing them, `cd` into
  `src-api` and run `bundle install` followed by `bundle exec middleman server`.
- You may need a specific version of Ruby for bundler to work (2.3.1).
 
