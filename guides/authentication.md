---
title: Authentication
description: Authenticate API requests with API keys
---

# Authentication

All API requests require authentication using an API key and Clinic ID.

## API Key Authentication

We will provide an **API Key** and **Clinic Id**. All requests need to pass a header `x-api-key` with the provided value.

Include your API key in every request:

```bash
curl https://novamed-feapidev.stackmod.info/api/external/practitioner \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```

### Required Headers

| Header | Value | Required |
|--------|-------|----------|
| `x-api-key` | Your API key | Yes |
| `Content-Type` | `application/json` | Yes |
| `Accept` | `application/json` | Yes |

### Clinic ID

Most endpoints also require a `clinic_id` in the request body. This identifies which clinic the operation is for.

```json
{
  "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
  "first_name": "John",
  "last_name": "Smith",
  ...
}
```

## Getting Your Credentials

API credentials are provided by the NovaMed team upon partner approval.

1. Complete the partner onboarding process
2. Sign the Business Associate Agreement (BAA) if accessing PHI
3. Receive your API Key and Clinic ID
4. Store credentials securely

**Important**: Store your API key securely. Treat it like a password.

**Note**: API access is limited to Approved Partners only. By using the API, you agree to the [API License Terms & Conditions](/api-terms).

## Key Management

### Storing Keys Securely

Never commit API keys to version control. Use environment variables or secure secret management:

```bash
# Good - use environment variables
export NOVAMED_API_KEY="your-key-here"
export NOVAMED_CLINIC_ID="your-clinic-id-here"

curl -H "x-api-key: $NOVAMED_API_KEY" ...
```

```python
# Python example
import os

api_key = os.environ.get('NOVAMED_API_KEY')
clinic_id = os.environ.get('NOVAMED_CLINIC_ID')
```

### Use Different Keys Per Environment

Use separate API keys for development and production. This allows you to:
- Test safely without affecting production
- Revoke development keys without impacting live integrations
- Track usage per environment

### Restrict Key Access

Limit who has access to API keys:
- Use secret management tools (AWS Secrets Manager, HashiCorp Vault, etc.)
- Rotate keys when team members leave
- Monitor key usage for anomalies

## Security Best Practices

### Use HTTPS Only

Always use HTTPS. The API requires TLS 1.2 or higher. Never send API keys over unencrypted connections.

### Environment Security

| Environment | URL | PHI Allowed |
|-------------|-----|-------------|
| Development | `https://novamed-feapidev.stackmod.info` | No |
| Production | `https://feapi.novamed.care` | Yes (with BAA) |

**Important**: Do not send PHI to the Development environment.

## HIPAA & PHI Handling

The NovaMed API handles Protected Health Information (PHI). When building integrations:

- **Encrypt data in transit**: Always use HTTPS (required by the API)
- **Encrypt data at rest**: Store any PHI securely in your systems
- **Implement access controls**: Limit who can access API keys and PHI
- **Audit access**: Log API key usage and data access
- **Follow your compliance policies**: Ensure your integration meets HIPAA requirements

The API is designed to comply with HIPAA requirements, but you are responsible for ensuring your integration also complies.

## Error Responses

### 401 Unauthorized

The API key is missing or invalid:

```json
{
  "success": false,
  "error": {
    "message": "Unauthorized. This can happen if the access token is invalid, expired or has been revoked"
  }
}
```

**Solutions**:
- Verify the key is in the `x-api-key` header (lowercase)
- Check that the key hasn't been revoked
- Ensure you're using the correct environment

### 400 Bad Request

The request is invalid:

```json
{
  "success": false,
  "error": {
    "message": "Clinic not found"
  }
}
```

**Solutions**:
- Verify the `clinic_id` is correct
- Check all required fields are provided
- Validate field formats (UUIDs, emails, etc.)

## Example Requests

### Create a Practitioner

```bash
curl -X POST https://novamed-feapidev.stackmod.info/api/external/practitioner \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "first_name": "Sarah",
    "last_name": "Johnson",
    "email": "dr.johnson@clinic.com",
    "npi_number": "1234567890",
    "assigned_clinic": "550e8400-e29b-41d4-a716-446655440000"
  }'
```

### Create a Patient

```bash
curl -X POST https://novamed-feapidev.stackmod.info/api/external/patient \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "first_name": "John",
    "last_name": "Smith",
    "email": "john.smith@email.com",
    "date_of_birth": "1985-03-15"
  }'
```

## Next Steps

- [Make your first request](/guides/quickstart)
- [Learn about error handling](/guides/errors-idempotency)
- [Read the API reference](/api-reference)
