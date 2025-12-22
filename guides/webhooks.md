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

### Medication Order Events

| Event | Description |
|-------|-------------|
| `medication_order:verified` | Medication order has been verified |
| `medication_order:submitted` | Medication order has been submitted |
| `medication_order:acknowledged` | Medication order has been acknowledged |
| `medication_order:filled` | Medication order has been filled |

### Shipment Events

| Event | Description |
|-------|-------------|
| `shipment:created` | Shipment has been created (includes tracking info) |
| `shipment:cancelled` | Shipment has been cancelled |

### Practitioner Events

| Event | Description |
|-------|-------------|
| `practitioner:activated` | Practitioner account has been activated |

---

## Webhook Payload Structure

All webhook events follow this structure:

```json
{
  "event_name": "medication_order:verified",
  "event_data": {
    // Event-specific data
  }
}
```

### Medication Order Event Example

```json
{
  "event_name": "medication_order:verified",
  "event_data": {
    "id": "a7570e3c-4338-485f-9465-ee09793c2d46",
    "clinic_id": "2a7d8da1-2f69-46e1-87a4-04490ab73c41",
    "patient_id": "8456ce26-05db-4fcd-bebd-495bd7bc04df",
    "practitioner_id": "a7570e3c-4338-485f-9465-ee09793c2d46",
    "rx_number": "123456"
  }
}
```

> **Note:** The `rx_number` field is not available for all events.

### Shipment Created Event Example

After a shipment is created, additional fields are included:

```json
{
  "event_name": "shipment:created",
  "event_data": {
    "id": "a7570e3c-4338-485f-9465-ee09793c2d46",
    "clinic_id": "2a7d8da1-2f69-46e1-87a4-04490ab73c41",
    "patient_id": "8456ce26-05db-4fcd-bebd-495bd7bc04df",
    "practitioner_id": "a7570e3c-4338-485f-9465-ee09793c2d46",
    "rx_number": "123456",
    "shipping_label": "https://example.com/shipping-label.pdf",
    "shipment_id": "8456ce26-05db-4fcd-bebd-495bd7bc04df",
    "shipment_provider": "Ameriship",
    "tracking_url": "https://example.com/tracking"
  }
}
```

### Practitioner Activated Event Example

```json
{
  "event_name": "practitioner:activated",
  "event_data": {
    "practitioner_id": "4f407bca-2a9b-4dde-8240-e53331a5a986",
    "practitioner_name": "Dr. John Smith",
    "practitioner_email": "dr.smith@clinic.com",
    "practitioner_phone": "+1-555-0123",
    "practitioner_npi": "1234567890",
    "practitioner_status": "active"
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
  const { event_name, event_data } = payload;

  // Check for duplicate events using medication order ID
  if (event_data.id && await isEventProcessed(event_data.id, event_name)) {
    console.log('Duplicate event, skipping:', event_name, event_data.id);
    return;
  }

  switch (event_name) {
    case 'medication_order:verified':
      await handleMedicationOrderVerified(event_data);
      break;
    case 'medication_order:submitted':
      await handleMedicationOrderSubmitted(event_data);
      break;
    case 'medication_order:acknowledged':
      await handleMedicationOrderAcknowledged(event_data);
      break;
    case 'medication_order:filled':
      await handleMedicationOrderFilled(event_data);
      break;
    case 'shipment:created':
      await handleShipmentCreated(event_data);
      break;
    case 'shipment:cancelled':
      await handleShipmentCancelled(event_data);
      break;
    case 'practitioner:activated':
      await handlePractitionerActivated(event_data);
      break;
    default:
      console.log('Unknown event:', event_name);
  }

  // Mark event as processed
  if (event_data.id) {
    await markEventProcessed(event_data.id, event_name);
  }
}

async function handleMedicationOrderVerified(data) {
  console.log('Medication order verified:', data.id);
  // Update order status in your system
}

async function handleMedicationOrderSubmitted(data) {
  console.log('Medication order submitted:', data.id);
  // Track submission status
}

async function handleMedicationOrderAcknowledged(data) {
  console.log('Medication order acknowledged:', data.id);
  // Update acknowledgment status
}

async function handleMedicationOrderFilled(data) {
  console.log('Medication order filled:', data.id);
  // Process filled order
}

async function handleShipmentCreated(data) {
  console.log('Shipment created:', data.shipment_id);
  console.log('Tracking URL:', data.tracking_url);
  // Send tracking information to patient
}

async function handleShipmentCancelled(data) {
  console.log('Shipment cancelled:', data.shipment_id);
  // Handle cancellation
}

async function handlePractitionerActivated(data) {
  console.log('Practitioner activated:', data.practitioner_id);
  console.log('Name:', data.practitioner_name);
  // Update practitioner status in your system
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
    event_name = payload.get('event_name')
    event_data = payload.get('event_data')
    
    handlers = {
        'medication_order:verified': handle_medication_order_verified,
        'medication_order:submitted': handle_medication_order_submitted,
        'medication_order:acknowledged': handle_medication_order_acknowledged,
        'medication_order:filled': handle_medication_order_filled,
        'shipment:created': handle_shipment_created,
        'shipment:cancelled': handle_shipment_cancelled,
        'practitioner:activated': handle_practitioner_activated,
    }
    
    handler = handlers.get(event_name)
    if handler:
        handler(event_data)
    else:
        print(f'Unknown event: {event_name}')

def handle_medication_order_verified(data):
    print(f"Medication order verified: {data.get('id')}")
    # Update order status

def handle_medication_order_submitted(data):
    print(f"Medication order submitted: {data.get('id')}")
    # Track submission

def handle_medication_order_acknowledged(data):
    print(f"Medication order acknowledged: {data.get('id')}")
    # Update acknowledgment status

def handle_medication_order_filled(data):
    print(f"Medication order filled: {data.get('id')}")
    # Process filled order

def handle_shipment_created(data):
    print(f"Shipment created: {data.get('shipment_id')}")
    print(f"Tracking URL: {data.get('tracking_url')}")
    # Send tracking info to patient

def handle_shipment_cancelled(data):
    print(f"Shipment cancelled: {data.get('shipment_id')}")
    # Handle cancellation

def handle_practitioner_activated(data):
    print(f"Practitioner activated: {data.get('practitioner_id')}")
    print(f"Name: {data.get('practitioner_name')}")
    # Update practitioner status

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

Handle duplicate events gracefully using the medication order `id` and `event_name`:

```javascript
const processedEvents = new Map();

async function isEventProcessed(id, eventName) {
  const key = `${id}:${eventName}`;
  return processedEvents.has(key);
}

async function markEventProcessed(id, eventName) {
  const key = `${id}:${eventName}`;
  processedEvents.set(key, Date.now());
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

- ✅ Implement idempotency checking using `id` and `event_name`
- ✅ Store processed event keys in database

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
5. ✅ **Implement Idempotency** - Handle duplicate events using `id` and `event_name`
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
