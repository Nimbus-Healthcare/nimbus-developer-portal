---
title: Authentication
description: Authenticate API requests with API keys
---

# Authentication

All API requests require authentication using an API key sent in the `X-API-Key` header.

## API Key Authentication

Include your API key in every request:

```bash
curl https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key-here"
```

### Header Format

```
X-API-Key: your-api-key-here
```

The API key is a string that identifies your integration and authorizes access to the API.

## Getting an API Key

1. Sign in to the [developer portal](https://developer.nimbus-os.com)
2. Navigate to **API Keys**
3. Click **Create API Key**
4. Choose an environment (Sandbox or Production)
5. Copy the key immediately (you won't be able to view it again)

**Important**: Store your API key securely. Treat it like a password.

**Note**: API access is limited to Approved Partners only. By using the API, you agree to the [API License Terms & Conditions](/api-terms).

## Key Management

### Rotating Keys

Regularly rotate your API keys for security:

1. Create a new API key
2. Update your integration to use the new key
3. Verify everything works
4. Revoke the old key

### Revoking Keys

If a key is compromised or no longer needed:

1. Navigate to **API Keys** in the developer portal
2. Find the key you want to revoke
3. Click **Revoke**
4. The key will immediately stop working

## Security Best Practices

### Never Commit Keys

Never commit API keys to version control. Use environment variables or secure secret management:

```bash
# Good
export NIMBUS_API_KEY="your-key-here"
curl -H "X-API-Key: $NIMBUS_API_KEY" ...

# Bad - don't do this
curl -H "X-API-Key: sk_live_abc123xyz" ...
```

### Use Different Keys Per Environment

Use separate API keys for sandbox and production. This allows you to:
- Test safely without affecting production
- Revoke sandbox keys without impacting live integrations
- Track usage per environment

### Restrict Key Access

Limit who has access to API keys:
- Use secret management tools (AWS Secrets Manager, HashiCorp Vault, etc.)
- Rotate keys when team members leave
- Monitor key usage for anomalies

### Use HTTPS Only

Always use HTTPS. The API requires TLS 1.2 or higher. Never send API keys over unencrypted connections.

## HIPAA & PHI Handling

The Nimbus OS API handles Protected Health Information (PHI). When building integrations:

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
  "error": {
    "code": "unauthorized",
    "message": "Invalid or missing API key",
    "requestId": "req_abc123"
  }
}
```

**Solutions**:
- Verify the key is in the `X-API-Key` header
- Check that the key hasn't been revoked
- Ensure you're using the correct environment

### 403 Forbidden

The API key doesn't have permission for the requested resource:

```json
{
  "error": {
    "code": "forbidden",
    "message": "Insufficient permissions",
    "requestId": "req_abc123"
  }
}
```

Contact support if you believe you should have access.

## Next Steps

- [Make your first request](/guides/quickstart)
- [Learn about error handling](/guides/errors-idempotency)
- [Read the API reference](/api-reference)
