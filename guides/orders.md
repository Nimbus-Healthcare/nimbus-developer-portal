---
title: Orders
description: Create and manage pharmacy orders
---

# Orders

Create and manage pharmacy orders programmatically through the Nimbus OS API.

## Creating Orders

Create a new order with patient and prescription information:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key" \
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
    },
    "notes": "Please leave at front door"
  }'
```

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `patientId` | string | Yes | Unique patient identifier |
| `prescriptionId` | string | Yes | Unique prescription identifier |
| `shippingAddress` | object | Yes | Delivery address |
| `notes` | string | No | Optional order notes (max 1000 chars) |

### Response

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
  "createdAt": "2025-01-15T15:00:00Z",
  "updatedAt": "2025-01-15T15:00:00Z"
}
```

## Retrieving Orders

Get order details by ID:

```bash
curl https://api-sandbox.nimbus-os.com/orders/ord_abc123xyz \
  -H "X-API-Key: your-api-key"
```

### Response

```json
{
  "id": "ord_abc123xyz",
  "patientId": "pat_1234567890",
  "prescriptionId": "rx_9876543210",
  "status": "processing",
  "shippingAddress": {
    "street1": "123 Main St",
    "city": "Austin",
    "state": "TX",
    "zip": "78701",
    "country": "US"
  },
  "trackingNumber": "1Z999AA10123456784",
  "createdAt": "2025-01-15T15:00:00Z",
  "updatedAt": "2025-01-15T16:30:00Z",
  "estimatedDeliveryDate": "2025-01-18"
}
```

## Order Lifecycle

Orders progress through the following statuses:

1. **pending** - Order created, awaiting processing
2. **processing** - Order is being prepared
3. **shipped** - Order has been shipped (tracking number available)
4. **delivered** - Order delivered to patient
5. **cancelled** - Order cancelled (cannot be fulfilled)

### Status Transitions

```
pending → processing → shipped → delivered
   ↓
cancelled
```

Once an order reaches `shipped` or `delivered`, it cannot be cancelled.

## Idempotency

Always include an `Idempotency-Key` header when creating orders to prevent duplicates:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key" \
  -H "Idempotency-Key: order-2025-01-15-001" \
  -d '{...}'
```

If you retry the request with the same idempotency key, you'll receive the original order response (status 201) or a conflict response (status 409) if the key was already used.

**Best practices**:
- Use a unique key for each distinct order
- Include timestamp or sequence number in the key
- Store the key with the order for reference

Example idempotency key formats:
- `order-{date}-{sequence}`: `order-2025-01-15-001`
- `order-{patientId}-{timestamp}`: `order-pat_123-1705320000`
- UUID: `550e8400-e29b-41d4-a716-446655440000`

## Tracking Orders

Once an order is shipped, a `trackingNumber` is available in the order response. Use this with the carrier's tracking system or our tracking webhooks.

You can also receive webhook notifications when order status changes. [Learn about webhooks →](/guides/webhooks)

## Error Handling

### Validation Errors

If the request body is invalid:

```json
{
  "error": {
    "code": "validation_error",
    "message": "The request body is invalid",
    "requestId": "req_abc123",
    "details": {
      "field": "shippingAddress.zip",
      "reason": "Invalid ZIP code format"
    }
  }
}
```

### Idempotency Conflicts

If an order with the same idempotency key already exists:

```json
{
  "error": {
    "code": "idempotency_key_conflict",
    "message": "An order with this idempotency key already exists",
    "requestId": "req_abc123"
  }
}
```

Retrieve the existing order using the order ID from your records, or use a different idempotency key.

## Examples

### Create Order with Full Address

```bash
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: order-2025-01-15-001" \
  -d '{
    "patientId": "pat_1234567890",
    "prescriptionId": "rx_9876543210",
    "shippingAddress": {
      "street1": "123 Main St",
      "street2": "Apt 4B",
      "city": "Austin",
      "state": "TX",
      "zip": "78701",
      "country": "US"
    },
    "notes": "Fragile - handle with care"
  }'
```

### Check Order Status

```bash
curl https://api-sandbox.nimbus-os.com/orders/ord_abc123xyz \
  -H "X-API-Key: your-api-key"
```

## Next Steps

- [Learn about tracking](/guides/tracking)
- [Set up webhooks](/guides/webhooks)
- [Read the API reference](/api-reference#tag/Orders)
