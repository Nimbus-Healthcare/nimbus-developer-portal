# Redocly Cloud Setup Guide

Step-by-step instructions for connecting this repository to Redocly Cloud.

## Prerequisites

- Redocly Cloud account with access to the **Nimbus** organization
- Admin access to the GitHub repository: `Nimbus-Healthcare/nimbus-developer-portal`

## Step 1: Connect Git Repository

1. Log in to [Redocly Cloud](https://app.cloud.redocly.com)
2. Navigate to the **Nimbus** organization
3. Go to **Projects** → **Nimbus-OS** (or create if it doesn't exist)
4. Click **"Connect your Git repo"** or **"Settings"** → **"Git Integration"**

## Step 2: Authorize GitHub Access

1. Click **"Connect GitHub"** or **"Add Git Provider"**
2. Authorize Redocly Cloud to access your GitHub account
3. Select the GitHub organization: **Nimbus-Healthcare**
4. Grant access to repositories (or specific repo access)

## Step 3: Select Repository

1. Choose repository: **`nimbus-developer-portal`**
2. Select branch: **`main`** (or your default branch)
3. Set root path: **`/`** (root of repository)
4. Click **"Connect"** or **"Save"**

## Step 4: Configure Build Settings

In the project settings:

1. **Build Configuration**:
   - Root directory: `/` (leave empty or set to `/`)
   - Build command: (Redocly handles this automatically)
   - Output directory: (Redocly handles this automatically)

2. **Environment Variables** (if needed):
   - None required for basic setup

3. **Custom Domain**:
   - Domain: `developer.nimbus-os.com`
   - Configure DNS as instructed by Redocly

## Step 5: Verify Connection

1. Make a small change to `README.md`
2. Commit and push to `main` branch:
   ```bash
   git add README.md
   git commit -m "Test: Verify Redocly Cloud connection"
   git push origin main
   ```
3. Check Redocly Cloud dashboard for build status
4. Build should start automatically within seconds

## Step 6: Configure DNS (if using custom domain)

1. In Redocly Cloud project settings → **Custom Domain**
2. Add domain: `developer.nimbus-os.com`
3. Follow DNS configuration instructions:
   - Add CNAME record pointing to Redocly Cloud
   - Or configure A records as specified

## Troubleshooting

### Build Not Triggering

- Verify webhook is installed in GitHub repository
- Check GitHub repository settings → Webhooks
- Ensure Redocly Cloud webhook is active

### Build Failures

- Check build logs in Redocly Cloud dashboard
- Validate OpenAPI spec: `npx @redocly/cli lint openapi.yaml`
- Check for syntax errors in markdown files

### Logo/Favicon Not Loading

**Important**: Logo and favicon paths currently reference the main repository:
- Logo: `../NB_br_Logo Black/NB_br_Logo Black_0325.png`
- Favicon: `../NB_br_Favicon/NIMBUS_favicon.ico`

**Options to fix**:

1. **Copy assets to this repo** (Recommended):
   ```bash
   # Create assets directory
   mkdir -p assets/logos assets/favicons
   
   # Copy from main repo (adjust path as needed)
   cp /path/to/main-repo/NB_br_Logo\ Black/NB_br_Logo\ Black_0325.png assets/logos/
   cp /path/to/main-repo/NB_br_Favicon/NIMBUS_favicon.ico assets/favicons/
   
   # Update redocly.yaml paths
   # logo.image: assets/logos/NB_br_Logo Black_0325.png
   # favicon: assets/favicons/NIMBUS_favicon.ico
   ```

2. **Use CDN/absolute URLs**:
   - Host logos on CDN or public URL
   - Update paths in `redocly.yaml` to absolute URLs

3. **Use GitHub raw URLs**:
   - Reference logos from main repo via GitHub raw URLs
   - Update paths accordingly

## Next Steps

After setup is complete:

1. Test deployment with a small change
2. Review preview URL
3. Deploy to production
4. Verify custom domain (if configured)
5. Share developer portal URL with team

## Support

- **Redocly Support**: [support@redocly.com](mailto:support@redocly.com) or [Redocly Docs](https://redocly.com/docs)
- **Internal**: Contact the development team

---

**Last Updated**: January 2025
