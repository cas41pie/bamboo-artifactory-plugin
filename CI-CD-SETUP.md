# CI/CD Implementation for Bamboo Artifactory Plugin

This document explains the complete CI/CD setup implemented for automatically building and publishing the Bamboo Artifactory Plugin from the master branch to GitHub releases.

## Overview

The CI/CD implementation consists of several GitHub Actions workflows that provide:
- Automated building and testing on every commit to master
- Automatic artifact publishing to GitHub Releases
- Pull request validation
- Security and dependency monitoring
- Manual release capabilities with proper version management

## Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Push/PR to    │───▶│   CI/CD Pipeline  │───▶│ GitHub Releases │
│     Master      │    │                  │    │   (Artifacts)   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌──────────────────┐
                       │  Security Scan   │
                       │  Dependency Check│
                       └──────────────────┘
```

## Workflows Implemented

### 1. Main CI/CD Pipeline (`.github/workflows/ci-cd.yml`)

**Triggers:**
- Push to master branch
- Pull requests to master branch
- Manual dispatch

**Jobs:**
- **Test:** Runs unit tests using Maven
- **Build:** Compiles and packages the plugin
- **Publish:** Creates GitHub releases with artifacts (master only)
- **Security Scan:** OWASP dependency vulnerability analysis

**Artifacts Created:**
- `bamboo-artifactory-plugin-{version}.jar` - Main plugin
- `bamboo-artifactory-plugin-{version}.obr` - OSGi Bundle Repository
- `bamboo-artifactory-plugin-{version}-sources.jar` - Source code

### 2. Manual Release Workflow (`.github/workflows/manual-release.yml`)

**Purpose:** Allows controlled releases with proper version management

**Features:**
- Version bumping (patch/minor/major/custom)
- Automatic pom.xml updates
- Git tagging
- GitHub Release creation
- Post-release SNAPSHOT version setup

**Usage:**
1. Go to Actions → Manual Release
2. Choose version bump type
3. Optionally mark as pre-release
4. Workflow handles the rest automatically

### 3. Pull Request Validation (`.github/workflows/pr-validation.yml`)

**Purpose:** Ensures code quality before merging

**Checks:**
- Maven compilation
- Unit test execution
- Code quality analysis (SpotBugs)
- Build status comments on PRs

### 4. Dependency & Security Monitoring (`.github/workflows/dependency-security.yml`)

**Purpose:** Proactive security and maintenance

**Features:**
- Weekly OWASP vulnerability scans
- Dependency update notifications
- License compliance checking
- Automatic issue creation for updates

**Schedule:** Runs every Sunday at midnight UTC

### 5. Dependabot Configuration (`.github/dependabot.yml`)

**Purpose:** Automatic dependency updates

**Features:**
- Weekly Maven dependency updates
- GitHub Actions updates
- Grouped related dependencies
- Proper labeling and commit messages

## Release Process

### Automatic Releases (Continuous Deployment)

1. Developer pushes code to master
2. CI/CD pipeline runs automatically
3. Tests execute and must pass
4. Artifacts are built and packaged
5. GitHub Release is created with build number
6. Artifacts are attached to the release

### Manual Releases (Controlled Deployment)

1. Maintainer triggers Manual Release workflow
2. Selects version bump type (patch/minor/major)
3. Workflow updates version in pom.xml
4. Creates Git tag and GitHub Release
5. Artifacts are published
6. Version is bumped to next SNAPSHOT for development

## Security Features

### Vulnerability Scanning
- OWASP Dependency Check integration
- SARIF results uploaded to GitHub Security tab
- Configurable CVSS threshold (currently 7.0)

### Secure Practices
- Pinned GitHub Action versions
- Minimal required permissions
- No custom secrets required for basic functionality
- Dependabot for keeping actions updated

## Installation Instructions

### For End Users

1. Navigate to the [Releases page](../../releases)
2. Download the latest `bamboo-artifactory-plugin-{version}.jar`
3. In Bamboo Administration:
   - Go to Manage apps
   - Click "Upload app"
   - Select the downloaded JAR
   - Restart Bamboo if prompted

### For Repository Setup

The workflows are ready to use immediately after merging. No additional configuration required.

**Optional Enhancements:**
1. Enable branch protection on master:
   - Require PR reviews
   - Require status checks
   - Require up-to-date branches

2. Configure repository secrets (if needed):
   - None required for basic functionality
   - `CLA_SIGN_TOKEN` already configured for existing CLA workflow

## Monitoring and Maintenance

### GitHub Actions Usage
- Monitor workflow runs in the Actions tab
- Check for failed builds and address issues
- Review security scan results

### Dependency Management
- Dependabot creates PRs for updates weekly
- Dependency monitoring workflow creates issues for manual review
- Security vulnerabilities are reported in GitHub Security tab

### Release Management
- Automatic releases for every master commit
- Manual releases for controlled version management
- Proper semantic versioning support

## Troubleshooting

### Common Issues

**Build Failures:**
- Check Java version (requires JDK 17)
- Verify Maven dependencies are available
- Review test failures in uploaded artifacts

**Permission Errors:**
- Ensure GitHub repository has Actions enabled
- Check workflow permissions in repository settings

**Artifact Upload Issues:**
- Verify file paths in target directory
- Check workflow artifact retention settings

**Security Scan Failures:**
- Review CVSS threshold settings
- Update vulnerable dependencies
- Check OWASP database connectivity

### Logs and Debugging
- All workflow runs are logged in GitHub Actions
- Test results are uploaded as artifacts
- Security scan reports available as downloadable artifacts

## Customization Options

### Workflow Modification
- Adjust security scan thresholds
- Modify release note templates
- Change artifact retention periods
- Update build matrix for multiple Java versions

### Version Management
- Customize version bump logic
- Modify SNAPSHOT handling
- Adjust tag naming conventions

### Notification Settings
- Configure different triggers
- Add Slack/email notifications
- Customize issue creation logic

## Benefits Achieved

1. **Automation:** Zero-touch deployments from master
2. **Quality:** Automated testing and security scanning
3. **Traceability:** Every build linked to commits and releases
4. **Security:** Proactive vulnerability management
5. **Maintenance:** Automated dependency updates
6. **Reliability:** Consistent, repeatable build process
7. **Visibility:** Clear status reporting and artifact management

## Next Steps

1. **Monitor Initial Runs:** Observe first few automated builds
2. **Branch Protection:** Consider enabling branch protection rules
3. **Notifications:** Set up additional notification channels if needed
4. **Documentation:** Update project README with CI/CD information
5. **Testing:** Verify manual release process works as expected

The CI/CD implementation provides a robust, secure, and automated pipeline for the Bamboo Artifactory Plugin, ensuring reliable releases while maintaining code quality and security standards.