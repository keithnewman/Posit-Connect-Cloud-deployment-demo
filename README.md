# Posit Connect Cloud Deployment Demo

[![Deploy to Posit Connect Cloud](https://github.com/keithnewman/Posit-Connect-Cloud-deployment-demo/actions/workflows/deploy.yml/badge.svg)](https://github.com/keithnewman/Posit-Connect-Cloud-deployment-demo/actions/workflows/deploy.yml)

This repository contains a standard [Golem](https://thinkr-open.github.io/golem/) app template for a Shiny application. The main focus of this repository is to test and demonstrate that it is possible to deploy a Shiny application to [Posit Connect Cloud](https://connect.posit.cloud/) from a GitHub Action.

## GitHub Actions Workflow

The deployment workflow ([`.github/workflows/deploy.yml`](.github/workflows/deploy.yml)) runs on every push to `main` and can also be triggered manually via `workflow_dispatch`. Key features:

- **R setup**: Installs R 4.5.2 using `r-lib/actions/setup-r`.
- **pak for package installation**: [pak](https://pak.r-lib.org/) is installed first, then an `renv` option (`renv.config.pak.enabled = TRUE`) is injected into `.Rprofile` so that `renv::restore()` uses pak to install R packages and their system dependencies automatically.
- **renv restoration**: Uses `r-lib/actions/setup-renv` to restore the lockfile, benefiting from pak's faster parallel downloads and automatic system dependency resolution.
- **rsconnect fallback**: If `rsconnect` is not listed in the `renv.lock`, it is installed on-the-fly via pak.
- **Authentication**: Uses `rsconnect::connectCloudClientCredentials()` to authenticate with Posit Connect Cloud using a client ID/secret pair.
- **Deployment**: Deploys the app with `rsconnect::deployApp()`, using configurable app name and title.
- **Cleanup**: Removes stored account credentials after deployment.

## Required Variables and Secrets

Configure these in your GitHub repository under **Settings > Secrets and variables > Actions**:

| Name | Type | Description |
|------|------|-------------|
| `RSCONNECT_CLIENT_ID` | Secret | OAuth client ID for Posit Connect Cloud authentication |
| `RSCONNECT_CLIENT_SECRET` | Secret | OAuth client secret for Posit Connect Cloud authentication |
| `RSCONNECT_USERNAME` | Variable | Your Posit Connect Cloud account name |
| `APP_NAME_MAIN` | Variable | The programmatic name for the deployed app |
| `APP_TITLE_MAIN` | Variable | The display title for the deployed app |
