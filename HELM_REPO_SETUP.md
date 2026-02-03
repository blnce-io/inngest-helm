# Helm Repository Setup Guide

This guide explains how to set up and use the Helm repository for the Inngest Helm chart.

## Initial Setup (One-Time Configuration)

### 1. Enable GitHub Pages

To publish your Helm repository, you need to enable GitHub Pages for this repository:

1. Go to your GitHub repository settings
2. Navigate to **Settings** > **Pages**
3. Under "Build and deployment":
   - **Source**: Deploy from a branch
   - **Branch**: Select `gh-pages` and `/ (root)` folder
   - Click **Save**

The Helm repository will be available at:
```
https://blnce-io.github.io/inngest-helm/
```

### 2. How It Works

The GitHub Actions workflow (`.github/workflows/release-chart.yml`) automatically:

1. **Triggers** when:
   - Changes are pushed to `main` branch that affect chart files
   - Manually triggered via GitHub Actions UI

2. **Packages** the Helm chart into a `.tgz` file

3. **Creates a GitHub Release** with the packaged chart

4. **Updates** the `index.yaml` file in the `gh-pages` branch

5. **Publishes** to GitHub Pages, making it available as a Helm repository

## Using the Helm Repository

### Adding the Repository

Once GitHub Pages is enabled, users can add your Helm repository:

```bash
helm repo add inngest https://blnce-io.github.io/inngest-helm/
helm repo update
```

### Installing the Chart

```bash
# Search for available versions
helm search repo inngest

# Install the chart
helm install my-inngest inngest/inngest \
  --set inngest.eventKey=your-event-key \
  --set inngest.signingKey=your-signing-key

# Install a specific version
helm install my-inngest inngest/inngest --version 0.1.0 \
  --set inngest.eventKey=your-event-key \
  --set inngest.signingKey=your-signing-key
```

### Upgrading Installations

```bash
# Upgrade to latest version
helm upgrade my-inngest inngest/inngest

# Upgrade to specific version
helm upgrade my-inngest inngest/inngest --version 0.2.0
```

## Releasing New Versions

### Version Management

The chart version is defined in `Chart.yaml`:

```yaml
version: 0.1.0  # Chart version
appVersion: "latest"  # Application version
```

### Release Process

1. **Update the version** in `Chart.yaml`:
   ```yaml
   version: 0.2.0  # Increment according to semver
   ```

2. **Commit and push** to the `main` branch:
   ```bash
   git add Chart.yaml
   git commit -m "Bump chart version to 0.2.0"
   git push origin main
   ```

3. **Automatic release**: The GitHub Actions workflow will automatically:
   - Package the new chart version
   - Create a GitHub release with tag `inngest-0.2.0`
   - Update the Helm repository index
   - Publish to GitHub Pages

4. **Verify the release**:
   - Check the "Releases" section in your GitHub repository
   - Verify the `gh-pages` branch has been updated
   - Test with: `helm repo update && helm search repo inngest`

### Manual Release Trigger

You can also manually trigger a release:

1. Go to **Actions** tab in GitHub
2. Select **Release Helm Chart** workflow
3. Click **Run workflow**
4. Select the `main` branch
5. Click **Run workflow**

## Semantic Versioning

Follow semantic versioning (semver) for chart versions:

- **Major** (1.0.0): Breaking changes
- **Minor** (0.1.0): New features, backwards compatible
- **Patch** (0.0.1): Bug fixes, backwards compatible

Example progression:
```
0.1.0 → 0.1.1 (bug fix)
0.1.1 → 0.2.0 (new feature)
0.2.0 → 1.0.0 (breaking change)
```

## Repository Structure

After the first release, the `gh-pages` branch will contain:

```
gh-pages/
├── index.yaml              # Helm repository index
├── inngest-0.1.0.tgz      # Packaged chart v0.1.0
├── inngest-0.2.0.tgz      # Packaged chart v0.2.0
└── ...
```

## Troubleshooting

### Charts Not Appearing in Repository

1. Check the GitHub Actions workflow completed successfully
2. Verify GitHub Pages is enabled and set to `gh-pages` branch
3. Check the `gh-pages` branch exists and contains `index.yaml`
4. Wait a few minutes for GitHub Pages to deploy

### Users Getting 404 Errors

1. Verify the repository URL is correct
2. Check GitHub Pages is publicly accessible (repo must be public)
3. Ensure the `gh-pages` branch is not empty

### Old Versions Not Showing

The workflow uses `skip_existing: true`, which means it won't re-release existing versions. To re-release:

1. Increment the version in `Chart.yaml`
2. Push the changes

## Advanced Configuration

### Custom Domain

To use a custom domain for your Helm repository:

1. Add a `CNAME` file to the `gh-pages` branch with your domain
2. Configure DNS settings for your domain
3. Update GitHub Pages settings to use the custom domain

### Private Repository

For private Helm repositories, consider:
- GitHub Packages (OCI registry)
- ChartMuseum (self-hosted)
- Cloud storage (S3, GCS) with authentication

## Testing Locally

Before releasing, test your chart locally:

```bash
# Package the chart
helm package .

# Create local index
helm repo index . --url https://<username>.github.io/<repo-name>/

# Test installation from local package
helm install test-inngest ./inngest-0.1.0.tgz \
  --set inngest.eventKey=test \
  --set inngest.signingKey=test \
  --dry-run --debug
```

## Additional Resources

- [Helm Chart Repository Guide](https://helm.sh/docs/topics/chart_repository/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Chart Releaser Action](https://github.com/helm/chart-releaser-action)
- [Semantic Versioning](https://semver.org/)
