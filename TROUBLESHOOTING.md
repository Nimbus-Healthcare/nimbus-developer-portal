# Deployment Troubleshooting

## Current Issue

GitHub connection is complete, but site still shows "Redocly Museum API" content instead of Nimbus OS content.

## Possible Causes

1. **Deployment hasn't run yet** - Connection may be new, deployment may be queued
2. **Wrong branch/root path** - Configuration may point to wrong location
3. **Build failure** - Deployment may have failed silently
4. **Caching** - Browser/CDN cache showing old content

## Steps to Check

### 1. Verify Git Connection Settings
- Go to: https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/settings/git
- Verify:
  - Repository: `Nimbus-Healthcare/nimbus-developer-portal`
  - Branch: `main`
  - Root path: `/` (should be empty or `/`)

### 2. Check Deployment Status
- Go to: https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/deployment
- Look for:
  - Recent deployments
  - Build logs
  - Error messages
  - Status (success/failed/pending)

### 3. Trigger Manual Deployment
If no recent deployment:
- Look for "Deploy" or "Redeploy" button
- Or make a small commit to trigger webhook:
  ```bash
  echo "# Trigger deployment" >> TRIGGER.md
  git add TRIGGER.md
  git commit -m "Trigger deployment"
  git push origin main
  ```

### 4. Check Branch Configuration
- Go to: https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/settings/branches-and-deployment
- Verify `main` branch is configured for production

### 5. Clear Cache
- Hard refresh browser: Cmd+Shift+R (Mac) or Ctrl+Shift+R (Windows)
- Or check in incognito/private window

## Expected Files in Repository

All files should be at root level:
- `index.md` ✅
- `openapi.yaml` ✅
- `redocly.yaml` ✅
- `guides/` directory ✅
- `api-reference.md` ✅
- `changelog.md` ✅
- `api-terms.md` ✅

## Verification

After deployment, site should show:
- Title: "Nimbus OS Developer Portal"
- Navigation with: Overview, Quickstart, Authentication, Orders, etc.
- API Reference showing Nimbus OS endpoints

Check with:
```bash
curl -s "https://nimbus-os.redocly.app" | grep -i "nimbus os"
```
