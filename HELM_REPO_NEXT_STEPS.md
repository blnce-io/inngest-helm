# Helm Repository - Next Steps

I've set up the infrastructure for your Helm repository. Here's what you need to do next:

## Immediate Actions Required

### 1. Update GitHub URLs in README.md

Replace `<username>` with your actual GitHub username or organization name in:
- The Helm repository URL
- The git clone URL

Search for `<username>` in README.md and replace with your actual GitHub username.

### 2. Enable GitHub Pages

1. Go to your GitHub repository
2. Click **Settings** → **Pages**
3. Under "Build and deployment":
   - **Source**: Deploy from a branch
   - **Branch**: Select `gh-pages` and `/ (root)` folder
   - Click **Save**

Note: The `gh-pages` branch will be created automatically when the first release workflow runs.

### 3. Trigger the First Release

Option A: Push a change to Chart.yaml, values.yaml, or templates/
```bash
git add .
git commit -m "Setup Helm repository infrastructure"
git push origin main
```

Option B: Manually trigger the workflow
1. Go to **Actions** tab in GitHub
2. Select **Release Helm Chart** workflow
3. Click **Run workflow**
4. Select `main` branch
5. Click **Run workflow**

### 4. Verify the Release

After the workflow completes:

1. Check that a GitHub Release was created
2. Verify the `gh-pages` branch exists and contains:
   - `index.yaml`
   - `inngest-0.1.0.tgz`
3. Wait 1-2 minutes for GitHub Pages to deploy
4. Test the repository:
   ```bash
   helm repo add inngest https://<your-username>.github.io/inngest-helm/
   helm repo update
   helm search repo inngest
   ```

## What Was Created

### 1. GitHub Actions Workflow (`.github/workflows/release-chart.yml`)

This workflow automatically:
- Packages the Helm chart
- Creates a GitHub Release
- Updates the Helm repository index
- Publishes to GitHub Pages

Triggers on:
- Push to main branch (when chart files change)
- Manual workflow dispatch

### 2. Setup Guide (`HELM_REPO_SETUP.md`)

Comprehensive documentation covering:
- Initial setup steps
- How the repository works
- Version management
- Release process
- Troubleshooting

### 3. Updated README.md

Added:
- Instructions for installing from the Helm repository
- Distinction between repository and source installation
- Reference to the setup guide

## Future Workflow

Once set up, releasing new versions is simple:

1. Update `Chart.yaml`:
   ```yaml
   version: 0.2.0  # Increment version
   ```

2. Commit and push:
   ```bash
   git add Chart.yaml
   git commit -m "Bump chart version to 0.2.0"
   git push origin main
   ```

3. The workflow automatically handles the rest

## Example Repository URL

If your GitHub details are:
- **Username/Org**: `inngest`
- **Repository**: `inngest-helm`

Then your Helm repository URL would be:
```
https://inngest.github.io/inngest-helm/
```

Users would add it with:
```bash
helm repo add inngest https://inngest.github.io/inngest-helm/
```

## Need Help?

Refer to:
- [HELM_REPO_SETUP.md](HELM_REPO_SETUP.md) - Complete setup guide
- [Helm Chart Repository Guide](https://helm.sh/docs/topics/chart_repository/)
- [Chart Releaser Action Docs](https://github.com/helm/chart-releaser-action)

## Checklist

- [ ] Update `<username>` placeholders in README.md
- [ ] Enable GitHub Pages in repository settings
- [ ] Trigger first release (push to main or manual trigger)
- [ ] Verify GitHub Release is created
- [ ] Verify `gh-pages` branch exists
- [ ] Test adding the Helm repository
- [ ] Test installing the chart from the repository
- [ ] Update documentation with actual repository URL
- [ ] Delete this file once setup is complete
