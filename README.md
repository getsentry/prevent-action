# Sentry Prevent GitHub Action

<!-- TODO: add these back later -->
<!-- [![GitHub Marketplace](https://img.shields.io/badge/Marketplace-v5-undefined.svg?logo=github&logoColor=white&style=flat)](https://github.com/marketplace/actions/prevent)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fprevent%2Fprevent-action.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fprevent%2Fprevent-action?ref=badge_shield)
[![Workflow for Prevent Action](https://github.com/prevent/prevent-action/actions/workflows/main.yml/badge.svg)](https://github.com/prevent/prevent-action/actions/workflows/main.yml) -->

### Easily upload coverage and test result reports to Sentry Prevent from GitHub Actions

## Usage

To integrate Sentry Prevent with your Actions pipeline, specify the name of this repository with a tag number (`@v0` is recommended) as a `step` within your `workflow.yml` file.

> [!WARNING]
> In order for the Action to work seamlessly, you will need to have `curl` and `git` installed on your runner. You will also need to run the [actions/checkout](https://github.com/actions/checkout) before calling the Action.
> It is suggested to write checkout with `fetch-depth: 0` or a number greater than `1`.

This Action also requires you to [provide an upload token](https://docs.prevent.io/docs/frequently-asked-questions#section-where-is-the-repository-upload-token-found-) from [prevent.io](https://www.prevent.io) (tip: in order to avoid exposing your token, [store it](https://docs.prevent.com/docs/adding-the-prevent-token#github-actions) as a `secret`).

> [!NOTE]
> Currently, the Action will identify linux, macos, and windows runners. However, the Action may misidentify other architectures. The OS can be specified as
> - alpine_arm64
> - alpine_x86_64
> - linux_arm64
> - linux_x86_64
> - macos
> - windows

Inside your `.github/workflows/workflow.yml` file:

```yaml
permissions:
  id-token: write  # by default, OIDC is used to verify the workflow by the Prevent Action
steps:
- uses: actions/checkout@v5
  with:
    fetch-depth: 0
- uses: getsentry/prevent-action@v0
  with:
    token: ${{ secrets.PREVENT_TOKEN }}
```

The token can also be passed in via environment variables:

```yaml
steps:
- uses: actions/checkout@main
- uses: getsentry/prevent-action@v5
  with:
    fail_ci_if_error: true # optional (default = false)
    files: ./coverage1.xml,./coverage2.xml # optional
    flags: unittests # optional
    name: prevent-umbrella # optional
    verbose: true # optional (default = false)
  env:
    PREVENT_TOKEN: ${{ secrets.PREVENT_TOKEN }}
```
> [!NOTE]
> This assumes that you've set your token inside *Settings > Secrets* as `PREVENT_TOKEN`. If not, you can [get an upload token](https://docs.prevent.io/docs/frequently-asked-questions#section-where-is-the-repository-upload-token-found-) for your specific repo on [prevent.io](https://www.prevent.io). Keep in mind that secrets are *not* available to forks of repositories.

### Dependabot
- For repositories using `Dependabot`, users will need to ensure that it has access to the token for PRs from Dependabot to upload coverage. To do this, please add your `PREVENT_TOKEN` as a Dependabot Secret. For more information, see ["Configuring access to private registries for Dependabot."](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/configuring-access-to-private-registries-for-dependabot#storing-credentials-for-dependabot-to-use)

### Using OIDC
By default, [OpenID Connect(OIDC)](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect) verification is on. You will need to add

```yaml
permissions:
  id-token: write
```
to the top of your workflow. To turn off OIDC verification, you will need to add the `use-oidc` argument with the Action and set it to `false`.

Any token supplied will be ignored, as Sentry Prevent will default to the OIDC token for verification.

## Arguments

Prevent's Action supports inputs from the user. These inputs, along with their descriptions and usage contexts, are listed in the table below:

| Input  | Description |
| :---       |     :---     |
| `token` | [Required] Repository token. Used to authorize report uploads. Able to be passed in as an environment variable instead. |
| `commit_parent` | SHA (with 40 chars) of what should be the parent of this commit. |
| `directory` | Folder to search for report files. Default to the current working directory |
| `disable_file_fixes` | Disable file fixes to ignore common lines from coverage (e.g. blank lines or empty brackets). Read more here https://docs.prevent.com/docs/fixing-reports |
| `disable_search` | Disable search for coverage files. This is helpful when specifying what files you want to upload with the files option. |
| `disable_safe_directory` | Disable setting safe directory. Set to true to disable. |
| `disable_telem` | Disable sending telemetry data to Prevent. Set to true to disable. |
| `dry_run` | Don't upload files to Prevent |
| `env_vars` | Environment variables to tag the upload with (e.g. PYTHON \| OS,PYTHON) |
| `exclude` | Comma-separated list of folders to exclude from search. |
| `fail_ci_if_error` | On error, exit with non-zero code |
| `files` | Comma-separated explicit list of files to upload. These will be added to the report files found for upload. If you wish to only upload the specified files, please consider using "disable-search" to disable uploading other files. |
| `git_service` | Override the git_service (e.g. github_enterprise) |
| `installer_source` | Select which installer to use. Choices are `binary` and `pypi`, defaults to `pypi` |
| `name` | Custom defined name of the upload. Visible in the Sentry Prevent UI |
| `network_filter` | Specify a filter on the files listed in the network section of the Sentry Prevent report. This will only add files whose path begin with the specified filter. Useful for upload-specific path fixing. |
| `network_prefix` | Specify a prefix on files listed in the network section of the Sentry Prevent report. Useful to help resolve path fixing. |
| `os` | Override the assumed OS. Options are: `linux`, `macos`, `windows`, `alpine`, `alpine-arm64`, `linux-arm64` |
| `override_branch` | Specify the branch to be displayed with this commit on Sentry Prevent |
| `override_build` | Specify the build number manually |
| `override_build_url` | The URL of the build where this is running |
| `override_commit` | Commit SHA (with 40 chars) |
| `override_pr` | Specify the pull request number manually. Used to override pre-existing CI environment variables. |
| `recurse_submodules` | Whether to enumerate files inside of submodules for path-fixing purposes. Off by default. |
| `root_dir` | Root folder from which to consider paths on the network section. Defaults to current working directory. |
| `use_oidc` | Use OIDC instead of token. This will ignore any token supplied |
| `verbose` | Enable verbose logging |
| `version` | Which version of the Prevent CLI to use (defaults to 'latest') |
| `working-directory` | Directory in which to execute prevent.sh |

## Contributing

Contributions are welcome! Check out the [Contribution Guide](CONTRIBUTING.md).

## License

The code in this project is released under the [MIT License](LICENSE).

[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fgetsentry%2Fprevent-action.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fgetsentry%2Fprevent-action?ref=badge_large)
