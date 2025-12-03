---
title: Tracking
description: Track orders and shipments
---

# Tracking

Track orders and shipments through the Nimbus OS API.

## Order Tracking

Once an order is shipped, a `trackingNumber` is available in the order response:

```bash
curl https://api-sandbox.nimbus-os.com/orders/ord_abc123xyz \
  -H "X-API-Key: your-api-key"
```

### Response with Tracking

```json
{
  "id": "ord_abc123xyz",
  "status": "shipped",
  "trackingNumber": "1Z999AA10123456784",
  "estimatedDeliveryDate": "2025-01-18",
  "shippingAddress": {
    "street1": "123 Main St",
    "city": "Austin",
    "state": "TX",
    "zip": "78701",
    "country": "US"
  },
  "updatedAt": "2025-01-15T16:30:00Z"
}
```

## Status Codes

Orders progress through these statuses:

| Status | Description |
|--------|-------------|
| `pending` | Order created, awaiting processing |
| `processing` | Order being prepared |
| `shipped` | Order shipped (tracking number available) |
| `delivered` | Order delivered to patient |
| `cancelled` | Order cancelled |

## Tracking Numbers

Tracking numbers are assigned when an order reaches `shipped` status. The format depends on the carrier:

- **UPS**: `1Z999AA10123456784`
- **FedEx**: `123456789012`
- **USPS**: `9400111899223197428490`

Use the tracking number with the carrier's tracking system or API to get detailed shipment information.

## Webhook Integration

Receive real-time updates when order status changes:

### Order Status Webhooks

- `order.created` - Order created
- `order.fulfilled` - Order shipped (includes tracking number)

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

[Learn more about webhooks →](/guides/webhooks)

## Polling Alternatives

If you prefer polling over webhooks, check order status periodically:

```bash
# Check order status every 5 minutes
while true; do
  curl https://api-sandbox.nimbus-os.com/orders/ord_abc123xyz \
    -H "X-API-Key: your-api-key"
  sleep 300
done
```

**Note**: Webhooks are recommended for real-time updates and reduce API calls.

## Estimated Delivery Dates

Orders include an `estimatedDeliveryDate` once shipped:

```json
{
  "status": "shipped",
  "trackingNumber": "1Z999AA10123456784",
  "estimatedDeliveryDate": "2025-01-18"
}
```

Delivery dates are estimates based on carrier transit times and may change.

## Examples

### Check Order Status

```bash
curl https://api-sandbox.nimbus-os.com/orders/ord_abc123xyz \
  -H "X-API-Key: your-api-key"
```

### Monitor Status Changes

Set up a webhook endpoint to receive status updates automatically:

```bash
# Webhook payload example
{
  "id": "evt_1234567891",
  "type": "order.fulfilled",
  "createdAt": "2025-01-15T14:22:00Z",
  "data": {
    "orderId": "ord_abc123xyz",
    "status": "shipped",
    "trackingNumber": "1Z999AA10123456784"
  }
}
```

## Best Practices

1. **Use webhooks**: Set up webhook endpoints for real-time updates
2. **Handle status changes**: Update your system when order status changes
3. **Display tracking**: Show tracking numbers to users once available
4. **Respect rate limits**: Don't poll too frequently

## Next Steps

- [Set up webhooks](/guides/webhooks)
- [Learn about orders](/guides/orders)
- [Read the API reference](/api-reference#tag/Orders)
