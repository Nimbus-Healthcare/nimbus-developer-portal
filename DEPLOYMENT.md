# Deployment Guide

This guide covers deployment processes, branch protection, and preview deployments for the Nimbus OS Developer Portal.

## Overview

The Nimbus OS Developer Portal is deployed automatically via Redocly Cloud, which is connected to the GitHub repository `Nimbus-Healthcare/nimbus-developer-portal`.

**Live Site**: [nimbus-os.redocly.app](https://nimbus-os.redocly.app)

## Deployment Architecture

```
GitHub Repository (main branch)
    ↓
Redocly Cloud (automatic webhook)
    ↓
Build Process (validate → lint → build → publish)
    ↓
Production Deployment
```

## Automatic Deployments

### Main Branch Deployments

When code is pushed to the `main` branch:

1. **Webhook Trigger**: Redocly Cloud detects the push via GitHub webhook
2. **Build Starts**: Build process begins automatically
3. **Validation**: OpenAPI spec is validated
4. **Linting**: Code quality checks run
5. **Link Checking**: All links are verified
6. **Build**: Documentation is compiled
7. **Deploy**: If successful, deploys to production

### Deployment Status

Monitor deployments at:
[https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/deployments](https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/deployments)

## Preview Deployments

### Pull Request Previews

When a pull request is created:

1. **Automatic Preview**: Redocly Cloud creates a preview deployment
2. **Preview URL**: Unique URL is generated for the PR
3. **Review Changes**: Preview URL is available in:
   - Redocly Cloud dashboard
   - PR comments (if GitHub integration configured)
   - PR status checks

### Accessing Preview URLs

1. Navigate to Redocly Cloud dashboard
2. Go to **Deployments** section
3. Find the preview deployment (marked as "Preview" or branch name)
4. Click **Visit** to open preview URL

### Preview Deployment Benefits

- ✅ Review changes before merging
- ✅ Share preview with team for feedback
- ✅ Test changes in production-like environment
- ✅ Verify build succeeds before merge

## Manual Deployment

### Trigger Deployment Manually

If automatic deployment doesn't trigger:

1. Navigate to [Redocly Cloud Deployments](https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/deployments)
2. Click **"Trigger deployment"** button
3. Select branch (default: `main`)
4. Monitor build progress
5. Wait for completion

### When to Use Manual Deployment

- After fixing build errors
- To redeploy without code changes
- To test deployment process
- After configuration changes

## Branch Protection

### Recommended GitHub Settings

To protect the `main` branch, configure in GitHub repository settings:

**Path**: `Settings` → `Branches` → `Branch protection rules` → `Add rule`

#### Recommended Rules

1. **Require Pull Request Reviews**
   - Require at least 1 approval
   - Dismiss stale reviews when new commits are pushed
   - Require review from code owners (if configured)

2. **Require Status Checks to Pass**
   - Require branches to be up to date before merging
   - Required status checks:
     - `Redocly Build` (if available)
     - `OpenAPI Validation` (from CI workflow)

3. **Require Conversation Resolution**
   - Require all conversations to be resolved before merging

4. **Do Not Allow**
   - Force pushes
   - Deletions of the protected branch

5. **Allow Specific Actors**
   - Allow administrators to bypass (optional)

### Setting Up Branch Protection

1. Go to repository: [github.com/Nimbus-Healthcare/nimbus-developer-portal](https://github.com/Nimbus-Healthcare/nimbus-developer-portal)
2. Click **Settings** → **Branches**
3. Click **Add rule** or edit existing rule for `main`
4. Configure protection rules as above
5. Click **Create** or **Save changes**

## CI/CD Workflow

### GitHub Actions Workflow

The repository includes `.github/workflows/validate.yml` which:

- Validates OpenAPI specification
- Runs Redocly CLI linting
- Checks for common errors
- Provides feedback in PR comments

### Workflow Triggers

- **On Pull Request**: Validates changes before merge
- **On Push to Main**: Validates after merge
- **Manual Trigger**: Can be run manually from Actions tab

### Viewing Workflow Runs

1. Go to repository on GitHub
2. Click **Actions** tab
3. View workflow runs and results
4. Click on a run to see detailed logs

## Deployment Environments

### Production

- **URL**: [nimbus-os.redocly.app](https://nimbus-os.redocly.app)
- **Branch**: `main`
- **Auto-deploy**: Yes
- **Status**: Live

### Preview/Staging

- **URL**: Generated per PR/branch
- **Branch**: Feature branches
- **Auto-deploy**: Yes (on PR creation)
- **Status**: Temporary (deleted when PR closes)

## Deployment Checklist

Before deploying:

- [ ] OpenAPI spec validates: `npx @redocly/cli lint openapi.yaml`
- [ ] Local preview works: `npx @redocly/cli preview-docs`
- [ ] No broken links
- [ ] Logo displays correctly
- [ ] Theme colors applied
- [ ] All navigation links work
- [ ] Mobile responsive
- [ ] Dark mode works

## Troubleshooting Deployments

### Build Failures

**Common Issues:**

1. **OpenAPI Validation Errors**
   - Fix: Run `npx @redocly/cli lint openapi.yaml --fix`
   - Check: All required fields are present

2. **Markdoc Errors**
   - Fix: Remove unsupported tags
   - Check: Use only supported Markdoc syntax

3. **Missing Files**
   - Fix: Ensure all referenced files exist
   - Check: File paths are correct

4. **CSS Errors**
   - Fix: Validate CSS syntax
   - Check: CSS variables are properly defined

### Deployment Not Triggering

1. **Check Webhook**: Verify GitHub webhook is configured in Redocly Cloud
2. **Check Branch**: Ensure pushing to correct branch (`main`)
3. **Check Permissions**: Verify Redocly Cloud has repository access
4. **Manual Trigger**: Use manual deployment as workaround

### Preview Not Creating

1. **Check Redocly Cloud Settings**: Ensure preview deployments are enabled
2. **Check PR**: Ensure PR is open (not draft)
3. **Check Branch**: Ensure branch exists and has commits
4. **Wait**: Preview deployments may take a few minutes

## Monitoring

### Deployment Status

Monitor at:
- [Redocly Cloud Dashboard](https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/deployments)
- GitHub Actions (for CI validation)

### Build Metrics

Track:
- Build success rate
- Average build time
- Failed deployments
- Preview deployment usage

## Best Practices

### Before Merging PRs

1. ✅ Review preview deployment
2. ✅ Verify all checks pass
3. ✅ Test on preview URL
4. ✅ Get approval from reviewer
5. ✅ Merge to `main`

### Deployment Frequency

- **Regular Updates**: Deploy as needed for content updates
- **API Changes**: Deploy immediately after API spec updates
- **Bug Fixes**: Deploy immediately for critical fixes
- **Features**: Deploy after testing in preview

### Rollback Procedure

If a bad deployment occurs:

1. Identify the last good commit
2. Revert to that commit:
   ```bash
   git revert <bad-commit-hash>
   git push origin main
   ```
3. Or manually trigger deployment from previous commit in Redocly Cloud

## Security Considerations

### API Keys

- Never commit API keys to repository
- Use environment variables in Redocly Cloud
- Rotate keys regularly

### Access Control

- Limit who can merge to `main`
- Require PR reviews
- Use branch protection rules
- Monitor deployment access logs

## Resources

- [Redocly Cloud Documentation](https://redocly.com/docs/realm)
- [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches)
- [GitHub Actions](https://docs.github.com/en/actions)

---

**Last Updated**: December 2025
