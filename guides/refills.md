---
title: Refills
description: Manage prescription refills
---

# Refills

Automate prescription refill requests through the Nimbus OS API.

## Creating Refill Requests

Initiate a refill request for an existing prescription:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/refills \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: refill-2025-01-15-001" \
  -d '{
    "prescriptionId": "rx_9876543210",
    "patientId": "pat_1234567890",
    "requestedQuantity": 30
  }'
```

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `prescriptionId` | string | Yes | Unique prescription identifier |
| `patientId` | string | Yes | Unique patient identifier |
| `requestedQuantity` | integer | No | Requested quantity (defaults to original prescription quantity) |

### Response

```json
{
  "id": "ref_abc123xyz",
  "prescriptionId": "rx_9876543210",
  "patientId": "pat_1234567890",
  "status": "pending",
  "requestedQuantity": 30,
  "createdAt": "2025-01-15T15:00:00Z"
}
```

## Refill Workflow

Refills progress through the following stages:

1. **pending** - Refill request created, awaiting approval
2. **approved** - Refill approved, ready for processing
3. **processing** - Refill being prepared
4. **shipped** - Refill shipped to patient
5. **cancelled** - Refill cancelled

### Status Flow

```
pending → approved → processing → shipped
   ↓
cancelled
```

## Eligibility Checks

The system automatically checks refill eligibility when a request is created:

- **Prescription validity**: Prescription must be active and valid
- **Refill timing**: Must be within allowed refill window
- **Quantity limits**: Requested quantity must be within allowed limits
- **Insurance coverage**: Insurance coverage verified if applicable

If eligibility checks fail, the refill request will be created with status `pending` and may require manual review.

## Idempotency

Include an `Idempotency-Key` header to prevent duplicate refill requests:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/refills \
  -H "X-API-Key: your-api-key" \
  -H "Idempotency-Key: refill-rx_987-2025-01-15" \
  -d '{...}'
```

Use a unique key for each distinct refill request. Include the prescription ID and date in the key for easy reference.

## Webhook Notifications

Receive webhook notifications when refill status changes:

- `refill.ready` - Refill approved and ready for processing

[Learn more about webhooks →](/guides/webhooks)

## Examples

### Basic Refill Request

```bash
curl -X POST https://api-sandbox.nimbus-os.com/refills \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: refill-2025-01-15-001" \
  -d '{
    "prescriptionId": "rx_9876543210",
    "patientId": "pat_1234567890"
  }'
```

### Refill with Custom Quantity

```bash
curl -X POST https://api-sandbox.nimbus-os.com/refills \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: refill-2025-01-15-002" \
  -d '{
    "prescriptionId": "rx_9876543210",
    "patientId": "pat_1234567890",
    "requestedQuantity": 60
  }'
```

## Error Handling

### Validation Errors

Invalid request data:

```json
{
  "error": {
    "code": "validation_error",
    "message": "The request body is invalid",
    "requestId": "req_abc123",
    "details": {
      "field": "prescriptionId",
      "reason": "Prescription not found"
    }
  }
}
```

### Eligibility Errors

Refill not eligible:

```json
{
  "error": {
    "code": "refill_not_eligible",
    "message": "Refill is not eligible at this time",
    "requestId": "req_abc123",
    "details": {
      "reason": "Too early for refill",
      "nextEligibleDate": "2025-01-22"
    }
  }
}
```

## Best Practices

1. **Check eligibility first**: Verify refill eligibility before creating requests
2. **Use idempotency keys**: Prevent duplicate refill requests
3. **Handle webhooks**: Set up webhook endpoints to receive status updates
4. **Monitor status**: Poll refill status or use webhooks for real-time updates

## Next Steps

- [Learn about tracking](/guides/tracking)
- [Set up webhooks](/guides/webhooks)
- [Read the API reference](/api-reference#tag/Refills)
