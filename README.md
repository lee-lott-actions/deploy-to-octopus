# Create Octopus Deploy Release GitHub Action

This GitHub Action automates the creation of a new release in Octopus Deploy by pushing a package, uploading build information, and creating a release for a specified project and channel.

## Features
- Pushes a package to Octopus Deploy with overwrite support.
- Generates and uploads build information, including GitHub run details.
- Creates a release in the specified Octopus Deploy project and channel.
- Appends deployment details to the GitHub job summary.
- Uses the `octo.exe` CLI for Octopus Deploy interactions.

## Inputs
| Name | Description | Required |
|------|-------------|----------|
| `octopus-url` | The instance URL hosting Octopus Deploy | Yes |
| `octopus-api-key` | The API key used to access Octopus Deploy | Yes |
| `octopus-space` | The name of the Octopus Deploy space within which this command will be executed | Yes |
| `octopus-project-name` | The name of the Octopus Deploy project associated with this release | Yes |
| `octopus-channel-name` | The name of the Octopus Deploy channel to use for the new release | Yes |
| `octopus-package-name` | The name of the Octopus Deploy package to use for this release | Yes |
| `octopus-package-version` | The version number of the package to use for this release | Yes |
| `octopus-package-path` | The full path to the package to upload to Octopus Deploy | Yes |

## Usage
To use this action, create a workflow file in your repository (e.g., `.github/workflows/create-octopus-release.yml`) and reference the action. Ensure the `octopus-api-key` has the necessary permissions to interact with Octopus Deploy, and the package file is accessible in the workflow environment.

**Note:** This action requires the `octo.exe` CLI and should be run on a **self-hosted runner** configured with a Windows environment to ensure compatibility with the CLI and related dependencies.

### Example Workflow
```yaml
name: Create Octopus Deploy Release
on:
  workflow_dispatch:
    inputs:
      octopus-project-name:
        description: 'Octopus Deploy project name'
        required: true
      octopus-channel-name:
        description: 'Octopus Deploy channel name'
        required: true
      octopus-package-name:
        description: 'Package name for the release'
        required: true
      octopus-package-version:
        description: 'Package version for the release'
        required: true
      octopus-package-path:
        description: 'Path to the package file'
        required: true

jobs:
  create-release:
    runs-on: self-hosted
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v6

      - name: Create Octopus Deploy Release
        uses: lee-lott-actions/deploy-to-octopus@v1
        with:
          octopus-url: ${{ secrets.OCTOPUS_URL }}
          octopus-api-key: ${{ secrets.OCTOPUS_API_KEY }}
          octopus-space: 'Default'
          octopus-project-name: ${{ github.event.inputs.octopus-project-name }}
          octopus-channel-name: ${{ github.event.inputs.octopus-channel-name }}
          octopus-package-name: ${{ github.event.inputs.octopus-package-name }}
          octopus-package-version: ${{ github.event.inputs.octopus-package-version }}
          octopus-package-path: ${{ github.event.inputs.octopus-package-path }}
      - name: View Summary
        run: cat $GITHUB_STEP_SUMMARY
        shell: bash
