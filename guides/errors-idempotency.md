---
title: Errors & Idempotency
description: Handle errors and ensure idempotent operations
---

# Errors & Idempotency

Understand error responses and implement idempotent API calls.

## Error Response Format

All errors follow a consistent format:

```json
{
  "error": {
    "code": "error_code",
    "message": "Human-readable error message",
    "requestId": "req_abc123xyz",
    "details": {
      // Additional error-specific details
    }
  }
}
```

### Error Fields

- `code`: Machine-readable error code
- `message`: Human-readable error message
- `requestId`: Unique identifier for this request (useful for support)
- `details`: Additional error information (structure varies)

## HTTP Status Codes

| Status | Meaning | Description |
|--------|---------|-------------|
| `200` | OK | Request succeeded |
| `201` | Created | Resource created successfully |
| `400` | Bad Request | Invalid request parameters |
| `401` | Unauthorized | Missing or invalid API key |
| `403` | Forbidden | Insufficient permissions |
| `404` | Not Found | Resource doesn't exist |
| `409` | Conflict | Idempotency key conflict |
| `422` | Unprocessable Entity | Validation error |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Server error |

## Common Error Codes

### Validation Errors

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

### Authentication Errors

```json
{
  "error": {
    "code": "unauthorized",
    "message": "Invalid or missing API key",
    "requestId": "req_abc123"
  }
}
```

### Not Found Errors

```json
{
  "error": {
    "code": "not_found",
    "message": "The requested resource was not found",
    "requestId": "req_abc123"
  }
}
```

### Idempotency Conflicts

```json
{
  "error": {
    "code": "idempotency_key_conflict",
    "message": "An order with this idempotency key already exists",
    "requestId": "req_abc123"
  }
}
```

## Idempotency

Idempotency ensures that retrying a request doesn't create duplicate resources.

### Using Idempotency Keys

Include an `Idempotency-Key` header in POST requests:

```bash
curl -X POST https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key" \
  -H "Idempotency-Key: unique-key-123" \
  -d '{...}'
```

### How It Works

1. **First request**: Creates the resource, returns 201
2. **Retry with same key**: Returns original response (201) or conflict (409)
3. **Different key**: Creates a new resource

### Idempotency Key Format

- **Length**: 1-255 characters
- **Uniqueness**: Must be unique per operation
- **Format**: Any string (recommend descriptive format)

**Recommended formats**:
- `order-{date}-{sequence}`: `order-2025-01-15-001`
- `order-{patientId}-{timestamp}`: `order-pat_123-1705320000`
- UUID: `550e8400-e29b-41d4-a716-446655440000`

### Idempotency Key Best Practices

1. **Include in all POST requests**: Always use idempotency keys for resource creation
2. **Make keys unique**: Use timestamps, UUIDs, or sequence numbers
3. **Store keys**: Save idempotency keys with created resources for reference
4. **Handle conflicts**: If you receive a 409, retrieve the existing resource

### Example: Handling Idempotency

```python
import requests
import uuid

def create_order(order_data):
    idempotency_key = f"order-{uuid.uuid4()}"
    
    response = requests.post(
        'https://api-sandbox.nimbus-os.com/orders',
        headers={
            'X-API-Key': api_key,
            'Idempotency-Key': idempotency_key,
            'Content-Type': 'application/json'
        },
        json=order_data
    )
    
    if response.status_code == 409:
        # Order already exists with this key
        # Retrieve existing order or handle accordingly
        existing_order_id = get_existing_order_id(idempotency_key)
        return get_order(existing_order_id)
    
    return response.json()
```

## Retry Strategies

### Exponential Backoff

Retry failed requests with increasing delays:

```python
import time

def retry_with_backoff(func, max_retries=5):
    for attempt in range(max_retries):
        try:
            return func()
        except RetryableError as e:
            if attempt == max_retries - 1:
                raise
            
            wait_time = 2 ** attempt  # 1s, 2s, 4s, 8s, 16s
            time.sleep(wait_time)
```

### Retryable vs Non-Retryable Errors

**Retryable** (5xx errors, network errors):
- `500` Internal Server Error
- `502` Bad Gateway
- `503` Service Unavailable
- `504` Gateway Timeout
- Network timeouts

**Non-Retryable** (4xx errors):
- `400` Bad Request
- `401` Unauthorized
- `403` Forbidden
- `404` Not Found
- `422` Validation Error

**Special case**:
- `409` Conflict: Don't retry, handle idempotency conflict

### Request ID Tracking

Always log the `requestId` from error responses. This helps with debugging and support:

```python
try:
    response = requests.post(...)
    response.raise_for_status()
except requests.HTTPError as e:
    error_data = e.response.json()
    request_id = error_data['error']['requestId']
    logger.error(f"Request failed: {request_id}")
    # Include requestId when contacting support
```

## Error Handling Examples

### Python

```python
import requests

def create_order(order_data, api_key, idempotency_key):
    try:
        response = requests.post(
            'https://api-sandbox.nimbus-os.com/orders',
            headers={
                'X-API-Key': api_key,
                'Idempotency-Key': idempotency_key,
                'Content-Type': 'application/json'
            },
            json=order_data
        )
        
        if response.status_code == 201:
            return response.json()
        elif response.status_code == 409:
            # Idempotency conflict - retrieve existing order
            return handle_idempotency_conflict(idempotency_key)
        else:
            error = response.json()
            raise APIError(
                code=error['error']['code'],
                message=error['error']['message'],
                request_id=error['error']['requestId']
            )
    except requests.RequestException as e:
        raise NetworkError(str(e))
```

### JavaScript

```javascript
async function createOrder(orderData, apiKey, idempotencyKey) {
  try {
    const response = await fetch('https://api-sandbox.nimbus-os.com/orders', {
      method: 'POST',
      headers: {
        'X-API-Key': apiKey,
        'Idempotency-Key': idempotencyKey,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(orderData)
    });
    
    if (response.status === 201) {
      return await response.json();
    } else if (response.status === 409) {
      // Handle idempotency conflict
      return handleIdempotencyConflict(idempotencyKey);
    } else {
      const error = await response.json();
      throw new APIError(
        error.error.code,
        error.error.message,
        error.error.requestId
      );
    }
  } catch (error) {
    if (error instanceof APIError) {
      throw error;
    }
    throw new NetworkError(error.message);
  }
}
```

## Best Practices

1. **Always use idempotency keys**: For all POST requests that create resources
2. **Handle errors gracefully**: Check status codes and parse error responses
3. **Log request IDs**: Include `requestId` in error logs for support
4. **Implement retries**: Retry retryable errors with exponential backoff
5. **Don't retry non-retryable errors**: 4xx errors indicate client issues
6. **Handle idempotency conflicts**: Check for existing resources on 409

## Next Steps

- [Learn about orders](/guides/orders)
- [Learn about authentication](/guides/authentication)
- [Read the API reference](/api-reference)
