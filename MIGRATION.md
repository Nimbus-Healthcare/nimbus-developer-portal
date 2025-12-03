# Migration Checklist

Checklist for migrating the developer portal to the new repository.

## Pre-Migration

- [x] Create new repository: `Nimbus-Healthcare/nimbus-developer-portal`
- [x] Create README.md
- [x] Create SETUP.md
- [x] Update HANDOFF.md with new repo info

## Migration Steps

### 1. Copy Files

Copy all files from `developer-portal/` directory to the new repository root:

```bash
# From main repository root
cd developer-portal
git init
git remote add origin https://github.com/Nimbus-Healthcare/nimbus-developer-portal.git
git add .
git commit -m "Initial commit: Nimbus OS Developer Portal"
git push -u origin main
```

### 2. Handle Logo Assets

**Option A: Copy assets to new repo** (Recommended)

```bash
# In new repository
mkdir -p assets/logos assets/favicons

# Copy from main repo (adjust path as needed)
cp ../nimbus-os/NB_br_Logo\ Black/NB_br_Logo\ Black_0325.png assets/logos/
cp ../nimbus-os/NB_br_Favicon/NIMBUS_favicon.ico assets/favicons/

# Update redocly.yaml
# logo.image: assets/logos/NB_br_Logo Black_0325.png
# favicon: assets/favicons/NIMBUS_favicon.ico
```

**Option B: Use GitHub raw URLs**

Update `redocly.yaml`:
```yaml
logo:
  image: https://raw.githubusercontent.com/Nimbus-Healthcare/nimbus-os/main/NB_br_Logo%20Black/NB_br_Logo%20Black_0325.png
favicon: https://raw.githubusercontent.com/Nimbus-Healthcare/nimbus-os/main/NB_br_Favicon/NIMBUS_favicon.ico
```

**Option C: Use CDN/public URLs**

If logos are hosted elsewhere, update paths accordingly.

### 3. Connect to Redocly Cloud

1. Log in to [Redocly Cloud](https://app.cloud.redocly.com)
2. Go to **Nimbus** organization → **Nimbus-OS** project
3. Click **"Connect your Git repo"**
4. Select **GitHub** → **Nimbus-Healthcare/nimbus-developer-portal**
5. Set branch: `main`
6. Set root path: `/`
7. Save

### 4. Verify Deployment

- [ ] Push a test commit
- [ ] Verify Redocly Cloud detects the change
- [ ] Check build completes successfully
- [ ] Verify preview URL works
- [ ] Test logo/favicon loading
- [ ] Deploy to production

### 5. Update Documentation Links

Update any references to the old location:
- [ ] Internal documentation
- [ ] Team wiki/docs
- [ ] README files in other repos

### 6. Clean Up (Optional)

After confirming everything works:
- [ ] Remove `developer-portal/` from main repo (if desired)
- [ ] Update main repo README if it references developer portal
- [ ] Archive or delete old location

## Post-Migration

- [ ] Share new repository URL with team
- [ ] Update access permissions
- [ ] Set up branch protection rules
- [ ] Configure GitHub Actions (validate.yml included)
- [ ] Test full workflow: edit → commit → deploy

## Rollback Plan

If issues occur:
1. Keep old `developer-portal/` directory until migration is confirmed
2. Redocly Cloud can be reconnected to old location if needed
3. Both repos can coexist temporarily

---

**Status**: Ready for migration
