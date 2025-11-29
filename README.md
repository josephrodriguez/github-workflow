# GitHub Workflow Templates

A comprehensive collection of reusable GitHub Actions workflows for building, testing, and deploying applications across multiple programming languages and platforms.

## Table of Contents

- [Overview](#overview)
- [Java Workflows](#java-workflows)
- [Go Workflows](#go-workflows)
- [.NET Workflows](#net-workflows)
- [Docker Workflows](#docker-workflows)
- [Getting Started](#getting-started)
- [Common Configuration](#common-configuration)

## Overview

This repository contains a curated set of workflow templates designed to standardize CI/CD pipelines across projects. Each workflow is implemented as a reusable workflow (workflow_call) that can be easily integrated into your project's workflow files.

### Key Features

- **Modular Design**: Each workflow is independent and can be used in isolation
- **Consistent Configuration**: Common patterns across all workflows for easy adoption
- **Integration Support**: Built-in support for Slack notifications, SonarQube analysis, and artifact management
- **Caching**: Optimized dependency caching for faster builds
- **Multi-Platform Support**: Cross-platform compilation and testing capabilities

---

## Java Workflows

### java-gradle-build

Comprehensive Gradle-based build workflow for Java applications with testing, caching, and SonarQube integration.

#### File
`.github/workflows/java-gradle-build.yaml`

#### Features

- JDK setup (configurable version and distribution)
- Gradle and SonarCloud caching
- Unit and native tests execution
- SonarQube code quality analysis
- Gradle build scan integration
- Slack notifications

#### Input Parameters

| Parameter | Type | Default | Required | Description |
|-----------|------|---------|----------|-------------|
| `java_version` | string | `23` | No | JDK version to use |
| `java_distribution` | string | - | **Yes** | JDK distribution (temurin, zulu, corretto, etc.) |
| `cache_gradle` | boolean | `true` | No | Cache Gradle dependencies |
| `cache_sonar` | boolean | `true` | No | Cache SonarCloud packages |
| `gradle_build_args` | string | `` | No | Additional Gradle build arguments |
| `gradle_disable_daemon` | boolean | `true` | No | Disable Gradle daemon |
| `gradle_enable_sonar` | boolean | `false` | No | Enable SonarQube analysis |
| `gradle_enable_scan` | boolean | `false` | No | Enable Gradle build scans |
| `gradle_run_tests` | boolean | `true` | No | Run unit tests |
| `gradle_native_tests` | boolean | `false` | No | Run native tests (GraalVM) |
| `gradle_log_level` | string | `info` | No | Gradle logging level (quiet, warn, lifecycle, info, debug) |
| `slack_enabled` | boolean | `false` | No | Enable Slack notifications |
| `slack_username` | string | `bot` | No | Slack bot username |
| `slack_channel` | string | `slack-notification` | No | Slack channel for notifications |
| `slack_title` | string | `Build` | No | Slack message title |

#### Secret Requirements

| Secret | Required | Description |
|--------|----------|-------------|
| `SONAR_TOKEN` | No | SonarCloud authentication token |
| `MAVEN_REGISTRY_USERNAME` | No | Maven registry username for private dependencies |
| `MAVEN_REGISTRY_PASSWORD` | No | Maven registry password |
| `SLACK_WEBHOOK` | No | Slack webhook URL for notifications |

#### Usage Example

```yaml
name: Java Build

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    uses: josephrodriguez/github-workflow/.github/workflows/java-gradle-build.yaml@main
    with:
      java_version: '21'
      java_distribution: 'temurin'
      gradle_enable_sonar: true
      gradle_run_tests: true
      slack_enabled: true
      slack_channel: '#builds'
    secrets:
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
      MAVEN_REGISTRY_USERNAME: ${{ secrets.MAVEN_REGISTRY_USERNAME }}
      MAVEN_REGISTRY_PASSWORD: ${{ secrets.MAVEN_REGISTRY_PASSWORD }}
```

---

### java-gradle-publish

Publishes Gradle-based Java artifacts to Maven registry.

#### File
`.github/workflows/java-gradle-publish.yaml`

#### Features

- JDK setup and caching
- Maven settings configuration
- Clean build and test execution
- Package publishing to Maven registry
- Gradle build scan support
- Slack notifications

#### Input Parameters

| Parameter | Type | Default | Required | Description |
|-----------|------|---------|----------|-------------|
| `java_version` | string | `21` | No | JDK version to use |
| `java_distribution` | string | - | **Yes** | JDK distribution |
| `gradle_enable_cache` | boolean | `false` | No | Cache Gradle dependencies |
| `gradle_enable_scan` | boolean | `false` | No | Enable Gradle build scans |
| `gradle_log_level` | string | `info` | No | Gradle logging level |
| `slack_enabled` | boolean | `false` | No | Enable Slack notifications |
| `slack_username` | string | `bot` | No | Slack bot username |
| `slack_channel` | string | `slack-notification` | No | Slack channel |
| `slack_title` | string | `Build` | No | Slack message title |

#### Secret Requirements

| Secret | Required | Description |
|--------|----------|-------------|
| `MAVEN_REGISTRY_USERNAME` | **Yes** | Maven registry username |
| `MAVEN_REGISTRY_PASSWORD` | **Yes** | Maven registry password |
| `SLACK_WEBHOOK` | No | Slack webhook URL |

#### Usage Example

```yaml
name: Java Publish

on:
  release:
    types: [ published ]

jobs:
  publish:
    uses: josephrodriguez/github-workflow/.github/workflows/java-gradle-publish.yaml@main
    with:
      java_version: '21'
      java_distribution: 'temurin'
      gradle_enable_cache: true
      slack_enabled: true
    secrets:
      MAVEN_REGISTRY_USERNAME: ${{ secrets.MAVEN_REGISTRY_USERNAME }}
      MAVEN_REGISTRY_PASSWORD: ${{ secrets.MAVEN_REGISTRY_PASSWORD }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Go Workflows

### golang-build

Advanced Go application build workflow with multi-platform support, testing, coverage analysis, and SonarQube integration.

#### File
`.github/workflows/golang/golang-build.yaml`

#### Features

- Go version configuration
- Dependency management (go mod download/verify)
- Code linting with golangci-lint
- Unit tests with race detection
- Code coverage analysis with configurable threshold
- Single or multi-platform binary compilation
- SonarQube code quality analysis
- Build artifact management
- Slack notifications

#### Input Parameters

| Parameter | Type | Default | Required | Description |
|-----------|------|---------|----------|-------------|
| `go_version` | string | `1.21` | No | Go version to use |
| `go_modules_enabled` | boolean | `true` | No | Enable Go modules |
| `go_tests_enabled` | boolean | `true` | No | Run unit tests |
| `go_lint_enabled` | boolean | `true` | No | Run `golangci-lint` during the workflow |
| `go_lint_timeout` | string | `5m` | No | Timeout for `golangci-lint` (e.g., `5m`) |
| `go_coverage_enabled` | boolean | `true` | No | Enable coverage analysis |
| `go_coverage_threshold` | string | `70` | No | Minimum coverage percentage |
| `go_build_ldflags` | string | `` | No | LDFLAGS for build (e.g., `-s -w -X main.Version=1.0.0`) |
| `go_build_gcflags` | string | `` | No | GCFLAGS (compiler flags) for `go build` (e.g., `-N -l`) |
| `go_build_output_dir` | string | `./bin` | No | Binary output directory |
| `go_build_os` | string | `linux` | No | Target OS (linux, windows, darwin) |
| `go_build_arch` | string | `amd64` | No | Target architecture (amd64, arm64, 386) |
| `go_build_output_name` | string | `app` | No | Output binary base name (extension added for Windows) |
| `go_project_dir` | string | `` | No | Path to Go project directory to build. If empty, builds from root. |
| `go_artifacts_enabled` | boolean | `true` | No | Enable uploading build artifacts |
| `go_artifacts_name` | string | `golang-binaries` | No | Name for the uploaded artifacts |
| `sonar_enabled` | boolean | `false` | No | Enable SonarQube analysis |
| `sonar_project_key` | string | `` | No | SonarQube project key |
| `sonar_host_url` | string | `https://sonarcloud.io` | No | SonarQube server URL |
| `slack_enabled` | boolean | `false` | No | Enable Slack notifications |
| `slack_username` | string | `bot` | No | Slack bot username |
| `slack_channel` | string | `slack-notification` | No | Slack channel |
| `slack_title` | string | `Golang Build` | No | Slack message title |
| `github_runner_os_version` | string | `ubuntu-latest` | No | GitHub Actions runner OS |

#### Secret Requirements

| Secret | Required | Description |
|--------|----------|-------------|
| `SONAR_TOKEN` | No | SonarCloud authentication token |
| `SLACK_WEBHOOK` | No | Slack webhook URL |

#### Usage Examples

**Basic Build (Linux/AMD64)**
```yaml
name: Go Build

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    uses: josephrodriguez/github-workflow/.github/workflows/golang/golang-build.yaml@main
    with:
      go_version: '1.21'
      go_tests_enabled: true
      go_coverage_enabled: true
      go_coverage_threshold: '80'
```

**Advanced Build with SonarQube and Slack**
```yaml
jobs:
  build:
    uses: josephrodriguez/github-workflow/.github/workflows/golang/golang-build.yaml@main
    with:
      go_version: '1.21'
      go_build_ldflags: '-s -w -X main.Version=${{ github.ref_name }}'
      go_build_os: 'linux'
      go_build_arch: 'amd64'
      sonar_enabled: true
      sonar_project_key: 'my-org_my-project'
      slack_enabled: true
      slack_channel: '#deployments'
    secrets:
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

---

## .NET Workflows

### dotnet-build

Comprehensive .NET build workflow with NuGet package management, testing, and Slack notifications.

#### File
`.github/workflows/dotnet-build.yaml`

#### Features

- .NET SDK version configuration
- NuGet source configuration (GitHub Packages support)
- Dependency caching
- Solution build and restore
- Unit test execution
- Slack notifications

#### Input Parameters

| Parameter | Type | Default | Required | Description |
|-----------|------|---------|----------|-------------|
| `dotnet_version` | string | `8.0.x` | No | .NET SDK version to use |
| `nuget_source_url` | string | `https://nuget.pkg.github.com/${{ github.repository_owner }}/index.json` | No | Custom NuGet source URL |
| `nuget_source_name` | string | `github` | No | Name for the NuGet source |
| `nuget_store_password_clear_text` | boolean | `true` | No | Store NuGet password in clear text |
| `run_tests` | boolean | `true` | No | Run unit tests |
| `build_no_restore` | boolean | `true` | No | Skip restore during build (use cached packages) |
| `build_configuration` | string | `Release` | No | Build configuration (Debug or Release) |
| `slack_enabled` | boolean | `false` | No | Enable Slack notifications |
| `slack_username` | string | `bot` | No | Slack bot username |
| `slack_channel` | string | `slack-notification` | No | Slack channel |
| `slack_title` | string | `Build` | No | Slack message title |

#### Secret Requirements

| Secret | Required | Description |
|--------|----------|-------------|
| `NUGET_USERNAME` | **Yes** | NuGet registry username |
| `NUGET_PASSWORD` | **Yes** | NuGet registry password or PAT |
| `SLACK_WEBHOOK` | No | Slack webhook URL |

#### Usage Example

```yaml
name: .NET Build

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    uses: josephrodriguez/github-workflow/.github/workflows/dotnet-build.yaml@main
    with:
      dotnet_version: '8.0.x'
      build_configuration: 'Release'
      run_tests: true
      slack_enabled: true
      slack_channel: '#builds'
    secrets:
      NUGET_USERNAME: ${{ secrets.NUGET_USERNAME }}
      NUGET_PASSWORD: ${{ secrets.NUGET_PASSWORD }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Docker Workflows

### docker-build-release

Comprehensive Docker image build and push workflow with multi-platform support, layer caching, and security features.

#### File
`.github/workflows/docker-build-release.yaml`

#### Features

- Multi-platform Docker builds (AMD64, ARM64, etc.)
- Docker layer caching optimization
- Software Bill of Materials (SBOM) generation
- Build provenance for reproducibility
- GitHub Actions and Docker registry caching
- Automated image tagging
- Docker registry authentication
- Slack notifications

#### Input Parameters

| Parameter | Type | Default | Required | Description |
|-----------|------|---------|----------|-------------|
| `github_repository` | string | - | **Yes** | GitHub repository identifier |
| `docker_image_name` | string | - | **Yes** | Docker image name (format: owner/repo) |
| `docker_registry_url` | string | - | **Yes** | Docker registry URL |
| `docker_build_args` | string | `` | No | Docker build arguments (--build-arg format) |
| `docker_context` | string | `.` | No | Build context directory |
| `docker_file_name` | string | `./Dockerfile` | No | Path to Dockerfile |
| `docker_image_tags` | string | `` | No | Image tags (comma-separated) |
| `docker_platform` | string | `linux/amd64` | No | Target platform (e.g., linux/amd64,linux/arm64) |
| `docker_cache_from` | string | `type=gha` | No | Cache source strategy |
| `docker_cache_to` | string | `type=gha,mode=max` | No | Cache destination strategy |
| `docker_provenance` | string | `mode=max` | No | Provenance mode (max for full metadata) |
| `docker_sbom` | string | `true` | No | Generate Software Bill of Materials |
| `docker_push_enabled` | boolean | `false` | No | Push image to registry |
| `os_version` | string | `ubuntu-latest` | No | GitHub Actions runner OS |
| `slack_enabled` | boolean | `false` | No | Enable Slack notifications |
| `slack_username` | string | `bot` | No | Slack bot username |
| `slack_channel` | string | `slack-notification` | No | Slack channel |
| `slack_title` | string | `Build` | No | Slack message title |

#### Secret Requirements

| Secret | Required | Description |
|--------|----------|-------------|
| `DOCKER_REGISTRY_USERNAME` | **Yes** | Docker registry username |
| `DOCKER_REGISTRY_TOKEN` | **Yes** | Docker registry password or token |

#### Usage Examples

**Build and Push to Docker Hub**
```yaml
name: Docker Build and Push

on:
  push:
    tags: [ 'v*' ]

jobs:
  build:
    uses: josephrodriguez/github-workflow/.github/workflows/docker-build-release.yaml@main
    with:
      github_repository: ${{ github.repository }}
      docker_image_name: 'myorg/myapp'
      docker_registry_url: 'docker.io'
      docker_image_tags: 'latest,${{ github.ref_name }}'
      docker_platform: 'linux/amd64,linux/arm64'
      docker_push_enabled: true
      slack_enabled: true
    secrets:
      DOCKER_REGISTRY_USERNAME: ${{ secrets.DOCKER_REGISTRY_USERNAME }}
      DOCKER_REGISTRY_TOKEN: ${{ secrets.DOCKER_REGISTRY_TOKEN }}
```

**Build and Push to GitHub Container Registry**
```yaml
jobs:
  build:
    uses: josephrodriguez/github-workflow/.github/workflows/docker-build-release.yaml@main
    with:
      github_repository: ${{ github.repository }}
      docker_image_name: 'ghcr.io/${{ github.repository_owner }}/myapp'
      docker_registry_url: 'ghcr.io'
      docker_image_tags: 'latest'
      docker_push_enabled: true
    secrets:
      DOCKER_REGISTRY_USERNAME: ${{ github.actor }}
      DOCKER_REGISTRY_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## Getting Started

### Prerequisites

- GitHub repository with GitHub Actions enabled
- Appropriate secrets configured for your chosen workflow

### Setup Instructions

1. **Create a workflow file** in your repository at `.github/workflows/my-workflow.yaml`

2. **Reference the template workflow** using the `uses` keyword:
   ```yaml
   uses: josephrodriguez/github-workflow/.github/workflows/<workflow-name>.yaml@main
   ```

3. **Configure inputs** according to your project's needs

4. **Add required secrets** to your repository settings

5. **Test the workflow** by triggering an event (push, pull request, etc.)

### Repository Secrets Setup

Go to your repository → Settings → Secrets and variables → Actions and add the following:

**For Java Projects:**
- `SONAR_TOKEN` - Get from SonarCloud
- `MAVEN_REGISTRY_USERNAME` - Your Maven registry username
- `MAVEN_REGISTRY_PASSWORD` - Your Maven registry password
- `SLACK_WEBHOOK` - Get from Slack workspace

**For Go Projects:**
- `SONAR_TOKEN` - SonarCloud token
- `SLACK_WEBHOOK` - Slack webhook URL

**For .NET Projects:**
- `NUGET_USERNAME` - NuGet registry username
- `NUGET_PASSWORD` - NuGet registry PAT
- `SLACK_WEBHOOK` - Slack webhook URL

**For Docker Projects:**
- `DOCKER_REGISTRY_USERNAME` - Docker registry username
- `DOCKER_REGISTRY_TOKEN` - Docker registry token
- `SLACK_WEBHOOK` - Slack webhook URL

---

## Common Configuration

### Slack Notifications

All workflows support Slack notifications. Enable by setting:

```yaml
slack_enabled: true
slack_channel: '#builds'  # or 'channel-name'
slack_username: 'BuildBot'
slack_title: 'Build Status'
```

### SonarQube Integration

For workflows supporting SonarQube:

```yaml
sonar_enabled: true
sonar_project_key: 'org_project'
sonar_host_url: 'https://sonarcloud.io'  # or your self-hosted instance
```

Get your token from SonarCloud/SonarQube and add as `SONAR_TOKEN` secret.

### Caching Strategy

All workflows implement intelligent caching:

- **Java**: Gradle and Maven caches
- **Go**: Go build and module caches
- **.NET**: NuGet package cache
- **Docker**: GitHub Actions and Docker layer caches

Caching is automatically enabled by default to speed up builds.

### Multi-Platform Support

**Docker**: Specify multiple platforms:
```yaml
docker_platform: 'linux/amd64,linux/arm64,linux/arm/v7'
```

**Go**: Build for different OS/architecture by running the workflow multiple times with different inputs:
```yaml
strategy:
  matrix:
    include:
      - go_build_os: 'linux'
        go_build_arch: 'amd64'
      - go_build_os: 'darwin'
        go_build_arch: 'amd64'
      - go_build_os: 'windows'
        go_build_arch: 'amd64'
```

---

## Best Practices

1. **Pin workflow versions**: Always use specific versions instead of `@main` in production
2. **Use input validation**: Leverage GitHub Actions input validation for better error messages
3. **Secure secrets**: Never commit secrets; use repository secrets
4. **Cache management**: Enable caching for faster builds
5. **Notification settings**: Configure Slack channels per environment (dev, staging, prod)
6. **Coverage thresholds**: Set realistic coverage targets (typically 70-80%)
7. **Build metadata**: Use LDFLAGS to inject version and build information
8. **Test early**: Run tests as early as possible in workflows to catch issues faster

---

## Troubleshooting

### Docker Build Fails

- Check `docker_context` path is correct
- Verify `docker_file_name` exists
- Ensure base image is accessible
- Check Docker registry credentials

### Coverage Threshold Not Met

- Review coverage reports in artifacts
- Lower `go_coverage_threshold` if necessary
- Check test file coverage
- Run `go test -cover ./...` locally

### NuGet/Maven Authentication Issues

- Verify credentials in repository secrets
- Check if using correct registry URL
- Ensure personal access token (PAT) has correct permissions

### SonarQube Connection Issues

- Verify `sonar_host_url` is accessible
- Check `SONAR_TOKEN` is valid
- Ensure project key exists in SonarQube

---

## License

These workflows are provided as-is for use in your projects.
