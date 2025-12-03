---
title: Environments
description: Sandbox vs Production environments
---

# Environments

The Nimbus OS API provides two environments: Sandbox for testing and Production for live integrations.

## Sandbox

Use the sandbox environment for development and testing.

**Base URL**: `https://api-sandbox.nimbus-os.com`

### Features

- **Test data**: Pre-configured test patients, prescriptions, and orders
- **No real orders**: Orders created in sandbox don't result in actual shipments
- **Full API access**: All endpoints available with test data
- **Rate limits**: Higher rate limits for testing

**Important**: Do not send PHI (Protected Health Information) to the Sandbox environment. PHI is not recommended and should not be transmitted to Sandbox. See [API Terms & Conditions](/api-terms) for details.

### Test Data

Sandbox includes test data you can use:

- **Patients**: `pat_1234567890`, `pat_test_001`, etc.
- **Prescriptions**: `rx_9876543210`, `rx_test_001`, etc.
- **Orders**: Create orders with test patient/prescription IDs

### Getting Started

1. Create a sandbox API key in the developer portal
2. Use the sandbox base URL
3. Start testing with provided test data

## Production

Use the production environment for live integrations.

**Base URL**: `https://api.nimbus-os.com`

### Features

- **Real data**: All operations affect real patients and orders
- **Actual shipments**: Orders result in real pharmacy fulfillment
- **Stricter validation**: More rigorous validation and checks
- **Production rate limits**: Standard rate limits apply

### Migration Checklist

Before going to production:

- [ ] Test thoroughly in sandbox
- [ ] Verify error handling
- [ ] Set up webhook endpoints
- [ ] Configure production API keys
- [ ] Update base URLs
- [ ] Test with real data (carefully)
- [ ] Monitor initial requests

## Base URLs

| Environment | Base URL |
|------------|----------|
| Sandbox | `https://api-sandbox.nimbus-os.com` |
| Production | `https://api.nimbus-os.com` |

## API Keys

API keys are environment-specific:

- **Sandbox keys**: Only work with sandbox base URL
- **Production keys**: Only work with production base URL

Create separate keys for each environment in the developer portal.

## Rate Limits

Rate limits vary by environment and endpoint:

### Sandbox

- **Orders**: 100 requests/minute
- **Refills**: 100 requests/minute
- **Webhooks**: 50 requests/minute

### Production

- **Orders**: 60 requests/minute
- **Refills**: 60 requests/minute
- **Webhooks**: 30 requests/minute

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 59
X-RateLimit-Reset: 1705320000
```

### Rate Limit Exceeded

If you exceed rate limits:

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Rate limit exceeded",
    "requestId": "req_abc123",
    "details": {
      "retryAfter": 60
    }
  }
}
```

Wait for the `retryAfter` seconds before retrying.

## Test Data

### Sandbox Test IDs

Use these test IDs in sandbox:

**Patients**:
- `pat_1234567890`
- `pat_test_001`
- `pat_test_002`

**Prescriptions**:
- `rx_9876543210`
- `rx_test_001`
- `rx_test_002`

**Note**: Test IDs may change. Check the developer portal for current test data.

### Production Data

In production, use real patient and prescription IDs from your system.

## Webhooks

Webhook URLs are configured per environment:

- **Sandbox webhooks**: Use sandbox webhook URLs
- **Production webhooks**: Use production webhook URLs

Configure webhook URLs separately for each environment in the developer portal.

## Migration Guide

### Step 1: Complete Sandbox Testing

- Test all endpoints
- Verify error handling
- Test webhook delivery
- Validate idempotency

### Step 2: Prepare Production

- Create production API keys
- Set up production webhook endpoints
- Update configuration with production base URL
- Review production rate limits

### Step 3: Gradual Rollout

- Start with low-volume testing
- Monitor error rates
- Verify webhook delivery
- Gradually increase volume

### Step 4: Monitor

- Watch error rates
- Monitor webhook delivery
- Track API response times
- Review logs regularly

## Differences Summary

| Feature | Sandbox | Production |
|---------|---------|-------------|
| Base URL | `api-sandbox.nimbus-os.com` | `api.nimbus-os.com` |
| Data | Test data | Real data |
| Orders | No real shipments | Real shipments |
| Rate Limits | Higher | Standard |
| Validation | Basic | Strict |
| Webhooks | Test endpoints | Production endpoints |

## Best Practices

1. **Test in sandbox first**: Always test new features in sandbox
2. **Use separate keys**: Never mix sandbox and production keys
3. **Monitor production**: Watch error rates and webhook delivery
4. **Gradual rollout**: Start with low volume in production
5. **Keep sandbox active**: Use sandbox for ongoing testing

## Next Steps

- [Get started with sandbox](/guides/quickstart)
- [Learn about authentication](/guides/authentication)
- [Read the API reference](/api-reference)
