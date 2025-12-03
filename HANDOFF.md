# Developer Handoff Guide

This guide explains how to maintain and extend the Nimbus OS Developer Portal built with Redocly Realm.

## Repository

**GitHub**: [github.com/Nimbus-Healthcare/nimbus-developer-portal](https://github.com/Nimbus-Healthcare/nimbus-developer-portal)

**Redocly Cloud**:
- Organization: Nimbus
- Project: Nimbus-OS
- Site URL: developer.nimbus-os.com

## Project Structure

```
nimbus-developer-portal/
├── redocly.yaml          # Redocly configuration
├── openapi.yaml          # OpenAPI 3.1 API specification
├── index.md              # Landing page
├── api-reference.md       # API reference page
├── api-terms.md          # API License Terms & Conditions
├── changelog.md          # API changelog
├── README.md             # Repository README
├── HANDOFF.md            # This file
└── guides/               # Documentation guides
    ├── overview.md
    ├── quickstart.md
    ├── authentication.md
    ├── orders.md
    ├── refills.md
    ├── tracking.md
    ├── webhooks.md
    ├── errors-idempotency.md
    └── environments.md
```

## Editing the OpenAPI Specification

The API reference is generated from `openapi.yaml`. To update:

1. **Edit `openapi.yaml`**:
   - Add new endpoints under `paths`
   - Add new schemas under `components/schemas`
   - Update existing endpoints or schemas

2. **Validate locally**:
   ```bash
   npx @redocly/cli lint openapi.yaml
   ```

3. **Preview changes**:
   ```bash
   npx @redocly/cli preview-docs
   ```

4. **Commit and push**: Changes will be reflected in Redocly Cloud

### OpenAPI Best Practices

- Use OpenAPI 3.1 format
- Include examples for all endpoints
- Document all required fields
- Use consistent naming conventions
- Add descriptions for all schemas
- Include error responses for all endpoints

## Adding New Guide Pages

To add a new guide:

1. **Create markdown file** in `guides/`:
   ```bash
   touch guides/new-guide.md
   ```

2. **Add frontmatter**:
   ```markdown
   ---
   title: Guide Title
   description: Brief description
   ---
   ```

3. **Add to navigation** in `redocly.yaml`:
   ```yaml
   site:
     navigation:
       - label: Guides
         items:
           - label: New Guide
             href: /guides/new-guide
   ```

4. **Write content** following WHOOP-style:
   - Short sections
   - Lots of whitespace
   - Code examples
   - Clear headings

## Local Development

### Prerequisites

- Node.js 18+ (required for Redocly CLI v2)
- npm or yarn

### Installation

```bash
# Install Redocly CLI globally
npm install -g @redocly/cli

# Or use npx (no installation needed)
npx @redocly/cli --version
```

### Linting

Validate the OpenAPI spec:

```bash
# From developer-portal directory
npx @redocly/cli lint openapi.yaml
```

Fix auto-fixable issues:

```bash
npx @redocly/cli lint openapi.yaml --fix
```

### Building Documentation

Build the documentation locally:

```bash
npx @redocly/cli build-docs
```

Output will be in `.redocly/build/` directory.

### Preview Documentation

Preview the documentation locally:

```bash
npx @redocly/cli preview-docs
```

This starts a local server (usually at `http://localhost:8080`).

### Development Workflow

1. Make changes to `openapi.yaml` or markdown files
2. Run linting: `npx @redocly/cli lint openapi.yaml`
3. Preview changes: `npx @redocly/cli preview-docs`
4. Commit and push to trigger Redocly Cloud build

## Redocly Cloud Deployment

The portal is configured to deploy automatically from the GitHub repository.

### Configuration

- **GitHub Repository**: `Nimbus-Healthcare/nimbus-developer-portal`
- **Organization**: `Nimbus`
- **Project**: `Nimbus-OS`
- **Site URL**: `https://developer.nimbus-os.com`

### Connecting to Redocly Cloud

1. Log in to [Redocly Cloud](https://app.cloud.redocly.com)
2. Select the **Nimbus** organization
3. Go to **Projects** → **Nimbus-OS**
4. Click **Connect Git Repository**
5. Select **GitHub** and authorize access
6. Choose repository: `Nimbus-Healthcare/nimbus-developer-portal`
7. Select branch: `main` (or your default branch)
8. Set root path: `/` (root of repository)
9. Save configuration

### Deployment Process

1. **Push to GitHub**: Changes pushed to `main` branch
2. **Redocly Cloud detects**: Webhook triggers build automatically
3. **Build runs**: Documentation built from `openapi.yaml` and markdown files
4. **Preview available**: Preview URL provided for review
5. **Deploy**: Deploy to production URL

### Manual Deployment

If needed, deploy manually from Redocly Cloud dashboard:

1. Log in to [Redocly Cloud](https://app.cloud.redocly.com)
2. Select the **Nimbus** organization
3. Select the **Nimbus-OS** project
4. Click **Deploy** or **Publish**

## Branding & Styling

### Colors

Brand colors are configured in `redocly.yaml`:

- **Primary (Teal)**: `#0ED2C3`
- **Secondary (Azure)**: `#0041C2`
- **Accent (Violet)**: `#8A7CFF`
- **Success**: `#2FFFC7`
- **Error**: `#FF4F7B`

### Typography

- **Headings**: Montserrat Light (300)
- **Body**: Inter Regular (400)
- **Code**: JetBrains Mono

### Logo & Favicon

- **Logo**: `../NB_br_Logo Black/NB_br_Logo Black_0325.png`
- **Favicon**: `../NB_br_Favicon/NIMBUS_favicon.ico`

Update paths in `redocly.yaml` if logo location changes.

## Next Steps Backlog

### High Priority

- [ ] **Replace placeholder base URLs**: Update sandbox and production URLs in `openapi.yaml`
- [ ] **Add complete endpoint schemas**: Expand schemas with all fields
- [ ] **Implement webhook signing**: Add webhook signature verification details
- [ ] **Add API key generation flow**: Document how developers get API keys

### Medium Priority

- [ ] **Add OAuth 2.0**: If OAuth is needed, add authentication flow
- [ ] **Add more webhook events**: Expand webhook event types
- [ ] **Add order cancellation endpoint**: `DELETE /orders/{orderId}`
- [ ] **Add refill status endpoint**: `GET /refills/{refillId}`
- [ ] **Add pagination**: For list endpoints (when added)

### Low Priority

- [ ] **Add rate limit details**: Document specific rate limits per endpoint
- [ ] **Add webhook retry details**: Document retry behavior
- [ ] **Add HIPAA compliance details**: Legal review and documentation
- [ ] **Add SDK examples**: Code examples in multiple languages
- [ ] **Add Postman collection**: Importable API collection

## Troubleshooting

### OpenAPI Validation Errors

If linting fails:

1. Check error messages: `npx @redocly/cli lint openapi.yaml`
2. Fix syntax errors (indentation, YAML format)
3. Verify schema references are correct
4. Check for duplicate operation IDs

### Build Failures

If Redocly Cloud build fails:

1. Check build logs in Redocly Cloud dashboard
2. Validate OpenAPI locally first
3. Check for broken internal links
4. Verify markdown syntax

### Navigation Issues

If navigation doesn't work:

1. Check `redocly.yaml` navigation structure
2. Verify file paths match `href` values
3. Ensure markdown files exist
4. Check for typos in paths

## Resources

- [Redocly Documentation](https://redocly.com/docs)
- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Redocly CLI Reference](https://redocly.com/docs/cli)
- [Nimbus OS Brand Guidelines](../NIMBUS-OS-BRAND-GUIDELINES-2025-11-24.md)

## Support

For questions about:
- **Redocly**: [Redocly Support](https://redocly.com/support)
- **API**: [api@nimbus-os.com](mailto:api@nimbus-os.com)
- **Portal**: Contact the development team

---

**Last Updated**: January 2025
# Nimbus OS Developer Portal
