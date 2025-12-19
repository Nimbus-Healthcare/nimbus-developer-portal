---
title: API Reference
description: Complete NovaMed Partner API endpoint documentation
---

# API Reference

Complete reference documentation for all NovaMed Partner API endpoints.

## Base URLs

| Environment | URL |
|-------------|-----|
| **Development** | `https://novamed-feapidev.nimbushealthcaretest.com` |
| **Production** | `https://feapi.novamed.care` |

## Authentication

All API requests require an API key in the `x-api-key` header:

```bash
curl https://novamed-feapidev.nimbushealthcaretest.com/api/external/practitioner \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```

## Required Headers

| Header | Value | Required |
|--------|-------|----------|
| `x-api-key` | Your API key | Yes |
| `Content-Type` | `application/json` | Yes |
| `Accept` | `application/json` | Yes |

---

## Practitioners

Manage practitioners (doctors, veterinarians) in the NovaMed platform.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/external/practitioner` | Create a new practitioner |

### Create Practitioner

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/practitioner \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "npi_number": "1234567890",
    "first_name": "Sarah",
    "last_name": "Johnson",
    "email": "dr.johnson@partnerclinic.com",
    "phone": "+1-555-0123",
    "credentials": "MD",
    "specialty": "Internal Medicine",
    "license_number": "MD-12345",
    "license_state": "CA",
    "license_expiration": "2026-12-31",
    "dea_number": "FJ1234563",
    "dea_expiration": "2026-06-30",
    "signature_image": "base64_encoded_image_string",
    "assigned_clinic": "550e8400-e29b-41d4-a716-446655440000"
  }'
```

[View full Practitioner documentation →](/openapi-partner/practitioners)

---

## Patients

Register and manage patients in the NovaMed platform.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/external/patient` | Create a new patient |

### Create Patient

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/patient \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "first_name": "John",
    "last_name": "Smith",
    "date_of_birth": "1985-03-15",
    "gender": "male",
    "email": "john.smith@email.com",
    "phone": "+1-555-0199",
    "address": {
      "line1": "123 Main Street",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94102",
      "country": "US"
    }
  }'
```

[View full Patient documentation →](/openapi-partner/patients)

---

## Medication Requests

Create medication orders for patients.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/external/medication-request` | Create a new medication request |

### Create Medication Request

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/medication-request \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "patient_id": "660e8400-e29b-41d4-a716-446655440001",
    "practitioner_id": "770e8400-e29b-41d4-a716-446655440002",
    "medication_id": "880e8400-e29b-41d4-a716-446655440003",
    "quantity": 1,
    "refills": 3,
    "days_supply": 30,
    "instructions": "Inject 0.5ml (100mg) intramuscularly twice weekly",
    "diagnosis_codes": ["E29.1"]
  }'
```

[View full Medication Request documentation →](/openapi-partner/medication-requests)

---

## Refills

Request prescription refills for existing medication orders.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/external/refill-request` | Request a prescription refill |

### Create Refill Request

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/refill-request \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "medication_request_id": "990e8400-e29b-41d4-a716-446655440004",
    "shipping_address": {
      "line1": "123 Main Street",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94102",
      "country": "US"
    },
    "shipment_service_code": "usps_priority",
    "payment_method_id": "pm_card_visa_1234"
  }'
```

[View full Refill documentation →](/openapi-partner/refills)

---

## Webhooks

Register webhook endpoints to receive real-time event notifications.

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/external/webhook` | Register a webhook endpoint |
| `DELETE` | `/api/external/webhook` | Delete a webhook endpoint |

### Register Webhook

```bash
curl -X POST https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "webhook_url": "https://your-domain.com/webhooks/nimbus"
  }'
```

### Delete Webhook

```bash
curl -X DELETE https://novamed-feapidev.nimbushealthcaretest.com/api/external/webhook \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "clinic_id": "550e8400-e29b-41d4-a716-446655440000",
    "webhook_id": "aa0e8400-e29b-41d4-a716-446655440005"
  }'
```

[View full Webhook documentation →](/openapi-partner/webhooks)

---

## Response Format

All successful responses follow this structure:

```json
{
  "success": true,
  "data": {
    // Response-specific data
  },
  "message": "Operation completed successfully"
}
```

## Error Handling

Error responses include a standardized error object:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request parameters",
    "details": {
      "field": "email",
      "reason": "Invalid email format"
    }
  }
}
```

### Common Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `VALIDATION_ERROR` | 400 | Invalid request parameters |
| `UNAUTHORIZED` | 401 | Missing or invalid API key |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Server error |

---

## Interactive API Documentation

For interactive API documentation with "Try it" functionality, explore the OpenAPI endpoints:

- [Practitioners](/openapi-partner/practitioners)
- [Patients](/openapi-partner/patients)
- [Medication Requests](/openapi-partner/medication-requests)
- [Refills](/openapi-partner/refills)
- [Webhooks](/openapi-partner/webhooks)

## External Resources

- [Postman Collection](https://documenter.getpostman.com/view/49178248/2sB3QML9Jg) - Interactive API testing
- [API Terms & Conditions](/api-terms) - Usage terms and policies
