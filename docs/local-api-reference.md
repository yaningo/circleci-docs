## Updating the API Reference

The documentation for our  API is handled in two possible places currently:

**[API v1](https://circleci.com/docs/api/v1-reference/)**

The original docs are currently accessible via the CircleCI landing page > Developers Dropdown > "Api"
These docs were built by hand; written in markdown, and processed by Slate.

**[API v2](https://circleci.com/docs/api/v2)**

  A newer API guide, built with [Slate](https://github.com/lord/slate) and
  openAPI spec. These documents are **automatically build** as part of our CI
  process. This happens in the `/src-api` folder using Slate. Every time we run
  our CI config, we build the most recent version of the API v2 Docs.
  
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
