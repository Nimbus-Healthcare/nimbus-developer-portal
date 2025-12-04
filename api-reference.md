---
title: API Reference
description: Complete API endpoint documentation
---

# API Reference

Complete reference documentation for all Nimbus OS API endpoints.

## Base URLs

| Environment | URL |
|-------------|-----|
| **Sandbox** | `https://api-sandbox.nimbus-os.com` |
| **Production** | `https://api.nimbus-os.com` |

## Authentication

All API requests require an API key in the `X-API-Key` header:

```bash
curl https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json"
```

## Available Endpoints

### Orders

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/orders` | Create a new order |
| `GET` | `/orders/{orderId}` | Get order details |

### Refills

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/refills` | Request a prescription refill |

### Tracking

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/tracking/{orderId}` | Get tracking information |

## Response Format

All responses are returned in JSON format with the following structure:

```json
{
  "data": { ... },
  "meta": {
    "requestId": "req_abc123"
  }
}
```

## Error Handling

Error responses include a standardized error object:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request parameters",
    "details": [...]
  }
}
```

For detailed endpoint documentation, see the [OpenAPI specification](./openapi.yaml).
