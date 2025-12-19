---
title: Webhook Integration Guide
description: Complete guide to integrating webhooks for real-time NovaMed event notifications
---

# Webhook Integration Guide

Webhooks enable you to receive real-time notifications about NovaMed operations, including order status updates, shipment notifications, and medication request changes. This guide will help you set up and integrate webhooks into your system.

---

## Overview

### What Are Webhooks?

Webhooks are HTTP callbacks that allow NovaMed to notify your system when specific events occur. Instead of polling the API for updates, you register a webhook endpoint, and we send POST requests to your endpoint whenever events happen.

### Benefits

- ✅ **Real-time updates** - Receive notifications immediately when events occur
- ✅ **Efficient** - No need to poll the API repeatedly
- ✅ **Reliable** - Built-in retry mechanisms for failed deliveries
- ✅ **Scalable** - Handles high-volume event streams efficiently

---

## Base URLs

| Environment | URL |
|-------------|-----|
| **Development** | `https://novamed-feapidev.nimbushealthcaretest.com` |
| **Production** | `https://feapi.novamed.care` |

---

## Getting Started

### Step 1: Register Your Webhook Endpoint

Register your webhook URL with NovaMed:

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "webhook_url": "https://your-domain.com/webhooks/novamed"
  }'
```

**Response**:
```json
{
  "success": true,
  "data": {
    "webhook_id": "660e8400-e29b-41d4-a716-446655440001",
    "webhook_url": "https://your-domain.com/webhooks/novamed",
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "created_at": "2025-01-15T10:00:00.000Z"
  },
  "message": "Webhook registered successfully"
}
```

### Step 2: Set Up Your Webhook Endpoint

Your webhook endpoint must:
- Accept HTTPS POST requests
- Return HTTP 200 OK quickly (process asynchronously)
- Verify the `x-api-key` header
- Handle duplicate events (idempotency)

---

## Webhook Events

NovaMed sends the following webhook events:

### Order Status Events

| Event | Description |
|-------|-------------|
| `order:created` | New order has been created |
| `order:processing` | Order is being processed |
| `order:shipped` | Order has been shipped |
| `order:delivered` | Order has been delivered |
| `order:cancelled` | Order has been cancelled |

### Medication Request Events

| Event | Description |
|-------|-------------|
| `medication_request:created` | New medication request created |
| `medication_request:approved` | Medication request approved |
| `medication_request:filled` | Medication has been filled |
| `medication_request:cancelled` | Medication request cancelled |

### Refill Events

| Event | Description |
|-------|-------------|
| `refill:requested` | Refill request submitted |
| `refill:approved` | Refill request approved |
| `refill:denied` | Refill request denied |

---

## Webhook Payload Structure

All webhook events follow this structure:

```json
{
  "event_type": "order:shipped",
  "event_id": "evt_abc123xyz",
  "timestamp": "2025-01-15T10:30:00.000Z",
  "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
  "data": {
    // Event-specific data
  }
}
```

### Order Shipped Event Example

```json
{
  "event_type": "order:shipped",
  "event_id": "evt_abc123xyz",
  "timestamp": "2025-01-15T10:30:00.000Z",
  "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
  "data": {
    "order_id": "ord_12345",
    "patient_id": "pat_67890",
    "medication_request_id": "med_req_11111",
    "tracking_number": "1Z999AA10123456784",
    "carrier": "UPS",
    "estimated_delivery": "2025-01-18",
    "shipping_address": {
      "line1": "123 Main Street",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94102"
    }
  }
}
```

### Medication Request Approved Event Example

```json
{
  "event_type": "medication_request:approved",
  "event_id": "evt_def456xyz",
  "timestamp": "2025-01-15T09:00:00.000Z",
  "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
  "data": {
    "medication_request_id": "med_req_11111",
    "patient_id": "pat_67890",
    "practitioner_id": "prac_22222",
    "medication_name": "Testosterone Cypionate",
    "quantity": 1,
    "refills": 3,
    "approved_at": "2025-01-15T09:00:00.000Z"
  }
}
```

---

## Implementation Examples

### Node.js/Express Example

```javascript
const express = require('express');
const app = express();

app.use(express.json());

// Webhook endpoint
app.post('/webhooks/novamed', async (req, res) => {
  // 1. Verify API key
  const apiKey = req.headers['x-api-key'];
  if (apiKey !== process.env.NOVAMED_WEBHOOK_SECRET) {
    return res.status(401).json({ error: 'Unauthorized' });
  }

  // 2. Return 200 immediately
  res.status(200).json({ received: true });

  // 3. Process event asynchronously
  try {
    await processWebhookEvent(req.body);
  } catch (error) {
    console.error('Error processing webhook:', error);
  }
});

async function processWebhookEvent(payload) {
  const { event_type, data, event_id } = payload;

  // Check for duplicate events
  if (await isEventProcessed(event_id)) {
    console.log('Duplicate event, skipping:', event_id);
    return;
  }

  switch (event_type) {
    case 'order:shipped':
      await handleOrderShipped(data);
      break;
    case 'order:delivered':
      await handleOrderDelivered(data);
      break;
    case 'medication_request:approved':
      await handleMedicationApproved(data);
      break;
    case 'refill:approved':
      await handleRefillApproved(data);
      break;
    default:
      console.log('Unknown event:', event_type);
  }

  // Mark event as processed
  await markEventProcessed(event_id);
}

async function handleOrderShipped(data) {
  console.log('Order shipped:', data.order_id);
  // Update your system with tracking information
  // Send notification to patient
}

async function handleOrderDelivered(data) {
  console.log('Order delivered:', data.order_id);
  // Update order status in your database
}

async function handleMedicationApproved(data) {
  console.log('Medication approved:', data.medication_request_id);
  // Trigger next steps in your workflow
}

async function handleRefillApproved(data) {
  console.log('Refill approved:', data.refill_id);
  // Process refill order
}

app.listen(3000, () => {
  console.log('Webhook server listening on port 3000');
});
```

### Python/Flask Example

```python
from flask import Flask, request, jsonify
import os
import threading

app = Flask(__name__)

@app.route('/webhooks/novamed', methods=['POST'])
def webhook_handler():
    # 1. Verify API key
    api_key = request.headers.get('x-api-key')
    if api_key != os.getenv('NOVAMED_WEBHOOK_SECRET'):
        return jsonify({'error': 'Unauthorized'}), 401
    
    # 2. Return 200 immediately
    payload = request.get_json()
    
    # 3. Process event asynchronously
    thread = threading.Thread(target=process_webhook_event, args=(payload,))
    thread.start()
    
    return jsonify({'received': True}), 200

def process_webhook_event(payload):
    event_type = payload.get('event_type')
    data = payload.get('data')
    event_id = payload.get('event_id')
    
    handlers = {
        'order:shipped': handle_order_shipped,
        'order:delivered': handle_order_delivered,
        'medication_request:approved': handle_medication_approved,
        'refill:approved': handle_refill_approved,
    }
    
    handler = handlers.get(event_type)
    if handler:
        handler(data)
    else:
        print(f'Unknown event: {event_type}')

def handle_order_shipped(data):
    print(f"Order shipped: {data.get('order_id')}")
    # Update your system with tracking information

def handle_order_delivered(data):
    print(f"Order delivered: {data.get('order_id')}")
    # Update order status

def handle_medication_approved(data):
    print(f"Medication approved: {data.get('medication_request_id')}")
    # Trigger workflow

def handle_refill_approved(data):
    print(f"Refill approved: {data.get('refill_id')}")
    # Process refill

if __name__ == '__main__':
    app.run(port=3000)
```

---

## Managing Webhooks

### Delete a Webhook

To remove a registered webhook:

```bash
curl -X DELETE https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "webhook_id": "660e8400-e29b-41d4-a716-446655440001"
  }'
```

**Response**:
```json
{
  "success": true,
  "message": "Webhook deleted successfully"
}
```

---

## Security Best Practices

### 1. Verify the API Key

Always verify the `x-api-key` header matches your webhook secret:

```javascript
const apiKey = req.headers['x-api-key'];
if (apiKey !== process.env.NOVAMED_WEBHOOK_SECRET) {
  return res.status(401).json({ error: 'Unauthorized' });
}
```

### 2. Use HTTPS Only

Webhook endpoints **must** use HTTPS. NovaMed will not send webhooks to HTTP endpoints.

### 3. Implement Idempotency

Handle duplicate events gracefully using the `event_id`:

```javascript
const processedEvents = new Map();

async function isEventProcessed(eventId) {
  return processedEvents.has(eventId);
}

async function markEventProcessed(eventId) {
  processedEvents.set(eventId, Date.now());
  // Also persist to database for reliability
}
```

### 4. Return 200 OK Quickly

Your endpoint should return HTTP 200 OK within **5 seconds**. Process events asynchronously:

```javascript
app.post('/webhooks/novamed', (req, res) => {
  // Return immediately
  res.status(200).json({ received: true });
  
  // Process asynchronously
  processWebhookEvent(req.body).catch(console.error);
});
```

---

## Error Handling & Retries

### Retry Policy

NovaMed will retry failed webhook deliveries:

| Attempt | Delay |
|---------|-------|
| 1 | Immediate |
| 2 | 1 minute |
| 3 | 5 minutes |
| 4 | 15 minutes |
| 5 | 1 hour |

After 5 failed attempts, the webhook delivery is marked as failed.

### Handling Failures

If your endpoint returns a non-200 status code, the webhook will be retried. To prevent retries:

1. **Return 200 OK** even if processing fails
2. **Log errors** for later processing
3. **Process asynchronously** to avoid timeouts

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

3. **Register webhook** with the ngrok URL:
   ```bash
   curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
     -H "x-api-key: your-api-key" \
     -H "Content-Type: application/json" \
     -d '{
       "clinic_id": "your-clinic-uuid",
       "webhook_url": "https://abc123.ngrok.io/webhooks/novamed"
     }'
   ```

4. **Trigger a test event** by creating an order in the sandbox environment

5. **Monitor ngrok requests** at `http://localhost:4040`

---

## Troubleshooting

### Webhooks Not Received

- ✅ Check webhook URL is registered correctly
- ✅ Verify endpoint is accessible (not behind firewall)
- ✅ Ensure endpoint uses HTTPS
- ✅ Check API key is correct

### Duplicate Events

- ✅ Implement idempotency checking using `event_id`
- ✅ Store processed event IDs in database

### Timeout Errors

- ✅ Return 200 OK quickly (< 5 seconds)
- ✅ Process events asynchronously
- ✅ Optimize database queries

---

## Best Practices Summary

1. ✅ **Use HTTPS** - Required for webhook endpoints
2. ✅ **Verify API Key** - Always check `x-api-key` header
3. ✅ **Return 200 OK Quickly** - Within 5 seconds
4. ✅ **Process Asynchronously** - Don't block the response
5. ✅ **Implement Idempotency** - Handle duplicate events using `event_id`
6. ✅ **Log Everything** - For debugging and monitoring
7. ✅ **Handle Errors Gracefully** - Don't crash on errors

---

## Next Steps

1. [Register your webhook endpoint](#step-1-register-your-webhook-endpoint)
2. [Implement webhook handler](#implementation-examples)
3. [Test with development environment](#testing-webhooks)
4. Deploy to production

## Support

- **API Support**: api@nimbus-os.com
- **Development Environment**: `https://novamed-feapidev.nimbushealthcaretest.com`
- **Production Environment**: `https://feapi.novamed.care`

For more information, see the [API Reference](/api-reference).
