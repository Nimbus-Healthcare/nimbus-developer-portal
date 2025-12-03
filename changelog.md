---
title: Changelog
description: API version history and changes
---

# Changelog

All notable changes to the Nimbus OS API will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned
- OAuth 2.0 authentication support
- Additional webhook event types
- Order cancellation endpoint
- Refill status retrieval endpoint

## [1.0.0] - 2025-01-15

### Added
- Initial API release
- Order creation endpoint (`POST /orders`)
- Order retrieval endpoint (`GET /orders/{orderId}`)
- Refill creation endpoint (`POST /refills`)
- Webhook test endpoint (`POST /webhooks/test`)
- API key authentication
- Idempotency key support
- Webhook event types:
  - `order.created`
  - `order.fulfilled`
  - `refill.ready`
- Error response format with `requestId`
- Sandbox and production environments

### Documentation
- API reference documentation
- Quickstart guide
- Authentication guide
- Order management guide
- Refill workflow guide
- Webhook integration guide
- Error handling guide
- Environment configuration guide

---

## Versioning

The API uses semantic versioning (MAJOR.MINOR.PATCH):

- **MAJOR**: Breaking changes (require code changes)
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes (backward compatible)

## Deprecation Policy

We provide at least 6 months notice before deprecating endpoints or features:

1. **Announcement**: Deprecation announced in changelog
2. **Warning period**: Deprecation warnings in API responses
3. **Sunset**: Endpoint removed after notice period

## Migration Guides

Migration guides are provided for breaking changes:

- [Version 1.0 Migration Guide](#) (coming soon)

## Support

For questions about API changes:
- [API Support](mailto:api@nimbus-os.com)
- [Developer Portal](https://developer.nimbus-os.com)
