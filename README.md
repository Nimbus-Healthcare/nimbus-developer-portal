# Nimbus OS Developer Portal

Public developer portal for the Nimbus OS API, built with Redocly Realm.

**Live Site**: [developer.nimbus-os.com](https://developer.nimbus-os.com)

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

### Important: Logo Assets

The logo and favicon paths in `redocly.yaml` currently reference the main repository. You'll need to either:
1. Copy logo assets to this repository, or
2. Update paths to use CDN/absolute URLs

See [SETUP.md](./SETUP.md) for details.

### Local Development

1. **Clone the repository**:
   ```bash
   git clone https://github.com/Nimbus-Healthcare/nimbus-developer-portal.git
   cd nimbus-developer-portal
   ```

2. **Install Redocly CLI** (optional, for local preview):
   ```bash
   npm install -g @redocly/cli
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
.
├── redocly.yaml          # Redocly Realm configuration
├── openapi.yaml          # OpenAPI 3.1 API specification
├── index.md              # Landing page
├── api-reference.md      # API reference page
├── api-terms.md         # API License Terms & Conditions
├── changelog.md         # API changelog
├── README.md            # This file
├── HANDOFF.md           # Developer handoff guide
└── guides/              # Documentation guides
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

## Redocly Cloud Integration

This repository is connected to Redocly Cloud for automatic deployment:

- **Organization**: Nimbus
- **Project**: Nimbus-OS
- **Site URL**: developer.nimbus-os.com

### Deployment

Changes pushed to the `main` branch automatically trigger a build and deployment in Redocly Cloud.

### Manual Deployment

1. Push changes to GitHub
2. Redocly Cloud detects changes
3. Build runs automatically
4. Preview available for review
5. Deploy to production

## Editing Documentation

### Update OpenAPI Spec

Edit `openapi.yaml` to add or modify API endpoints:

```bash
# Validate before committing
npx @redocly/cli lint openapi.yaml
```

### Add New Guide Pages

1. Create markdown file in `guides/`
2. Add frontmatter with title and description
3. Update navigation in `redocly.yaml`
4. Commit and push

See [HANDOFF.md](./HANDOFF.md) for detailed instructions.

## Contributing

1. Create a feature branch
2. Make your changes
3. Validate OpenAPI spec: `npx @redocly/cli lint openapi.yaml`
4. Preview locally: `npx @redocly/cli preview-docs`
5. Commit and push
6. Create pull request

## Resources

- [Redocly Documentation](https://redocly.com/docs)
- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Redocly CLI Reference](https://redocly.com/docs/cli)
- [Nimbus OS Brand Guidelines](https://github.com/Nimbus-Healthcare/nimbus-os)

## Support

- **API Support**: [api@nimbus-os.com](mailto:api@nimbus-os.com)
- **Legal Inquiries**: [legal@nimbushealthcare.com](mailto:legal@nimbushealthcare.com)
- **Redocly Support**: [Redocly Support](https://redocly.com/support)

## License

Proprietary - See [API Terms & Conditions](./api-terms.md)

---

**Last Updated**: January 2025
