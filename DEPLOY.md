# Deployment Guide

This developer portal can be deployed via **Redocly Cloud** (recommended) or **Vercel** (alternative).

## Option 1: Redocly Cloud (Recommended)

Redocly Cloud is the native platform for Redocly Realm projects and provides the best experience.

### Quick Setup

1. **Connect Repository**:
   - Go to [Redocly Cloud](https://app.cloud.redocly.com)
   - Navigate to **Nimbus** organization → **Nimbus-OS** project
   - Click **"Connect your Git repo"**
   - Select: `Nimbus-Healthcare/nimbus-developer-portal`
   - Branch: `main`
   - Root path: `/`

2. **Configure Custom Domain**:
   - In project settings → **Custom Domain**
   - Add: `developer.nimbus-os.com`
   - Follow DNS instructions (CNAME or A records)

3. **Deploy**:
   - Push to `main` branch triggers automatic build
   - Or manually deploy from Redocly Cloud dashboard

**Benefits**:
- Native Redocly Realm support
- Automatic OpenAPI rendering
- Built-in preview environments
- Optimized for API documentation

See [SETUP.md](./SETUP.md) for detailed instructions.

---

## Option 2: Vercel Deployment

Vercel can host the static build output from Redocly CLI.

### Prerequisites

- Vercel account
- Vercel CLI installed: `npm i -g vercel`

### Build Locally First

```bash
# Install dependencies
npm install

# Build documentation
npm run build

# This creates .redocly/build/ directory
```

### Deploy to Vercel

**Via Vercel Dashboard:**

1. Go to [vercel.com](https://vercel.com)
2. Click **"Add New Project"**
3. Import `Nimbus-Healthcare/nimbus-developer-portal`
4. Configure:
   - **Framework Preset**: Other
   - **Build Command**: `npm run build`
   - **Output Directory**: `.redocly/build`
   - **Install Command**: `npm install`
5. Add environment variable (if needed):
   - `NODE_VERSION`: `18`
6. Click **"Deploy"**

**Via Vercel CLI:**

```bash
# Install Vercel CLI
npm i -g vercel

# Login
vercel login

# Deploy
vercel --prod

# Follow prompts:
# - Link to existing project? No (first time)
# - Project name: nimbus-developer-portal
# - Directory: .redocly/build
```

### Configure Custom Domain

1. In Vercel dashboard → Project → Settings → Domains
2. Add domain: `developer.nimbus-os.com`
3. Configure DNS:
   - Add CNAME: `developer.nimbus-os.com` → `cname.vercel-dns.com`
   - Or use A records as shown in Vercel

### Update Build Configuration

The `vercel.json` file is already configured. Vercel will:
- Run `npm run build` on each push
- Serve from `.redocly/build` directory
- Handle routing for SPA

### Limitations with Vercel

- Requires manual build step (Redocly CLI must run)
- Less optimized for API documentation features
- No built-in OpenAPI preview features
- Need to handle logo paths differently

---

## Comparison

| Feature | Redocly Cloud | Vercel |
|---------|---------------|--------|
| Native Redocly support | ✅ Yes | ❌ No |
| Automatic builds | ✅ Yes | ✅ Yes |
| Custom domain | ✅ Yes | ✅ Yes |
| OpenAPI rendering | ✅ Optimized | ⚠️ Static only |
| Preview environments | ✅ Yes | ⚠️ Limited |
| Cost | 💰 Paid | 💰 Free tier available |

---

## Recommendation

**Use Redocly Cloud** for the best developer portal experience. It's specifically designed for API documentation and provides features Vercel cannot match.

---

## Troubleshooting

### Build Fails on Vercel

- Ensure `package.json` has `@redocly/cli` in dependencies
- Check Node version (set to 18+)
- Verify `vercel.json` configuration

### Logo/Favicon Not Loading

Update paths in `redocly.yaml` to use:
- Absolute URLs (CDN)
- GitHub raw URLs
- Or copy assets to repository

### Domain Not Working

- Verify DNS records are correct
- Wait for DNS propagation (up to 48 hours)
- Check SSL certificate status

---

**Last Updated**: January 2025
