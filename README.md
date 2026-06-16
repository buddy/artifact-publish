# Buddy Publish Artifact GitHub Action

Publish artifacts to Buddy CI/CD platform from GitHub Actions workflows.

## Features

- Publish artifacts directly from GitHub Actions
- Support for versioned artifacts (artifact@version)
- Create artifacts automatically if they don't exist
- Force overwrite existing versions

## Usage

### Basic Usage

```yaml
name: Publish
on: [push]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Login to Buddy
        uses: buddy/login@v1
        with:
          token: ${{ secrets.BUDDY_TOKEN }}
          region: 'US'

      - name: Publish artifact
        uses: buddy/artifact-publish@v1
        with:
          workspace: my-workspace
          project: my-project
          identifier: my-artifact
          directory: ./dist
```

### With Version

```yaml
- name: Publish versioned artifact
  uses: buddy/artifact-publish@v1
  with:
    workspace: my-workspace
    project: my-project
    identifier: my-artifact@1.0.0
    directory: ./dist
```

### Create Artifact If Not Exists

```yaml
- name: Publish artifact (create if missing)
  uses: buddy/artifact-publish@v1
  with:
    workspace: my-workspace
    project: my-project
    identifier: my-artifact@1.0.0
    directory: ./dist
    create: true
```

### Force Overwrite Existing Version

```yaml
- name: Publish artifact (force overwrite)
  uses: buddy/artifact-publish@v1
  with:
    workspace: my-workspace
    project: my-project
    identifier: my-artifact@1.0.0
    directory: ./dist
    force: true
```

## Inputs

| Input        | Required | Description                                                           |
| ------------ | -------- | --------------------------------------------------------------------- |
| `workspace`  | Yes      | Buddy workspace domain                                                |
| `project`    | Yes      | Buddy project name (URL handle)                                       |
| `identifier` | Yes      | Artifact identifier with optional version (e.g., `my-artifact@1.0.0`) |
| `directory`  | Yes      | Path to the directory or file to publish                              |
| `create`     | No       | Create artifact if it does not exist (`true`/`false`)                 |
| `force`      | No       | Allow overwriting existing version (`true`/`false`)                   |
| `debug`      | No       | Run the BDY CLI in DEBUG mode and dump `~/.bdy/cli.log` on failure    |

## Outputs

| Output         | Description                       |
| -------------- | --------------------------------- |
| `artifact_url` | The URL of the published artifact |

## Environment Variables

The action exports the following environment variables for use in subsequent steps:

| Variable             | Description                       |
| -------------------- | --------------------------------- |
| `BUDDY_ARTIFACT_URL` | The URL of the published artifact |

## Prerequisites

This action requires authentication with Buddy. Use the [`buddy/login`](https://github.com/buddy/login) action before publishing artifacts:

```yaml
- name: Login to Buddy
  uses: buddy/login@v1
  with:
    token: ${{ secrets.BUDDY_TOKEN }}
    region: 'US'
```

The login action sets the following environment variables that are used by this action:

- `BUDDY_TOKEN` - Authentication token
- `BUDDY_API_ENDPOINT` - API endpoint URL

### BDY CLI Installation

By default, this action automatically installs the latest BDY CLI from the production channel. If you need a specific version or channel, use the `buddy/setup` action first:

```yaml
- name: Setup BDY CLI
  uses: buddy/setup@v1
  with:
    version: '1.12.8'
```

See the [`buddy/setup`](https://github.com/buddy/setup) action for more options.

## Debugging

If a BDY CLI command fails, the action throws an error that includes the exit code, the command that was run, and its captured output.

Enabling debug runs the BDY CLI in DEBUG mode, so it writes verbose logs to `~/.bdy/cli.log`, and on failure dumps that log (plus a stack trace) into the workflow output. Enable it in either of these ways:

- **Per step** — set the `debug` input on this action:

  ```yaml
  - uses: buddy/artifact-publish@v1
    with:
      # ...
      debug: true
  ```

- **Whole run** — enable [GitHub step debug logging](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/enabling-debug-logging), either by ticking **Enable debug logging** in the "Re-run jobs" dialog or by setting the `ACTIONS_STEP_DEBUG` repository secret/variable to `true`.

## License

MIT - See [LICENSE.md](LICENSE.md) for details.
