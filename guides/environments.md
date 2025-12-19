---
title: Environments
description: Development vs Production environments for NovaMed Partner API
---

# Environments

The NovaMed Partner API provides two environments: Development for testing and Production for live integrations.

## Base URLs

| Environment | Base URL |
|-------------|----------|
| **Development** | `https://novamed-feapidev.nimbushealthcaretest.com` |
| **Production** | `https://feapi.novamed.care` |

---

## Development Environment

Use the development environment for testing your integration.

**Base URL**: `https://novamed-feapidev.nimbushealthcaretest.com`

### Features

- **Test data**: Pre-configured test clinics, practitioners, patients, and medications
- **No real orders**: Orders created in development don't result in actual shipments
- **Full API access**: All endpoints available with test data
- **Higher rate limits**: More lenient rate limits for testing

### Important Notes

- ⚠️ **Do not send PHI** (Protected Health Information) to the Development environment
- Development data may be reset periodically
- Use provided test IDs for testing

### Getting Started with Development

1. Obtain your development API key from NovaMed
2. Use the development base URL in all requests
3. Start testing with your assigned clinic ID

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/practitioner \
  -H "x-api-key: your-dev-api-key" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "npi_number": "1234567890",
    "first_name": "Test",
    "last_name": "Provider",
    ...
  }'
```

---

## Production Environment

Use the production environment for live integrations with real patient data.

**Base URL**: `https://feapi.novamed.care`

### Features

- **Real data**: All operations affect real patients and orders
- **Actual shipments**: Orders result in real pharmacy fulfillment
- **Stricter validation**: More rigorous validation and compliance checks
- **Standard rate limits**: Production rate limits apply

### Production Requirements

Before accessing production:

1. ✅ Complete Business Associate Agreement (BAA) with Nimbus Healthcare
2. ✅ Pass integration review with NovaMed team
3. ✅ Complete testing in development environment
4. ✅ Obtain production API credentials

```bash
curl -X POST https://feapi.novamed.care/api/external/practitioner \
  -H "x-api-key: your-prod-api-key" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "npi_number": "1234567890",
    "first_name": "Sarah",
    "last_name": "Johnson",
    ...
  }'
```

---

## API Keys

API keys are environment-specific and tied to your clinic:

| Environment | Key Format | Usage |
|-------------|------------|-------|
| Development | `dev_xxxxx` | Testing only |
| Production | `prod_xxxxx` | Live operations |

### Key Management

- **Never mix environments**: Development keys only work with the development URL
- **Keep keys secure**: Store in environment variables, never in code
- **Rotate regularly**: Contact NovaMed to rotate compromised keys

---

## Rate Limits

Rate limits vary by environment:

### Development

| Endpoint | Limit |
|----------|-------|
| All endpoints | 100 requests/minute |

### Production

| Endpoint | Limit |
|----------|-------|
| All endpoints | 60 requests/minute |

### Rate Limit Headers

All responses include rate limit information:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 55
X-RateLimit-Reset: 1705320000
```

### Rate Limit Exceeded

When you exceed rate limits, you'll receive a `429 Too Many Requests` response:

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Please retry after 60 seconds.",
    "retry_after": 60
  }
}
```

---

## Webhooks by Environment

Configure webhook URLs separately for each environment:

| Environment | Webhook Delivery |
|-------------|------------------|
| Development | Sends to your development webhook URL |
| Production | Sends to your production webhook URL |

Register webhooks for each environment:

```bash
# Development webhook
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
  -H "x-api-key: your-dev-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "your-clinic-uuid",
    "webhook_url": "https://your-dev-server.com/webhooks/novamed"
  }'

# Production webhook
curl -X POST https://feapi.novamed.care/api/external/webhook \
  -H "x-api-key: your-prod-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "your-clinic-uuid",
    "webhook_url": "https://your-prod-server.com/webhooks/novamed"
  }'
```

---

## Migration Checklist

Before moving from Development to Production:

### Pre-Production Checklist

- [ ] Complete all API integration testing in development
- [ ] Verify error handling for all error codes
- [ ] Test webhook delivery and processing
- [ ] Implement idempotency for all write operations
- [ ] Set up production webhook endpoints
- [ ] Complete BAA with Nimbus Healthcare
- [ ] Pass integration review with NovaMed team
- [ ] Obtain production API credentials
- [ ] Update application configuration with production URLs

### Go-Live Checklist

- [ ] Configure production API keys securely
- [ ] Register production webhook URLs
- [ ] Start with low-volume testing
- [ ] Monitor error rates and response times
- [ ] Verify webhook delivery in production
- [ ] Gradually increase traffic

---

## Environment Comparison

| Feature | Development | Production |
|---------|-------------|------------|
| **Base URL** | `novamed-feapidev.nimbushealthcaretest.com` | `feapi.novamed.care` |
| **Data** | Test data | Real patient data |
| **Orders** | No real shipments | Real shipments |
| **PHI** | Not allowed | Allowed (with BAA) |
| **Rate Limits** | 100 req/min | 60 req/min |
| **Validation** | Basic | Strict |
| **BAA Required** | No | Yes |

---

## Best Practices

1. **Always test in development first** - Never test new features in production
2. **Use environment variables** - Store API keys and base URLs in environment variables
3. **Separate configurations** - Maintain separate config files for each environment
4. **Monitor production** - Set up alerting for error rates and API failures
5. **Keep development active** - Use development for ongoing testing and debugging

### Configuration Example

```javascript
// config.js
const config = {
  development: {
    baseUrl: 'https://novamed-feapidev.nimbushealthcaretest.com',
    apiKey: process.env.NOVAMED_DEV_API_KEY,
    webhookUrl: 'https://dev.yourapp.com/webhooks/novamed'
  },
  production: {
    baseUrl: 'https://feapi.novamed.care',
    apiKey: process.env.NOVAMED_PROD_API_KEY,
    webhookUrl: 'https://yourapp.com/webhooks/novamed'
  }
};

const env = process.env.NODE_ENV || 'development';
module.exports = config[env];
```

---

## Next Steps

- [Get started with development](/guides/quickstart)
- [Learn about authentication](/guides/authentication)
- [Set up webhooks](/guides/webhooks)
- [Read the API reference](/api-reference)
