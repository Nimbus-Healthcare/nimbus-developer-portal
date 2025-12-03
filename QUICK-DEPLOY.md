# Quick Deploy to Vercel

## Option A: Deploy via Vercel Dashboard (Easiest)

1. **Go to Vercel**: https://vercel.com
2. **Sign in** with GitHub
3. **Click "Add New Project"**
4. **Import Git Repository**:
   - Select: `Nimbus-Healthcare/nimbus-developer-portal`
   - Click "Import"
5. **Configure Project**:
   - **Framework Preset**: Other
   - **Root Directory**: `./` (leave as is)
   - **Build Command**: `npm run build`
   - **Output Directory**: `.redocly/build`
   - **Install Command**: `npm install`
6. **Environment Variables**: None needed
7. **Click "Deploy"**

## Option B: Deploy via CLI

```bash
# Install Vercel CLI
npm install -g vercel

# Navigate to project
cd developer-portal

# Login to Vercel
vercel login

# Deploy (first time - follow prompts)
vercel

# Deploy to production
vercel --prod
```

## Configure Custom Domain

After deployment:

1. Go to Vercel Dashboard → Your Project → Settings → Domains
2. Add domain: `developer.nimbus-os.com`
3. Configure DNS:
   - **CNAME**: `developer.nimbus-os.com` → `cname.vercel-dns.com`
   - Or use A records shown in Vercel dashboard
4. Wait for DNS propagation (5-60 minutes)

## Important Notes

⚠️ **Redocly Cloud is Recommended**: This portal is built with Redocly Realm, which works best with Redocly Cloud. Vercel deployment works but lacks some features.

⚠️ **Logo Assets**: Logo paths in `redocly.yaml` need to be updated for Vercel deployment. See `DEPLOY.md` for options.

## Next Steps

1. Deploy to Vercel (follow steps above)
2. Configure custom domain
3. Test the site
4. Consider Redocly Cloud for better API docs features
