---
title: Webhooks
description: Receive real-time event notifications
---

# Webhooks

Receive real-time notifications when events occur in your integration.

## Overview

Webhooks deliver event notifications to your server via HTTP POST requests. This eliminates the need to poll the API for status changes.

## Setting Up Webhooks

1. **Create an endpoint**: Set up an HTTPS endpoint on your server
2. **Configure webhook URL**: Add your endpoint URL in the developer portal
3. **Verify signature**: Validate webhook signatures (see below)
4. **Handle events**: Process incoming webhook events

## Webhook URL Requirements

Your webhook endpoint must:

- Use HTTPS (required for security)
- Accept POST requests
- Return a 2xx status code within 10 seconds
- Be publicly accessible (no localhost)

## Event Types

### Order Events

- `order.created` - New order created
- `order.fulfilled` - Order shipped (includes tracking number)

### Refill Events

- `refill.ready` - Refill approved and ready for processing

## Event Payload

All webhook events follow this structure:

```json
{
  "id": "evt_1234567890",
  "type": "order.created",
  "createdAt": "2025-01-15T10:30:00Z",
  "data": {
    "orderId": "ord_abc123xyz",
    "patientId": "pat_1234567890",
    "status": "pending"
  }
}
```

### Order Created Event

```json
{
  "id": "evt_1234567890",
  "type": "order.created",
  "createdAt": "2025-01-15T10:30:00Z",
  "data": {
    "orderId": "ord_abc123xyz",
    "patientId": "pat_1234567890",
    "prescriptionId": "rx_9876543210",
    "status": "pending"
  }
}
```

### Order Fulfilled Event

```json
{
  "id": "evt_1234567891",
  "type": "order.fulfilled",
  "createdAt": "2025-01-15T14:22:00Z",
  "data": {
    "orderId": "ord_abc123xyz",
    "trackingNumber": "1Z999AA10123456784",
    "estimatedDeliveryDate": "2025-01-18"
  }
}
```

### Refill Ready Event

```json
{
  "id": "evt_1234567892",
  "type": "refill.ready",
  "createdAt": "2025-01-15T10:30:00Z",
  "data": {
    "refillId": "ref_abc123xyz",
    "prescriptionId": "rx_9876543210",
    "status": "approved"
  }
}
```

## Webhook Signing

Webhooks are signed to verify authenticity. The signature is included in the `X-Webhook-Signature` header.

**Note**: Webhook signing implementation details will be provided. For now, webhooks include a signature header that should be validated.

### Signature Verification

```python
import hmac
import hashlib

def verify_webhook_signature(payload, signature, secret):
    expected_signature = hmac.new(
        secret.encode('utf-8'),
        payload.encode('utf-8'),
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(expected_signature, signature)
```

**TODO**: Update with actual signing method once implemented.

## Retry Logic

If your endpoint doesn't respond with a 2xx status code, we'll retry:

- **Immediate retry**: After 1 second
- **Exponential backoff**: 2 seconds, 4 seconds, 8 seconds, etc.
- **Maximum retries**: 5 attempts over ~24 hours
- **Dead letter**: Failed webhooks are logged for manual review

### Handling Retries

Your endpoint should be idempotent. Process events based on the event `id` to avoid duplicate processing:

```python
def handle_webhook(event):
    # Check if event already processed
    if event_already_processed(event['id']):
        return {'status': 'ok'}  # Already handled
    
    # Process event
    process_event(event)
    
    # Mark as processed
    mark_event_processed(event['id'])
    
    return {'status': 'ok'}
```

## Testing Webhooks

Use the test endpoint to verify your webhook setup:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/webhooks/test \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "eventType": "order.created",
    "webhookUrl": "https://your-server.com/webhooks"
  }'
```

This sends a test event to your endpoint without creating a real order.

## Endpoint Implementation

### Example Endpoint (Node.js)

```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.post('/webhooks', (req, res) => {
  const event = req.body;
  
  // Verify signature (implement actual verification)
  // const signature = req.headers['x-webhook-signature'];
  // if (!verifySignature(req.body, signature)) {
  //   return res.status(401).send('Invalid signature');
  // }
  
  // Handle event
  switch (event.type) {
    case 'order.created':
      handleOrderCreated(event.data);
      break;
    case 'order.fulfilled':
      handleOrderFulfilled(event.data);
      break;
    case 'refill.ready':
      handleRefillReady(event.data);
      break;
  }
  
  // Always return 2xx quickly
  res.status(200).json({ received: true });
});

function handleOrderCreated(data) {
  console.log('Order created:', data.orderId);
  // Update your system
}

function handleOrderFulfilled(data) {
  console.log('Order fulfilled:', data.orderId);
  console.log('Tracking:', data.trackingNumber);
  // Update tracking in your system
}

function handleRefillReady(data) {
  console.log('Refill ready:', data.refillId);
  // Process refill
}

app.listen(3000);
```

## Best Practices

1. **Respond quickly**: Return a 2xx status within 10 seconds
2. **Process asynchronously**: Queue events for background processing
3. **Verify signatures**: Always validate webhook signatures
4. **Handle idempotency**: Use event IDs to prevent duplicate processing
5. **Log events**: Keep logs of all received webhooks
6. **Monitor failures**: Alert on webhook delivery failures

## Security

- **Use HTTPS**: Webhook endpoints must use HTTPS
- **Verify signatures**: Always validate webhook signatures
- **Validate events**: Check event structure before processing
- **Rate limiting**: Implement rate limiting on your endpoint

## Next Steps

- [Learn about orders](/guides/orders)
- [Learn about refills](/guides/refills)
- [Read the API reference](/api-reference#tag/Webhooks)
