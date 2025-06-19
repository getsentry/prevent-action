# Contribution Guide

:tada: Thanks for taking the time to contribute! :tada:

The following is a set of guidelines for contributing to this repository, which is hosted in the [Sentry Organization](https://github.com/getsentry) on GitHub.

## What does this repo do?

This repo is a GitHub Action, meaning it integrates with the GitHub Actions CI/CD pipeline. It's meant to take formatted reports with code coverage stats and upload them to Sentry Prevent.

We're submoduling in Prevent's wrapper scripts and copying the `prevent.sh` script that:

- downloads the Prevent CLI
- passes through arguments from the GitHub Action to the CLI
- invokes the CLI's upload command (or user-specified run command)

## PRs, Issues, and Support

Feel free to clone, modify code and request a PR to this repository. All PRs and issues will be reviewed by the Sentry team.
