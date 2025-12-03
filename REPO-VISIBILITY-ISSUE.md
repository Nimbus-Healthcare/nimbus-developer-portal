# Repository Visibility Issue

## Problem

GitHub repository `Nimbus-Healthcare/nimbus-developer-portal` appears to be **private**, which may be preventing Redocly Cloud from accessing it.

## Symptoms

- Repository exists (git commands work)
- GitHub web shows "Page not found" (typical for private repos)
- Redocly Cloud site still shows default "Museum API" content

## Solution

### Option 1: Make Repository Public (Recommended for Developer Portal)

1. Go to: https://github.com/Nimbus-Healthcare/nimbus-developer-portal/settings
2. Scroll to "Danger Zone"
3. Click "Change visibility"
4. Select "Make public"
5. Confirm

**Note**: Developer portals are typically public. If you need to keep it private, use Option 2.

### Option 2: Ensure Redocly Cloud Has Access (Keep Private)

1. Go to: https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/settings/git
2. Verify GitHub connection shows:
   - Organization: `Nimbus-Healthcare`
   - Repository: `nimbus-developer-portal`
   - Permissions: Full access to private repositories
3. If not connected, reconnect and ensure you grant access to:
   - `Nimbus-Healthcare` organization
   - Private repositories
   - Read access to repository contents

### Option 3: Check GitHub App Permissions

1. Go to: https://github.com/organizations/Nimbus-Healthcare/settings/installations
2. Find "Redocly Cloud" app
3. Verify it has:
   - Repository access to `nimbus-developer-portal`
   - Read access to contents
   - Read access to metadata

## After Fix

Once visibility/permissions are fixed:
1. Redocly Cloud should automatically detect the repository
2. Deployment should trigger automatically
3. Site should update within 1-3 minutes

## Verification

After fixing, check:
```bash
curl -s "https://nimbus-os.redocly.app" | grep -i "nimbus os developer portal"
```

Should show "Nimbus OS Developer Portal" instead of "Museum API".
