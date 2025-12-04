# Partner API Documentation Plan - Pharmacy Operations

**Date**: December 2025  
**Focus**: Pharmacy-side operations for partners (shipping, prescriptions, refills)  
**Excludes**: Internal APIs (RPA, clinic creation, practitioner management)

---

## 🎯 Partner API Scope

Partners need access to **pharmacy operations** only:
- ✅ **Shipping confirmations** - When prescriptions are shipped
- ✅ **Prescription status updates** - When prescription status changes
- ✅ **Refill requests** - Requesting prescription refills
- ✅ **Shipment tracking** - Tracking information for shipments
- ✅ **Medication status** - Real-time medication order status

**NOT included** (Internal only):
- ❌ Clinic creation/management
- ❌ Practitioner creation/management
- ❌ Patient creation (handled internally)
- ❌ RPA workflows
- ❌ Internal admin operations

---

## 📋 Partner API Endpoints

### 1. **Webhook Configuration** (For receiving updates)

#### Register Webhook
**Endpoint**: `POST /api/external/webhook`  
**Purpose**: Register a webhook URL to receive pharmacy event notifications

**Request**:
```json
{
  "clinic_id": "uuid-of-clinic",
  "webhook_url": "https://partner.example.com/webhooks/nimbus"
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "webhook_id": "uuid"
  },
  "message": "Webhook created successfully"
}
```

#### Delete Webhook
**Endpoint**: `DELETE /api/external/webhook/:id`  
**Purpose**: Remove a webhook configuration

---

## 🔔 Webhook Events (What Partners Receive)

### 1. **Shipment Created**
**Event**: `shipment:created`  
**Triggered**: When a prescription shipment is created and ready to ship

**Payload**:
```json
{
  "event_name": "shipment:created",
  "event_data": {
    "shipment_id": "uuid",
    "medication_orders": ["order-id-1", "order-id-2"],
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

### 2. **Shipment Cancelled**
**Event**: `shipment:cancelled`  
**Triggered**: When a shipment is cancelled

**Payload**:
```json
{
  "event_name": "shipment:cancelled",
  "event_data": {
    "shipment_id": "uuid",
    "medication_orders": ["order-id-1"],
    "cancellation_reason": "Patient request",
    "cancelled_at": "2024-01-15T11:00:00.000Z"
  }
}
```

### 3. **Medication Status Updated**
**Event**: `medication:updated` (various event types)  
**Triggered**: When medication order status changes (from Pharmetika)

**Possible Event Types**:
- `medication:validated` - Order validated
- `medication:submitted` - Order submitted to pharmacy
- `medication:filled` - Medication filled
- `medication:shipped` - Medication shipped
- `medication:delivered` - Medication delivered
- `medication:cancelled` - Medication cancelled

**Payload**:
```json
{
  "event_name": "medication:updated",
  "event_data": {
    "medication_order_id": "uuid",
    "prescription_id": "uuid",
    "medication_request_id": "uuid",
    "status": "shipped",
    "previous_status": "filled",
    "updated_at": "2024-01-15T10:30:00.000Z",
    "pharmetika_status": "shipped",
    "tracking_number": "1Z999AA10123456784",
    "notes": "Out for delivery"
  }
}
```

---

## 🔐 Webhook Authentication

Webhooks include an `X-API-Key` header for authentication:
```
X-API-Key: your-partner-api-key
```

**Security Best Practices**:
- Verify the `X-API-Key` header matches your API key
- Use HTTPS endpoints only
- Implement idempotency (handle duplicate events)
- Return 200 OK quickly, process asynchronously

---

## 📊 Webhook Implementation Guide

### Step 1: Register Your Webhook

```bash
curl -X POST https://api.nimbus-os.com/api/external/webhook \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "your-clinic-uuid",
    "webhook_url": "https://your-domain.com/webhooks/nimbus"
  }'
```

### Step 2: Handle Webhook Events

**Example Node.js Handler**:
```javascript
app.post('/webhooks/nimbus', async (req, res) => {
  // Verify API key
  const apiKey = req.headers['x-api-key'];
  if (apiKey !== process.env.NIMBUS_API_KEY) {
    return res.status(401).send('Unauthorized');
  }

  const { event_name, event_data } = req.body;

  // Process event asynchronously
  processWebhookEvent(event_name, event_data).catch(console.error);

  // Return 200 immediately
  res.status(200).json({ received: true });
});

async function processWebhookEvent(eventName, eventData) {
  switch (eventName) {
    case 'shipment:created':
      await handleShipmentCreated(eventData);
      break;
    case 'shipment:cancelled':
      await handleShipmentCancelled(eventData);
      break;
    case 'medication:updated':
      await handleMedicationUpdated(eventData);
      break;
    default:
      console.log('Unknown event:', eventName);
  }
}
```

### Step 3: Implement Idempotency

```javascript
const processedEvents = new Set();

async function processWebhookEvent(eventName, eventData) {
  // Use shipment_id or medication_order_id as idempotency key
  const idempotencyKey = eventData.shipment_id || eventData.medication_order_id;
  
  if (processedEvents.has(idempotencyKey)) {
    console.log('Duplicate event, skipping');
    return;
  }
  
  processedEvents.add(idempotencyKey);
  // Process event...
}
```

---

## 🔄 Refill Requests (If Needed)

If partners need to **request refills** programmatically:

**Endpoint**: `POST /api/external/refill-request`  
**Purpose**: Request a refill for an existing medication order

**Request**:
```json
{
  "medication_request_id": "uuid-of-medication-request",
  "shipment_group_id": "optional-group-id"
}
```

**Response**:
```json
{
  "success": true,
  "message": "Refill request was created successfully.",
  "data": {
    "refill_request_id": "uuid",
    "medication_order_id": "uuid"
  }
}
```

**Note**: Refills are only available for medications that have been shipped or delivered.

---

## 📚 OpenAPI Specification Structure

### Base Configuration

```yaml
openapi: 3.1.0
info:
  title: Nimbus OS Partner API
  version: 1.0.0
  description: |
    Partner API for pharmacy operations integration.
    Partners receive real-time updates via webhooks for:
    - Shipment creation and tracking
    - Prescription status updates
    - Medication order status changes
    
    **Note**: This API is for pharmacy operations only.
    Internal operations (clinic management, practitioner onboarding)
    are not exposed to partners.

servers:
  - url: https://api-sandbox.nimbus-os.com/api/external
    description: Sandbox environment
  - url: https://api.nimbus-os.com/api/external
    description: Production environment

security:
  - ApiKeyAuth: []

components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
      description: Partner API key
```

### Tags

```yaml
tags:
  - name: Webhooks
    description: Webhook configuration for receiving pharmacy event notifications
  - name: Refills
    description: Prescription refill requests
```

### Webhook Events Schema

```yaml
components:
  schemas:
    WebhookEvent:
      type: object
      properties:
        event_name:
          type: string
          enum:
            - shipment:created
            - shipment:cancelled
            - medication:updated
          description: Type of webhook event
        event_data:
          type: object
          description: Event-specific data payload
    
    ShipmentCreatedEvent:
      allOf:
        - $ref: '#/components/schemas/WebhookEvent'
        - type: object
          properties:
            event_name:
              type: string
              example: "shipment:created"
            event_data:
              type: object
              properties:
                shipment_id:
                  type: string
                  format: uuid
                medication_orders:
                  type: array
                  items:
                    type: string
                shipment_status:
                  type: string
                tracking_number:
                  type: string
                tracking_url:
                  type: string
                  format: uri
                shipping_partner:
                  type: string
                delivery_method:
                  type: string
```

---

## 🎯 Implementation Checklist

### Phase 1: Webhook Documentation
- [ ] Document webhook registration endpoint
- [ ] Document webhook deletion endpoint
- [ ] Document all webhook event types
- [ ] Create webhook payload schemas
- [ ] Document webhook authentication
- [ ] Add webhook security best practices

### Phase 2: Integration Guides
- [ ] Create webhook integration guide
- [ ] Add webhook handler examples (Node.js, Python)
- [ ] Document idempotency patterns
- [ ] Document error handling and retries
- [ ] Add webhook testing guide

### Phase 3: Refill Documentation (If Needed)
- [ ] Document refill request endpoint
- [ ] Document refill eligibility rules
- [ ] Add refill examples

### Phase 4: Code Examples
- [ ] cURL examples for webhook registration
- [ ] JavaScript/Node.js webhook handler example
- [ ] Python webhook handler example
- [ ] Error handling examples

### Phase 5: Testing & Deployment
- [ ] Test webhook delivery in sandbox
- [ ] Validate webhook payloads
- [ ] Test webhook authentication
- [ ] Review documentation accuracy
- [ ] Deploy to developer portal

---

## 📖 Additional Documentation Needed

### 1. **Webhook Integration Guide**
- Setting up webhook endpoints
- Handling webhook events
- Webhook security
- Idempotency best practices
- Error handling and retries
- Testing webhooks locally (using ngrok or similar)

### 2. **Event Reference**
- Complete list of all webhook events
- Event payload schemas
- When each event is triggered
- Event frequency and timing

### 3. **Status Reference**
- Medication order statuses
- Shipment statuses
- Status transition flow
- What each status means

### 4. **Best Practices**
- Webhook endpoint design
- Handling webhook failures
- Rate limiting considerations
- Monitoring webhook delivery
- Debugging webhook issues

---

## 🔒 Security Considerations

### Webhook Security
- **HTTPS Only**: Webhooks must use HTTPS endpoints
- **API Key Verification**: Always verify `X-API-Key` header
- **Signature Verification**: Consider adding HMAC signature verification (future enhancement)
- **Idempotency**: Handle duplicate events gracefully
- **Rate Limiting**: Implement rate limiting on webhook endpoints

### API Key Management
- API keys are clinic-scoped
- Contact api@nimbus-os.com to obtain API keys
- API keys should be stored securely
- Rotate API keys periodically

---

## 📞 Support & Resources

- **API Support**: api@nimbus-os.com
- **Documentation**: https://developer.nimbus-os.com
- **Sandbox**: https://api-sandbox.nimbus-os.com
- **Production**: https://api.nimbus-os.com

---

## 🚀 Next Steps

1. **Review** this plan with engineering team
2. **Clarify** webhook event payloads (check actual implementation)
3. **Document** all webhook events with exact payloads
4. **Create** OpenAPI specification
5. **Write** webhook integration guide
6. **Add** code examples
7. **Test** webhook delivery in sandbox
8. **Deploy** to developer portal

---

**Last Updated**: December 2025  
**Status**: Focused on Pharmacy Operations - Ready for Documentation
