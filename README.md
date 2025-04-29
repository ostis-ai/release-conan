# Release Conan GitHub Action

This GitHub Action is designed to build, package, and release a C++ component of the ostis-system using Conan. It automates the setup, dependency installation, build process, Conan package export, upload to a Conan remote, and creation/upload of a release archive. The action runs builds on multiple operating systems (Ubuntu and macOS) defined within its matrix.

## Inputs

### `name` (required)

The name of the component being released (used for Conan package naming).

### `version` (required)

The version string for the component being released (used for Conan package versioning).

### `directory` (required)

The directory relative to the repository root containing the component's source code and `conanfile.py`.

### `configure-preset` (required)

The CMake preset name used to configure the component build.

### `build-preset` (required)

The CMake preset name used to build the component.

### `conan-username` (required)

The Conan username required for authenticating with the target Conan remote repository (`ostis-ai`).

### `conan-api-key` (required)

The Conan API key (or password) corresponding to the `conan-username` for authentication with the `ostis-ai` remote.

## Usage

To use this action in your workflow, include the following example configuration. Ensure you have the necessary secrets (`CONAN_USERNAME`, `CONAN_API_KEY`) configured in your repository or organization settings.

```yaml
name: Release Conan

on: [push, pull_request]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run Release Conan Action
        uses: ostis-ai/release-conan@0.1.0
        with:
          name: my-component-name
          version: 0.1.0
          directory: path/to/your/component
          configure-preset: release-conan
          build-preset: release
          conan-username: ${{ secrets.CONAN_USERNAME }}
          conan-api-key: ${{ secrets.CONAN_API_KEY }}

# The composite action uploads the archive artifacts automatically.
# They will appear under the 'release' job artifacts
```

## Requirements

-   Runs on GitHub-hosted runners: `ubuntu-22.04` and `macos-14`.
-   The component directory (`inputs.directory`) must contain a valid `conanfile.py`.
-   CMake presets (specified by `inputs.configure-preset` and `inputs.build-preset`) must be defined for the component.
-   The following secrets must be configured in the repository or organization for uploading to the Conan remote:
    -   `CONAN_USERNAME`
    -   `CONAN_API_KEY`

## Outputs

This action produces the following outputs:

1.  Uploads the built component package (`<name>/<version>`) to the configured Conan remote (`ostis-ai`).
2.  Uploads `.tar.gz` archives (created using CPack) for each operating system build as GitHub Actions artifacts. The artifact names will follow the pattern: `<name>-<os>-<version>`.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
