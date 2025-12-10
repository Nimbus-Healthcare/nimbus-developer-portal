---
title: Errors & Idempotency
description: Handle API errors and ensure reliable operations
---

# API Errors

Understand error responses and handle them properly in your integration.

## Error Response Format

All errors follow a consistent format:

```json
{
  "success": false,
  "error": {
    "message": "Human-readable error message",
    "details": {}
  }
}
```

### Error Fields

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Always `false` for errors |
| `error.message` | string | Human-readable error description |
| `error.details` | object | Additional error information (optional) |

## HTTP Status Codes

The following table describes all the possible HTTP error codes:

| Code | Name | Description |
|------|------|-------------|
| `200` | OK | Request succeeded |
| `201` | Created | Resource created successfully |
| `400` | Bad Request | Invalid request parameters or validation error |
| `401` | Unauthorized | Missing or invalid API key |
| `404` | Not Found | Resource not found |
| `5XX` | Server Error | An error occurred on the NovaMed API |

## Common Error Scenarios

### 400 Bad Request

Returned when request validation fails:

```json
{
  "success": false,
  "error": {
    "message": "Clinic ID must be a valid UUID."
  }
}
```

**Common causes:**
- Missing required fields
- Invalid field formats (UUID, email, etc.)
- Invalid clinic ID
- Invalid practitioner or patient ID

**Example - Clinic not found:**

```json
{
  "success": false,
  "error": {
    "message": "Clinic not found"
  }
}
```

### 401 Unauthorized

Returned when authentication fails:

```json
{
  "success": false,
  "error": {
    "message": "Unauthorized. This can happen if the access token is invalid, expired or has been revoked"
  }
}
```

**Solutions:**
- Verify the `x-api-key` header is present
- Check that the API key is valid
- Ensure you're using the correct environment

### 404 Not Found

Returned when a resource doesn't exist:

```json
{
  "success": false,
  "error": {
    "message": "Resource not found"
  }
}
```

**Common causes:**
- Invalid resource ID
- Resource was deleted
- Wrong environment

### Refill-Specific Errors

```json
{
  "success": false,
  "error": {
    "message": "Medication request not found"
  }
}
```

```json
{
  "success": false,
  "error": {
    "message": "Refills are not possible for pending medication requests"
  }
}
```

```json
{
  "success": false,
  "error": {
    "message": "Refill request already exists"
  }
}
```

## Error Handling Best Practices

### 1. Check Response Status

Always check the `success` field in responses:

```python
import requests

response = requests.post(url, headers=headers, json=data)
result = response.json()

if result.get('success'):
    # Handle success
    data = result.get('data')
else:
    # Handle error
    error_message = result.get('error', {}).get('message')
    print(f"Error: {error_message}")
```

### 2. Handle Different Error Types

```python
def handle_api_error(response):
    result = response.json()
    
    if response.status_code == 400:
        # Validation error - check your request
        raise ValidationError(result['error']['message'])
    
    elif response.status_code == 401:
        # Authentication error - check API key
        raise AuthenticationError("Invalid API key")
    
    elif response.status_code == 404:
        # Resource not found
        raise NotFoundError(result['error']['message'])
    
    elif response.status_code >= 500:
        # Server error - retry with backoff
        raise ServerError("NovaMed API error, please retry")
```

### 3. Implement Retry Logic

For server errors (5XX), implement retry with exponential backoff:

```python
import time

def retry_with_backoff(func, max_retries=3):
    for attempt in range(max_retries):
        try:
            return func()
        except ServerError as e:
            if attempt == max_retries - 1:
                raise
            
            wait_time = 2 ** attempt  # 1s, 2s, 4s
            print(f"Retrying in {wait_time}s...")
            time.sleep(wait_time)
```

### 4. Log Errors for Debugging

```python
import logging

logger = logging.getLogger(__name__)

def make_api_request(endpoint, data):
    try:
        response = requests.post(endpoint, json=data, headers=headers)
        result = response.json()
        
        if not result.get('success'):
            logger.error(f"API Error: {result.get('error')}")
            logger.error(f"Request Data: {data}")
        
        return result
    
    except Exception as e:
        logger.exception(f"Request failed: {e}")
        raise
```

## Idempotency

For operations that create resources, ensure idempotency by:

1. **Using unique identifiers**: Generate unique IDs on your side before creating resources
2. **Checking for existing resources**: Before creating, check if a resource already exists
3. **Handling duplicates gracefully**: If a resource already exists, return the existing one

### Example: Preventing Duplicate Refill Requests

```python
def request_refill(medication_request_id):
    """
    Request a refill, handling the case where one already exists.
    """
    response = requests.post(
        f"{BASE_URL}/api/external/refill-request",
        headers=headers,
        json={"medication_request_id": medication_request_id}
    )
    
    result = response.json()
    
    if result.get('success'):
        return result['data']
    
    # Check if refill already exists
    if "already exists" in result.get('error', {}).get('message', ''):
        # Refill was already requested - this is OK
        return {"status": "already_requested"}
    
    # Other error
    raise RefillError(result['error']['message'])
```

## Error Handling Examples

### Python

```python
import requests

BASE_URL = "https://novamed-feapidev.stackmod.info"

def create_practitioner(practitioner_data):
    """Create a practitioner with proper error handling."""
    
    response = requests.post(
        f"{BASE_URL}/api/external/practitioner",
        headers={
            "x-api-key": API_KEY,
            "Content-Type": "application/json",
            "Accept": "application/json"
        },
        json=practitioner_data
    )
    
    result = response.json()
    
    if result.get('success'):
        return result['data']
    
    error_message = result.get('error', {}).get('message', 'Unknown error')
    
    if response.status_code == 400:
        raise ValueError(f"Validation error: {error_message}")
    elif response.status_code == 401:
        raise PermissionError("Invalid API key")
    elif response.status_code == 404:
        raise LookupError(error_message)
    else:
        raise Exception(f"API error: {error_message}")
```

### Node.js

```javascript
const axios = require('axios');

const BASE_URL = 'https://novamed-feapidev.stackmod.info';

async function createPractitioner(practitionerData) {
  try {
    const response = await axios.post(
      `${BASE_URL}/api/external/practitioner`,
      practitionerData,
      {
        headers: {
          'x-api-key': process.env.NOVAMED_API_KEY,
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        }
      }
    );
    
    if (response.data.success) {
      return response.data.data;
    }
    
    throw new Error(response.data.error?.message || 'Unknown error');
    
  } catch (error) {
    if (error.response) {
      const status = error.response.status;
      const message = error.response.data?.error?.message;
      
      if (status === 400) {
        throw new Error(`Validation error: ${message}`);
      } else if (status === 401) {
        throw new Error('Invalid API key');
      } else if (status === 404) {
        throw new Error(`Not found: ${message}`);
      }
    }
    
    throw error;
  }
}
```

## Next Steps

- [Learn about authentication](/guides/authentication)
- [Set up webhooks](/guides/webhooks)
- [Read the API reference](/api-reference)
