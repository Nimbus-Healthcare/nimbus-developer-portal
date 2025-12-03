---
title: Quickstart
description: Get started with the Nimbus OS API in minutes
---

# Quickstart

Get your first API call working in under 5 minutes.

## Prerequisites

- An API key (get one from the developer portal)
- `curl` or your preferred HTTP client
- Access to the sandbox environment
- Approved Partner status (API access is limited to Approved Partners)

**Note**: By using the API, you agree to the [API License Terms & Conditions](/api-terms).

## Get Your API Key

1. Sign in to the [developer portal](https://developer.nimbus-os.com)
2. Navigate to API Keys
3. Create a new API key for sandbox testing
4. Copy the key securely (you won't be able to view it again)

**Note**: API key generation is currently managed through the developer portal. Contact support if you need assistance.

## Make Your First Request

Let's retrieve an order to verify your setup:

```bash
curl https://api-sandbox.nimbus-os.com/orders/ord_1234567890 \
  -H "X-API-Key: your-api-key-here"
```

If successful, you'll receive a JSON response with order details:

```json
{
  "id": "ord_1234567890",
  "patientId": "pat_1234567890",
  "prescriptionId": "rx_9876543210",
  "status": "processing",
  "createdAt": "2025-01-15T10:30:00Z",
  "updatedAt": "2025-01-15T14:22:00Z"
}
```

## Create Your First Order

Now let's create an order:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: unique-key-123" \
  -d '{
    "patientId": "pat_1234567890",
    "prescriptionId": "rx_9876543210",
    "shippingAddress": {
      "street1": "123 Main St",
      "city": "Austin",
      "state": "TX",
      "zip": "78701",
      "country": "US"
    }
  }'
```

The response includes the created order:

```json
{
  "id": "ord_abc123xyz",
  "patientId": "pat_1234567890",
  "prescriptionId": "rx_9876543210",
  "status": "pending",
  "shippingAddress": {
    "street1": "123 Main St",
    "city": "Austin",
    "state": "TX",
    "zip": "78701",
    "country": "US"
  },
  "createdAt": "2025-01-15T15:00:00Z"
}
```

## Using Idempotency Keys

Notice the `Idempotency-Key` header in the request above. This ensures that if you retry the request (due to a network error, for example), you won't create duplicate orders.

**Best practice**: Always include an idempotency key when creating resources. Use a unique value for each distinct operation.

```bash
# First request
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key" \
  -H "Idempotency-Key: order-2025-01-15-001" \
  -d '{...}'

# Retry with same key - returns original order, no duplicate created
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key" \
  -H "Idempotency-Key: order-2025-01-15-001" \
  -d '{...}'
```

## Next Steps

- [Learn about authentication](/guides/authentication)
- [Explore order management](/guides/orders)
- [Set up webhooks](/guides/webhooks)
- [Read the API reference](/api-reference)

## Common Issues

### 401 Unauthorized

Your API key is missing or invalid. Verify:
- The key is included in the `X-API-Key` header
- The key hasn't been revoked
- You're using the correct environment (sandbox vs production)

### 404 Not Found

The resource doesn't exist. In sandbox, use the test IDs provided in examples.

### 422 Validation Error

The request body is invalid. Check the error response for specific field issues.

## Need Help?

- [API Reference](/api-reference)
- [Error Handling Guide](/guides/errors-idempotency)
- [Contact Support](mailto:api@nimbus-os.com)
