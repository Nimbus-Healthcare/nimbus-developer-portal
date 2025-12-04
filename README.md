# Nimbus OS Developer Portal

Public developer portal for the Nimbus OS API, built with Redocly Realm.

**Live Site**: [nimbus-os.redocly.app](https://nimbus-os.redocly.app)

## Overview

This repository contains the complete source for the Nimbus OS Developer Portal, including:

- API reference documentation (OpenAPI 3.1)
- Integration guides and tutorials
- API terms and conditions
- Changelog and version history

## Repository

**GitHub**: [github.com/Nimbus-Healthcare/nimbus-developer-portal](https://github.com/Nimbus-Healthcare/nimbus-developer-portal)

## Quick Start

### Prerequisites

- Node.js 18+ (for Redocly CLI)
- Git
- A Redocly Cloud account (for deployment)

### Local Development

1. **Clone the repository**:
   ```bash
   git clone https://github.com/Nimbus-Healthcare/nimbus-developer-portal.git
   cd nimbus-developer-portal
   ```

2. **Install Redocly CLI** (optional, for local preview):
   ```bash
   npm install -g @redocly/cli
   # Or use npx (no installation needed)
   npx @redocly/cli --version
   ```

3. **Validate OpenAPI spec**:
   ```bash
   npx @redocly/cli lint openapi.yaml
   ```

4. **Preview locally**:
   ```bash
   npx @redocly/cli preview-docs
   ```
   Opens at `http://localhost:8080`

## Project Structure

```
nimbus-developer-portal/
├── @theme/
│   └── styles.css          # Custom theme CSS with Nimbus brand colors
├── assets/
│   └── logo.png            # Nimbus OS logo
├── .github/
│   └── workflows/
│       └── validate.yml    # CI validation workflow
├── guides/                  # Documentation guides
│   ├── overview.md
│   ├── quickstart.md
│   ├── authentication.md
│   ├── orders.md
│   ├── refills.md
│   ├── tracking.md
│   ├── webhooks.md
│   ├── errors-idempotency.md
│   └── environments.md
├── redocly.yaml            # Redocly Realm configuration
├── openapi.yaml            # OpenAPI 3.1 API specification
├── index.md                # Landing page
├── api-reference.md        # API reference page
├── api-terms.md           # API License Terms & Conditions
├── changelog.md           # API changelog
├── README.md              # This file
├── HANDOFF.md             # Developer handoff guide
└── package.json           # Dependencies (if any)
```

## Redocly Cloud Integration

This repository is connected to Redocly Cloud for automatic deployment:

- **Organization**: Nimbus (nimbus-d39ns)
- **Project**: Nimbus-OS
- **Site URL**: [nimbus-os.redocly.app](https://nimbus-os.redocly.app)

### Deployment Process

1. **Automatic Deployment**: Changes pushed to the `main` branch automatically trigger a build and deployment in Redocly Cloud.

2. **Manual Deployment**: 
   - Navigate to [Redocly Cloud Deployments](https://app.cloud.redocly.com/org/nimbus-d39ns/project/nimbus-os/deployments)
   - Click "Trigger deployment" button
   - Monitor build progress in the dashboard

3. **Preview Deployments**: 
   - Pull requests automatically create preview deployments
   - Preview URLs are available in the Redocly Cloud dashboard
   - Use previews to review changes before merging

### Deployment Status

- ✅ Build: Validates OpenAPI spec and builds documentation
- ✅ Lint: Checks code quality and documentation standards
- ✅ Link Checker: Verifies all internal and external links
- ✅ Respect Monitoring: Monitors API compliance

## Editing Documentation

### Update OpenAPI Spec

Edit `openapi.yaml` to add or modify API endpoints:

```bash
# Validate before committing
npx @redocly/cli lint openapi.yaml

# Auto-fix common issues
npx @redocly/cli lint openapi.yaml --fix
```

### Add New Guide Pages

1. Create markdown file in `guides/` directory:
   ```bash
   touch guides/new-guide.md
   ```

2. Add frontmatter:
   ```markdown
   ---
   title: Guide Title
   description: Brief description
   ---
   ```

3. Update navigation in `redocly.yaml`:
   ```yaml
   navbar:
     items:
       - group: Guides
         items:
           - page: guides/new-guide.md
             label: New Guide
   ```

4. Commit and push - deployment happens automatically

See [HANDOFF.md](./HANDOFF.md) for detailed instructions.

## Branding & Theme Customization

The portal uses Nimbus OS brand colors and styling:

- **Primary Color**: Teal `#0ED2C3`
- **Success Color**: Signal Green `#2FFFC7`
- **Error Color**: Crimson `#FF4F7B`
- **Logo**: Located in `assets/logo.png`

Theme customization is done via `@theme/styles.css` using CSS variables. See [THEME.md](./THEME.md) for detailed customization guide.

## Contributing

### Workflow

1. **Create a feature branch**:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**:
   - Edit markdown files, OpenAPI spec, or theme CSS
   - Validate OpenAPI spec: `npx @redocly/cli lint openapi.yaml`
   - Preview locally: `npx @redocly/cli preview-docs`

3. **Commit and push**:
   ```bash
   git add .
   git commit -m "feat: description of changes"
   git push origin feature/your-feature-name
   ```

4. **Create pull request**:
   - PR will automatically create a preview deployment
   - Review changes in preview URL
   - Request review from team
   - Merge to `main` triggers production deployment

### Code Standards

- **OpenAPI**: Follow OpenAPI 3.1 specification
- **Markdown**: Use consistent formatting and frontmatter
- **CSS**: Follow existing variable naming conventions
- **Commits**: Use conventional commit messages (feat:, fix:, docs:, etc.)

## Testing

### Local Testing

```bash
# Validate OpenAPI spec
npx @redocly/cli lint openapi.yaml

# Preview documentation
npx @redocly/cli preview-docs

# Check for broken links (if available)
npx @redocly/cli lint --format=stylish
```

### Production Testing Checklist

- [ ] All navigation links work
- [ ] Mobile responsiveness verified
- [ ] Dark mode toggle works
- [ ] Logo displays correctly
- [ ] Brand colors applied (teal links)
- [ ] API reference renders correctly
- [ ] All guide pages accessible

## Resources

- [Redocly Documentation](https://redocly.com/docs)
- [Redocly Realm Guide](https://redocly.com/docs/realm)
- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Redocly CLI Reference](https://redocly.com/docs/cli)
- [Theme Customization Guide](./THEME.md)

## Support

- **API Support**: [api@nimbus-os.com](mailto:api@nimbus-os.com)
- **Legal Inquiries**: [legal@nimbushealthcare.com](mailto:legal@nimbushealthcare.com)
- **Redocly Support**: [Redocly Support](https://redocly.com/support)
- **Portal Issues**: Open an issue in this repository

## License

Proprietary - See [API Terms & Conditions](./api-terms.md)

---

**Last Updated**: December 2025
