---
title: Webhook Integration Guide
description: Complete guide to integrating webhooks for real-time pharmacy event notifications
---

# Webhook Integration Guide

Webhooks enable you to receive real-time notifications about pharmacy operations, including shipment creation, prescription status updates, and medication order changes. This guide will help you set up and integrate webhooks into your system.

---

## Overview

### What Are Webhooks?

Webhooks are HTTP callbacks that allow Nimbus OS to notify your system when specific events occur. Instead of polling our API for updates, you register a webhook endpoint, and we send POST requests to your endpoint whenever events happen.

### Benefits

- ✅ **Real-time updates** - Receive notifications immediately when events occur
- ✅ **Efficient** - No need to poll our API repeatedly
- ✅ **Reliable** - Built-in retry mechanisms for failed deliveries
- ✅ **Scalable** - Handles high-volume event streams efficiently

---

## Webhook Events

Nimbus OS sends the following webhook events:

### 1. Shipment Created
**Event**: `shipment:created`  
**Triggered**: When a prescription shipment is created and ready to ship

### 2. Shipment Cancelled
**Event**: `shipment:cancelled`  
**Triggered**: When a shipment is cancelled

### 3. Medication Updated
**Event**: `medication:updated`  
**Triggered**: When medication order status changes (validated, submitted, filled, shipped, delivered, cancelled)

---

## Getting Started

### Step 1: Register Your Webhook Endpoint

First, register your webhook URL with Nimbus OS:

```bash
curl -X POST https://api.nimbus-os.com/api/external/webhook \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "your-clinic-uuid",
    "webhook_url": "https://your-domain.com/webhooks/nimbus"
  }'
```

**Response**:
```json
{
  "success": true,
  "data": {
    "webhook_id": "660e8400-e29b-41d4-a716-446655440001"
  },
  "message": "Webhook created successfully"
}
```

### Step 2: Set Up Your Webhook Endpoint

Your webhook endpoint must:
- Accept HTTPS POST requests
- Return HTTP 200 OK quickly (process asynchronously)
- Verify the `X-API-Key` header
- Handle duplicate events (idempotency)

---

## Webhook Payload Structure

All webhook events follow this structure:

```json
{
  "event_name": "shipment:created",
  "event_data": {
    // Event-specific data
  }
}
```

### Shipment Created Event

```json
{
  "event_name": "shipment:created",
  "event_data": {
    "shipment_id": "550e8400-e29b-41d4-a716-446655440000",
    "medication_orders": [
      "660e8400-e29b-41d4-a716-446655440001",
      "770e8400-e29b-41d4-a716-446655440002"
    ],
    "shipment_status": "pending",
    "shipped_at": "2024-01-15T10:00:00.000Z",
    "tracking_number": "1Z999AA10123456784",
    "tracking_url": "https://tracking.example.com/1Z999AA10123456784",
    "shipping_partner": "FedEx",
    "delivery_method": "next-day",
    "delivery_location": {
      "address": "456 Oak Ave",
      "city": "Austin",
      "state": "TX",
      "postal_code": "78702"
    },
    "notes": "Signature required",
    "barcode_url": "https://example.com/barcode.png",
    "shipping_label": "https://example.com/label.pdf"
  }
}
```

### Shipment Cancelled Event

```json
{
  "event_name": "shipment:cancelled",
  "event_data": {
    "shipment_id": "550e8400-e29b-41d4-a716-446655440000",
    "medication_orders": [
      "660e8400-e29b-41d4-a716-446655440001"
    ],
    "cancellation_reason": "Patient request",
    "cancelled_at": "2024-01-15T11:00:00.000Z"
  }
}
```

### Medication Updated Event

```json
{
  "event_name": "medication:updated",
 ication:updated",
  "event_data": {
    "medication_order_id": "880e8400-e29b-41d4-a716-446655440003",
    "prescription_id": "990e8400-e29b-41d4-a716-446655440004",
    "medication_request_id": "aa0e8400-e29b-41d4-a716-446655440005",
    "status": "shipped",
    "previous_status": "filled",
    "updated_at": "2024-01-15T10:30:00.000Z",
    "pharmetika_status": "shipped",
    "tracking_number": "1Z999AA10123456784",
    "notes": "Out for delivery"
  }
}
```

**Possible Status Values**:
- `validated` - Order validated successfully
- `submitted` - Order submitted to pharmacy
- `filled` - Medication filled
- `shipped` - Medication shipped
- `delivered` - Medication delivered
- `cancelled` - Medication cancelled

---

## Implementation Examples

### Node.js/Express Example

```javascript
const express = require('express');
const app = express();

app.use(express.json());

// Webhook endpoint
app.post('/webhooks/nimbus', async (req, res) => {
  // 1. Verify API key
  const apiKey = req.headers['x-api-key'];
  if (apiKey !== process.env.NIMBUS_API_KEY) {
    return res.status(401).json({ error: 'Unauthorized' });
  }

  // 2. Return 200 immediately
  res.status(200).json({ received: true });

  // 3. Process event asynchronously
  try {
    await processWebhookEvent(req.body);
  } catch (error) {
    console.error('Error processing webhook:', error);
    // Log error for retry handling
  }
});

async function processWebhookEvent(payload) {
  const { event_name, event_data } = payload;

  switch (event_name) {
    case 'shipment:created':
      await handleShipmentCreated(event_data);
      break;
    case 'shipment:cancelled':
      await handleShipmentCancelled(event_data);
      break;
    case 'medication:updated':
      await handleMedicationUpdated(event_data);
      break;
    default:
      console.log('Unknown event:', event_name);
  }
}

async function handleShipmentCreated(data) {
  console.log('Shipment created:', data.shipment_id);
  // Update your system with shipment information
  // Send notification to patient
  // Update order status in your database
}

async function handleShipmentCancelled(data) {
  console.log('Shipment cancelled:', data.shipment_id);
  // Update your system
  // Notify patient of cancellation
}

async function handleMedicationUpdated(data) {
  console.log('Medication updated:', data.medication_order_id, data.status);
  // Update medication status in your system
  // Trigger status-specific workflows
}

app.listen(3000, () => {
  console.log('Webhook server listening on port 3000');
});
```

### Python/Flask Example

```python
from flask import Flask, request, jsonify
import os
import asyncio
from typing import Dict, Any

app = Flask(__name__)

@app.route('/webhooks/nimbus', methods=['POST'])
def webhook_handler():
    # 1. Verify API key
    api_key = request.headers.get('X-API-Key')
    if api_key != os.getenv('NIMBUS_API_KEY'):
        return jsonify({'error': 'Unauthorized'}), 401
    
    # 2. Return 200 immediately
    payload = request.get_json()
    
    # 3. Process event asynchronously
    asyncio.create_task(process_webhook_event(payload))
    
    return jsonify({'received': True}), 200

async def process_webhook_event(payload: Dict[str, Any]):
    event_name = payload.get('event_name')
    event_data = payload.get('event_data')
    
    if event_name == 'shipment:created':
        await handle_shipment_created(event_data)
    elif event_name == 'shipment:cancelled':
        await handle_shipment_cancelled(event_data)
    elif event_name == 'medication:updated':
        await handle_medication_updated(event_data)
    else:
        print(f'Unknown event: {event_name}')

async def handle_shipment_created(data: Dict[str, Any]):
    shipment_id = data.get('shipment_id')
    print(f'Shipment created: {shipment_id}')
    # Update your system with shipment information

async def handle_shipment_cancelled(data: Dict[str, Any]):
    shipment_id = data.get('shipment_id')
    print(f'Shipment cancelled: {shipment_id}')
    # Update your system

async def handle_medication_updated(data: Dict[str, Any]):
    medication_order_id = data.get('medication_order_id')
    status = data.get('status')
    print(f'Medication updated: {medication_order_id} - {status}')
    # Update medication status in your system

if __name__ == '__main__':
    app.run(port=3000)
```

### cURL Testing Example

Test your webhook endpoint locally using ngrok:

```bash
# 1. Start your webhook server locally
node webhook-server.js

# 2. Expose it via ngrok (in another terminal)
ngrok http 3000

# 3. Register the ngrok URL as your webhook
curl -X POST https://api-sandbox.nimbus-os.com/api/external/webhook \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "your-clinic-uuid",
    "webhook_url": "https://your-ngrok-url.ngrok.io/webhooks/nimbus"
  }'
```

---

## Security Best Practices

### 1. Verify API Key

Always verify the `X-API-Key` header matches your API key:

```javascript
const apiKey = req.headers['x-api-key'];
if (apiKey !== process.env.NIMBUS_API_KEY) {
  return res.status(401).json({ error: 'Unauthorized' });
}
```

### 2. Use HTTPS Only

Webhook endpoints **must** use HTTPS. Nimbus OS will not send webhooks to HTTP endpoints.

### 3. Implement Idempotency

Handle duplicate events gracefully:

```javascript
const processedEvents = new Set();

async function processWebhookEvent(payload) {
  // Use shipment_id or medication_order_id as idempotency key
  const idempotencyKey = payload.event_data.shipment_id || 
                        payload.event_data.medication_order_id;
  
  if (processedEvents.has(idempotencyKey)) {
    console.log('Duplicate event, skipping');
    return;
  }
  
  processedEvents.add(idempotencyKey);
  
  // Process event...
  
  // Store in database for persistence across restarts
  await saveProcessedEvent(idempotencyKey);
}
```

### 4. Return 200 OK Quickly

Your endpoint should return HTTP 200 OK within **5 seconds**. Process events asynchronously:

```javascript
app.post('/webhooks/nimbus', (req, res) => {
  // Return immediately
  res.status(200).json({ received: true });
  
  // Process asynchronously
  processWebhookEvent(req.body).catch(console.error);
});
```

### 5. Handle Errors Gracefully

Log errors but don't crash your server:

```javascript
async function processWebhookEvent(payload) {
  try {
    // Process event
  } catch (error) {
    console.error('Error processing webhook:', error);
    // Log to error tracking service (Sentry, etc.)
    // Don't throw - let the webhook succeed
  }
}
```

---

## Idempotency

### Why Idempotency Matters

Webhooks may be delivered multiple times due to:
- Network retries
- Server restarts
- Duplicate deliveries

### Implementation Strategy

**Option 1: In-Memory Set (Simple)**
```javascript
const processedEvents = new Set();

function isProcessed(idempotencyKey) {
  return processedEvents.has(idempotencyKey);
}

function markProcessed(idempotencyKey) {
  processedEvents.add(idempotencyKey);
}
```

**Option 2: Database (Recommended)**
```javascript
async function isProcessed(idempotencyKey) {
  const event = await db.webhookEvents.findOne({
    where: { idempotency_key: idempotencyKey }
  });
  return !!event;
}

async function markProcessed(idempotencyKey) {
  await db.webhookEvents.create({
    idempotency_key: idempotencyKey,
    processed_at: new Date()
  });
}
```

**Option 3: Redis (High Volume)**
```javascript
const redis = require('redis');
const client = redis.createClient();

async function isProcessed(idempotencyKey) {
  const exists = await client.exists(`webhook:${idempotencyKey}`);
  return exists === 1;
}

async function markProcessed(idempotencyKey) {
  await client.setex(`webhook:${idempotencyKey}`, 86400, '1'); // 24 hours
}
```

---

## Error Handling & Retries

### Webhook Delivery Retries

Nimbus OS will retry failed webhook deliveries:
- **Initial attempt**: Immediate
- **Retry 1**: After 1 minute
- **Retry 2**: After 5 minutes
- **Retry 3**: After 15 minutes
- **Retry 4**: After 1 hour

### Handling Failures

If your endpoint returns a non-200 status code, the webhook will be retried. To prevent retries:

1. **Return 200 OK** even if processing fails
2. **Log errors** for later processing
3. **Process asynchronously** to avoid timeouts

```javascript
app.post('/webhooks/nimbus', (req, res) => {
  // Always return 200 OK
  res.status(200).json({ received: true });
  
  // Process in background
  processWebhookEvent(req.body)
    .catch(error => {
      // Log error for retry queue
      errorQueue.add({ payload: req.body, error });
    });
});
```

---

## Testing Webhooks

### Local Testing with ngrok

1. **Start your webhook server**:
   ```bash
   node webhook-server.js
   ```

2. **Expose with ngrok**:
   ```bash
   ngrok http 3000
   ```

3. **Register webhook**:
   ```bash
   curl -X POST https://api-sandbox.nimbus-os.com/api/external/webhook \
     -H "X-API-Key: your-api-key" \
     -H "Content-Type: application/json" \
     -d '{
       "clinic_id": "your-clinic-uuid",
       "webhook_url": "https://abc123.ngrok.io/webhooks/nimbus"
     }'
   ```

4. **Trigger test event** (create a shipment in sandbox)

5. **Monitor ngrok requests**:
   - Visit `http://localhost:4040` to see incoming requests

### Testing with Postman

1. **Set up webhook endpoint** in Postman Mock Server
2. **Register webhook** with mock server URL
3. **Monitor** incoming webhook calls

---

## Monitoring & Debugging

### Logging

Log all webhook events for debugging:

```javascript
app.post('/webhooks/nimbus', (req, res) => {
  console.log('Webhook received:', {
    event: req.body.event_name,
    timestamp: new Date().toISOString(),
    headers: req.headers
  });
  
  res.status(200).json({ received: true });
  processWebhookEvent(req.body);
});
```

### Monitoring

Track webhook processing metrics:
- **Success rate**: Percentage of successfully processed webhooks
- **Processing time**: Time to process each webhook
- **Error rate**: Number of failed webhook processing attempts

### Common Issues

**Issue**: Webhooks not received
- ✅ Check webhook URL is registered correctly
- ✅ Verify endpoint is accessible (not behind firewall)
- ✅ Ensure endpoint uses HTTPS
- ✅ Check API key is correct

**Issue**: Duplicate events
- ✅ Implement idempotency checking
- ✅ Use idempotency keys (shipment_id, medication_order_id)

**Issue**: Timeout errors
- ✅ Return 200 OK quickly (< 5 seconds)
- ✅ Process events asynchronously
- ✅ Optimize database queries

---

## Webhook Management

### List Registered Webhooks

Contact api@nimbus-os.com to get a list of your registered webhooks.

### Update Webhook URL

Delete the old webhook and register a new one:

```bash
# Delete old webhook
curl -X DELETE https://api.nimbus-os.com/api/external/webhook/old-webhook-id \
  -H "X-API-Key: your-api-key"

# Register new webhook
curl -X POST https://api.nimbus-os.com/api/external/webhook \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "your-clinic-uuid",
    "webhook_url": "https://new-url.com/webhooks/nimbus"
  }'
```

### Delete Webhook

```bash
curl -X DELETE https://api.nimbus-os.com/api/external/webhook/webhook-id \
  -H "X-API-Key: your-api-key"
```

---

## Best Practices Summary

1. ✅ **Use HTTPS** - Required for webhook endpoints
2. ✅ **Verify API Key** - Always check `X-API-Key` header
3. ✅ **Return 200 OK Quickly** - Within 5 seconds
4. ✅ **Process Asynchronously** - Don't block the response
5. ✅ **Implement Idempotency** - Handle duplicate events
6. ✅ **Log Everything** - For debugging and monitoring
7. ✅ **Handle Errors Gracefully** - Don't crash on errors
8. ✅ **Monitor Performance** - Track success rates and processing times

---

## Support

- **API Support**: api@nimbus-os.com
- **Documentation**: https://developer.nimbus-os.com
- **Sandbox Environment**: https://api-sandbox.nimbus-os.com

---

## Next Steps

1. ✅ Register your webhook endpoint
2. ✅ Implement webhook handler
3. ✅ Test with sandbox environment
4. ✅ Deploy to production
5. ✅ Monitor webhook delivery

For more information, see the [API Reference](/api-reference) documentation.
