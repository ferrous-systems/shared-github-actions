# Shared GitHub Actions workflows and actions

This repository contains a collection of GitHub Actions workflows and actions
we can use as part of our repositories. **This repository is internal, and
every member of our GitHub Enterprise will have access to it, not only Ferrous
Systems employees.**

## mdBook to GitHub Pages

A reusable workflow is present in this repository to automate publishing a
mdbook-powered website to GitHub Pages. You can use it in your private
repository by adding this job to a GitHub Actions workflow:

```yaml
jobs:
  mdbook:
    uses: ferrous-systems/shared-github-actions/.github/workflows/mdbook-to-github-pages.yml@main
    with:
      cname: subdomain.example.com
```

This will publish the book present in the root of the repository on the domain
name you specified. A pinned mdBook version is used (see the workflow's [source
code] to check which version is currently pinned).

[source code]: https://github.com/ferrous-systems/shared-github-actions/blob/main/.github/workflows/mdbook-to-github-pages.yml

You can optionally configure the mdBook version and the path containing the
book:

```yaml
jobs:
  mdbook:
    uses: ferrous-systems/shared-github-actions/.github/workflows/mdbook-to-github-pages.yml@main
    with:
      cname: subdomain.example.com
      version: 0.4.14
      path: docs/
```
