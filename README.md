# Shared GitHub Actions workflows and actions

This repository contains a collection of GitHub Actions workflows and actions
we can use in repositories managed by Ferrous Systems. Ferrous Systems
employees should prefer to use actions defined in this repository to reduce the
risk of supply-chain attacks.

🚨 **The contents of this repository are strictly meant for internal use** 🚨.
While this repository is public and released under an open source license,
breaking changes will regularly happen, we will provide no support and
third-party pull requests will not be accepted.

If you intend to use the contents of this repository outside of Ferrous Systems
we recommend forking.

The contents of this repository are licensed under either the MIT license or
the Apache-2.0 license, at your discretion.

## Cache Rust

An action is present in this repository to properly cache Rust build artifacts
for any project using Rust. The cache is keyed by the compiler version, to
avoid ever-growing caches when the compiler is bumped and the old artifacts are
not removed. In practice, the action caches:

* The Cargo registry cache, containing the `.crate` tarballs of all the crates
  used by the project.
* The Cargo index cache, containing the clone of the registry index repository.
* The contents of the target directory, by default `target/`.

You can use this action by adding this code to your workflow, before any
compilation step happens:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/cache-rust@main
```

If you're using a different target directory than `target/` you can specify it:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/cache-rust@main
    with:
      target-dir: path/to/target/
```

If you're using the action in multiple jobs in the same repository, you should
specify an unique cache key for each job:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/cache-rust@main
    with:
      cache-key: job1
```

## Upload to GitHub Pages

An action is present in this repository to upload the contents of a directory
to GitHub Pages. The action requires the path that should be uploaded. It will
disable Jekyll pre-processing, it will create a commit with the contents you
want to upload and it will push it to the `gh-pages` branch:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/github-pages@main
    with:
      path: build/site/
      token: ${{ secrets.GITHUB_TOKEN }}
```

A domain name can be optionally specified:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/github-pages@main
    with:
      path: build/site/
      cname: subdomain.example.com
      token: ${{ secrets.GITHUB_TOKEN }}
```

## Authenticate with AWS

An action is present in this repository to authenticate with an AWS account and
assume an AWS IAM Role through OpenID Connect authentication. The role must
have a trust relationship configured to allow GitHub Actions to assume it.
Documentation on how to configure the trust relationship is available [on the
GitHub documentation][github-aws-docs].

You can use this action by adding this code to your workflow, before any
step that requires AWS authentication:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/aws-oidc@main
    with:
      role: arn:aws:iam::000000000000:role/my-role-name
```

You can also choose a different default region than `us-east-1`:

```yaml
steps:
  - uses: ferrous-systems/shared-github-actions/aws-oidc@main
    with:
      role: arn:aws:iam::000000000000:role/my-role-name
      region: eu-central-1
```

[github-aws-docs]: https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services

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

If your book also uses a Rust preprocessor that requires compilation, you can
cache the Rust compilation artifacts to greatly speed up the build process:

```yaml
jobs:
  mdbook:
    uses: ferrous-systems/shared-github-actions/.github/workflows/mdbook-to-github-pages.yml@main
    with:
      cname: subdomain.example.com
      cache-target-directory: true
```
