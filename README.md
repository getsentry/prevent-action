# Sentry Prevent GitHub Action

<!-- TODO: add these back later -->
<!-- [![GitHub Marketplace](https://img.shields.io/badge/Marketplace-v5-undefined.svg?logo=github&logoColor=white&style=flat)](https://github.com/marketplace/actions/prevent)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fprevent%2Fprevent-action.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fprevent%2Fprevent-action?ref=badge_shield)
[![Workflow for Prevent Action](https://github.com/prevent/prevent-action/actions/workflows/main.yml/badge.svg)](https://github.com/prevent/prevent-action/actions/workflows/main.yml) -->

### Easily upload coverage and test result reports to Sentry Prevent from GitHub Actions

## Usage

To integrate Sentry Prevent with your Actions pipeline, specify the name of this repository with a tag number (`@vl` is recommended) as a `step` within your `workflow.yml` file.

> [!WARNING]
> In order for the Action to work seamlessly, you will need to have `curl`, `git`, and `gpg` installed on your runner. You will also need to run the [actions/checkout](https://github.com/actions/checkout) before calling the Action.

This Action also requires you to [provide an upload token](https://docs.prevent.io/docs/frequently-asked-questions#section-where-is-the-repository-upload-token-found-) from [prevent.io](https://www.prevent.io) (tip: in order to avoid exposing your token, [store it](https://docs.prevent.com/docs/adding-the-prevent-token#github-actions) as a `secret`).

> [!NOTE]
> Currently, the Action will identify linux, macos, and windows runners. However, the Action may misidentify other architectures. The OS can be specified as
> - alpine
> - alpine-arm64
> - linux
> - linux-arm64
> - macos
> - windows

Inside your `.github/workflows/workflow.yml` file:

```yaml
steps:
- uses: actions/checkout@main
- uses: getsentry/prevent-action@v5
  with:
    report_type: coverage # optional (default = coverage)
    fail_ci_if_error: true # optional (default = false)
    files: ./coverage1.xml,./coverage2.xml # optional
    flags: unittests # optional
    name: prevent-coverage-results # optional
    token: ${{ secrets.PREVENT_TOKEN }}
    verbose: true # optional (default = false)
- uses: getsentry/prevent-action@v5
  with:
    report_type: test_results # required to upload test results
    fail_ci_if_error: true # optional (default = false)
    files: ./junit.xml # optional
    flags: unittests # optional
    name: prevent-test-results # optional
    token: ${{ secrets.PREVENT_TOKEN }}
    verbose: true # optional (default = false)
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
For users with [OpenID Connect(OIDC) enabled](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect), the token is not necessary. You can use OIDC with the `use_oidc` argument as following.

```yaml
- uses: getsentry/prevent-action@v5
  with:
    use_oidc: true
```

Any token supplied will be ignored, as Sentry Prevent will default to the OIDC token for verification.

## Arguments

Prevent's Action supports inputs from the user. These inputs, along with their descriptions and usage contexts, are listed in the table below:

| Input  | Description |
| :---       |     :---     |
| `report_type` | [Required] The type of file to upload, coverage by default. Possible values are "test_results", "coverage". |
| `base_sha` | 'The base SHA to select. This is only used in the "pr-base-picking" run command' |
| `binary` | The file location of a pre-downloaded version of the CLI. If specified, integrity checking will be bypassed. |
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
| `flags` | Comma-separated list of flags to upload to group report metrics. |
| `force` | Only used for empty-upload run command |
| `git_service` | Override the git_service (e.g. github_enterprise) |
| `gcov_args` | Extra arguments to pass to gcov |
| `gcov_executable` | gcov executable to run. Defaults to 'gcov' |
| `gcov_ignore` | Paths to ignore during gcov gathering |
| `gcov_include` | Paths to include during gcov gathering |
| `handle_no_reports_found` | If no reports are found, do not raise an exception. |
| `name` | Custom defined name of the upload. Visible in the Sentry Prevent UI |
| `network_filter` | Specify a filter on the files listed in the network section of the Sentry Prevent report. This will only add files whose path begin with the specified filter. Useful for upload-specific path fixing. |
| `network_prefix` | Specify a prefix on files listed in the network section of the Sentry Prevent report. Useful to help resolve path fixing. |
| `os` | Override the assumed OS. Options are: `linux`, `macos`, `windows`, `alpine`, `alpine-arm64`, `linux-arm64` |
| `override_branch` | Specify the branch to be displayed with this commit on Sentry Prevent |
| `override_build` | Specify the build number manually |
| `override_build_url` | The URL of the build where this is running |
| `override_commit` | Commit SHA (with 40 chars) |
| `override_pr` | Specify the pull request number manually. Used to override pre-existing CI environment variables. |
| `plugins` | Comma-separated list of plugins to run. Specify `noop` to turn off all plugins |
| `recurse_submodules` | Whether to enumerate files inside of submodules for path-fixing purposes. Off by default. |
| `root_dir` | Root folder from which to consider paths on the network section. Defaults to current working directory. |
| `run_command` | Choose which CLI command to run. Options are "upload-coverage", "empty-upload", "pr-base-picking", "send-notifications". "upload-coverage" is run by default.' |
| `skip_validation` | Skip integrity checking of the CLI. This is NOT recommended. |
| `slug` | [Required when using the org token] Set to the owner/repo slug used instead of the private repo token. Only applicable to some Enterprise users. |
| `swift_project` | Specify the swift project name. Useful for optimization. |
| `token` | Repository token. Used to authorize report uploads |
| `url` | Set to the Sentry Prevent instance URl. Used by Dedicated Enterprise Cloud customers. |
| `use_legacy_upload_endpoint` | Use the legacy upload endpoint. |
| `use_oidc` | Use OIDC instead of token. This will ignore any token supplied |
| `use_pypi` | Use the pypi version of the CLI instead of from sentry.io/get-prevent-cli. If specified, integrity checking will be bypassed. |
| `verbose` | Enable verbose logging |
| `version` | Which version of the Prevent CLI to use (defaults to 'latest') |
| `working-directory` | Directory in which to execute prevent.sh |

### Example `workflow.yml` with Prevent Action

```yaml
name: Example workflow for Sentry Prevent
on: [push]
jobs:
  run:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
    env:
      OS: ${{ matrix.os }}
      PYTHON: '3.10'
    steps:
    - uses: actions/checkout@main
    - name: Setup Python
      uses: actions/setup-python@main
      with:
        python-version: '3.10'
    - name: Generate coverage report
      run: |
        pip install pytest
        pip install pytest-cov
        pytest --cov=./ --cov-report=xml --junitxml=junit.xml
    - name: Upload coverage to Sentry Prevent
      uses: getsentry/prevent-action@v5
      with:
        directory: ./coverage/reports/
        env_vars: OS,PYTHON
        fail_ci_if_error: true
        files: ./coverage1.xml,./coverage2.xml,!./cache
        flags: unittests
        name: prevent-coverage-results
        token: ${{ secrets.PREVENT_TOKEN }}
        verbose: true
    - name: Upload test results to Sentry Prevent
      uses: getsentry/prevent-action@v5
      with:
        report_type: test_results # required for test results
        directory: ./
        env_vars: OS,PYTHON
        fail_ci_if_error: true
        files: ./junit.xml
        flags: unittests
        name: prevent-test-results
        token: ${{ secrets.PREVENT_TOKEN }}
        verbose: true
```
## Contributing

Contributions are welcome! Check out the [Contribution Guide](CONTRIBUTING.md).

## License

The code in this project is released under the [MIT License](LICENSE).

[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fgetsentry%2Fprevent-action.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fgetsentry%2Fprevent-action?ref=badge_large)
