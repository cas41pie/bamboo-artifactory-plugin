# GitHub Actions CI/CD Setup

This directory contains GitHub Actions workflows for automated building, testing, and releasing of the Bamboo Artifactory Plugin.

## Workflows

### 1. CI/CD Pipeline (`ci-cd.yml`)
**Triggers:** Push to master, Pull requests to master
- **Test Job:** Runs unit tests and uploads results
- **Build Job:** Compiles and packages the plugin artifacts (.jar, .obr files)
- **Publish Job:** Automatically creates GitHub releases for master branch builds
- **Security Scan Job:** Runs dependency vulnerability checks

### 2. Manual Release (`manual-release.yml`)
**Triggers:** Manual workflow dispatch
- Allows manual version bumping (patch, minor, major, or custom)
- Updates pom.xml version
- Creates tagged releases
- Pushes release artifacts to GitHub Releases
- Automatically sets next SNAPSHOT version for development

### 3. Pull Request Validation (`pr-validation.yml`)
**Triggers:** Pull request events (opened, synchronized, reopened)
- Validates Maven configuration
- Runs compilation and tests
- Provides build status comments on PRs
- Runs code quality checks (SpotBugs)

### 4. Dependency and Security Monitoring (`dependency-security.yml`)
**Triggers:** Weekly schedule, Manual dispatch, pom.xml changes
- OWASP dependency vulnerability scanning
- Dependency update checking
- License compliance verification
- Automatic issue creation for dependency updates

### 5. Existing Workflows
- **Monitor Issues (`monitorIssues.yml`):** Automatically marks stale issues
- **CLA Assistant (`cla.yml`):** Manages contributor license agreements

## Artifacts Produced

The CI/CD pipeline produces the following artifacts:
- `bamboo-artifactory-plugin-{version}.jar` - Main plugin JAR file
- `bamboo-artifactory-plugin-{version}.obr` - OSGi Bundle Repository file
- `bamboo-artifactory-plugin-{version}-sources.jar` - Source code JAR

## Installation

To install a released plugin:
1. Go to the [Releases page](../../releases)
2. Download the latest `.jar` file
3. In your Bamboo instance, go to Administration → Manage apps
4. Click "Upload app" and select the downloaded JAR file
5. Restart Bamboo if required

## Manual Release Process

To create a manual release:
1. Go to the Actions tab in GitHub
2. Select "Manual Release" workflow
3. Click "Run workflow"
4. Choose version bump type (patch/minor/major/custom)
5. Optionally specify a custom version
6. Choose whether to create a GitHub Release
7. Specify if it should be marked as pre-release

## Security

- All workflows use pinned action versions for security
- Dependency vulnerability scanning runs automatically
- SARIF results are uploaded to GitHub Security tab
- No secrets are required for basic functionality

## Permissions

The workflows require the following GitHub permissions:
- `contents: write` - For creating releases and pushing tags
- `security-events: write` - For uploading security scan results
- `issues: write` - For creating dependency update issues

## Branch Protection

It's recommended to set up branch protection rules for the `master` branch:
- Require pull request reviews
- Require status checks to pass (CI/CD pipeline)
- Require branches to be up to date before merging
- Restrict pushes to the branch

## Configuration

Most workflows are ready to use out of the box. However, you may want to customize:
- Security scan thresholds in `dependency-security.yml`
- Version bump logic in `manual-release.yml`
- Build artifact retention periods
- Release note templates

## Troubleshooting

Common issues and solutions:
- **Build failures:** Check Java version compatibility (requires JDK 17)
- **Permission errors:** Ensure repository has necessary permissions enabled
- **Artifact upload failures:** Check file paths and artifact names
- **Security scan failures:** Review dependency vulnerabilities and update as needed