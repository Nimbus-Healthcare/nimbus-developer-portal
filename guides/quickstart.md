---
title: Quickstart
description: Get started with the NovaMed Partner API in minutes
---

# Quickstart

Get your first API call working in under 5 minutes.

## Prerequisites

- An API key and Clinic ID (provided by NovaMed team)
- `curl` or your preferred HTTP client
- Access to the development environment
- Approved Partner status

**Note**: By using the API, you agree to the [API License Terms & Conditions](/api-terms).

## Get Your API Key

API credentials are provided by the NovaMed team upon partner approval:

1. Complete the partner onboarding process
2. Sign the Business Associate Agreement (BAA) if accessing PHI
3. Receive your API Key and Clinic ID from the NovaMed team
4. Store credentials securely

**Contact**: [api@nimbus-os.com](mailto:api@nimbus-os.com) to request API access.

## Make Your First Request

Let's create a practitioner to verify your setup:

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/practitioner \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "first_name": "Sarah",
    "last_name": "Johnson",
    "email": "dr.johnson@partnerclinic.com",
    "npi_number": "1234567890",
    "assigned_clinic": "your-clinic-id-here"
  }'
```

If successful, you'll receive a JSON response:

```json
{
  "success": true,
  "data": {
    "practitioner_id": "660e8400-e29b-41d4-a716-446655440001"
  },
  "message": "Practitioner created successfully"
}
```

## Create a Patient

Now let's create a patient:

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/patient \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "clinic_id": "your-clinic-id-here",
    "first_name": "John",
    "last_name": "Smith",
    "date_of_birth": "1985-03-15",
    "gender": "male",
    "email": "john.smith@email.com",
    "phone": "+1-555-0199",
    "address_line1": "123 Main Street",
    "city": "San Francisco",
    "state": "CA",
    "zip": "94102"
  }'
```

The response includes the created patient:

```json
{
  "success": true,
  "data": {
    "patient_id": "770e8400-e29b-41d4-a716-446655440002"
  },
  "message": "Patient created successfully"
}
```

## Create a Medication Request

Now create a medication request for the patient:

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/medication-request \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "clinic_id": "your-clinic-id-here",
    "patient_id": "770e8400-e29b-41d4-a716-446655440002",
    "practitioner_id": "660e8400-e29b-41d4-a716-446655440001",
    "medication_name": "Testosterone Cypionate",
    "medication_strength": "200mg/ml",
    "quantity": 1,
    "refills": 3,
    "days_supply": 30,
    "instructions": "Inject 0.5ml intramuscularly twice weekly"
  }'
```

Response:

```json
{
  "success": true,
  "data": {
    "medication_request_id": "880e8400-e29b-41d4-a716-446655440003"
  },
  "message": "Medication request created successfully"
}
```

## Set Up Webhooks

Register a webhook to receive real-time notifications:

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "clinic_id": "your-clinic-id-here",
    "webhook_url": "https://your-server.com/webhooks/novamed"
  }'
```

Response:

```json
{
  "success": true,
  "data": {
    "webhook_id": "bb0e8400-e29b-41d4-a716-446655440006"
  },
  "message": "Webhook created successfully"
}
```

## Complete Integration Flow

Here's the typical integration workflow:

<ol class="workflow-list">
  <li><strong>Create Practitioner</strong> — Onboard prescribing practitioners</li>
  <li><strong>Create Patient</strong> — Register patients with demographics and address</li>
  <li><strong>Create Medication Request</strong> — Submit prescriptions for fulfillment</li>
  <li><strong>Register Webhook</strong> — Receive status updates automatically</li>
  <li><strong>Request Refills</strong> — Initiate refills for shipped medications</li>
</ol>

## Next Steps

- [Learn about authentication](/guides/authentication)
- [Set up webhooks](/guides/webhooks)
- [Handle errors](/guides/errors-idempotency)
- [Read the API reference](/api-reference)

## Common Issues

### 401 Unauthorized

Your API key is missing or invalid. Verify:
- The key is included in the `x-api-key` header (lowercase)
- The key hasn't been revoked
- You're using the correct environment

### 400 Bad Request

The request is invalid. Common causes:
- Missing required fields
- Invalid `clinic_id`
- Invalid UUID format
- Invalid email format

### 404 Not Found

The resource doesn't exist. Check:
- The resource ID is correct
- The resource belongs to your clinic
- You're using the correct environment

## Environment URLs

| Environment | Base URL |
|-------------|----------|
| Development | `https://novamed-feapidev.nimbushealthcaretest.com` |
| Production | `https://feapi.novamed.care` |

**Important**: Do not send PHI to the Development environment.

## Need Help?

- [API Reference](/api-reference)
- [Error Handling Guide](/guides/errors-idempotency)
- [Contact Support](mailto:api@nimbus-os.com)
