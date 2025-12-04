# NovaMed API Documentation Plan for Partner Portal

**Date**: December 2025  
**Purpose**: Guide for documenting NovaMed APIs in the Nimbus OS Developer Portal  
**Base Path**: `/api/external`

---

## 🔍 API Overview

The NovaMed External API provides partners with programmatic access to:
- **Practitioner Management**: Create and manage healthcare practitioners
- **Patient Management**: Create and manage patient records
- **Medication Orders**: Submit prescription orders for fulfillment
- **Refill Requests**: Request prescription refills
- **Webhooks**: Configure webhook endpoints for real-time updates

**Authentication**: API Key via `X-API-Key` header

---

## 📋 API Endpoints to Document

### 1. **Create Practitioner**
**Endpoint**: `POST /api/external/practitioner`  
**Purpose**: Register a new healthcare practitioner in the NovaMed system

**Request Schema**:
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "npi": "1234567890",
  "assigned_clinic": "uuid-of-clinic",
  "is_veterinarian": false,
  "phone": "+15551234567",
  "address_line_1": "123 Main St",
  "address_line_2": "Suite 100",
  "city": "Austin",
  "state_code": "TX",
  "zip_code": "78701"
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "practitioner_id": "uuid",
    "status": "Pending verification"
  },
  "message": "Practitioner created successfully"
}
```

**Key Details**:
- NPI must be exactly 10 digits
- State code must be valid US state code (2-letter abbreviation)
- Practitioner status starts as "pending" until verification
- Triggers RPA workflow for practitioner onboarding

---

### 2. **Create Patient**
**Endpoint**: `POST /api/external/patient`  
**Purpose**: Register a new patient in the NovaMed system

**Request Schema**:
```json
{
  "first_name": "Jane",
  "middle_name": "Marie",
  "last_name": "Smith",
  "dob": "1990-01-15",
  "gender": "female",
  "species": "human",
  "weight": "150",
  "weight_recorded_date": "2024-01-01",
  "height": 165,
  "practitioner_id": "uuid-of-practitioner",
  "assigned_clinic": "uuid-of-clinic",
  "status": "active",
  "medication_allergies": ["penicillin", "sulfa"],
  "medical_history": "Hypertension, Type 2 Diabetes",
  "preferred_contact_methods": {
    "email": true,
    "sms": true,
    "phone": false
  },
  "notes": "Patient prefers morning appointments",
  "contacts": [
    {
      "key": "email",
      "value": "jane.smith@example.com"
    },
    {
      "key": "phone",
      "value": "+15559876543"
    }
  ],
  "addresses": [
    {
      "addressLine1": "456 Oak Ave",
      "addressLine2": "Apt 2B",
      "addressLine3": "",
      "city": "Austin",
      "state": "TX",
      "postalCode": "78702"
    }
  ]
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "patient_id": "uuid"
  },
  "message": "Patient was created successfully."
}
```

**Key Details**:
- At least one address is required (exactly one address allowed)
- Phone numbers must be valid US format (+1XXXXXXXXXX, 1XXXXXXXXXX, or 10 digits)
- Email addresses must be valid format
- Patient is automatically synced to Pharmetika system
- Duplicate detection may return error if patient already exists

**Special Cases**:
- For veterinary patients: Include `animal_owner_name` in request
- For non-human species: Set `species` field appropriately

---

### 3. **Create Medication Order**
**Endpoint**: `POST /api/external/medication-order`  
**Purpose**: Submit a prescription order for pharmacy fulfillment

**Request Schema**:
```json
{
  "patient_id": "uuid-of-patient",
  "practitioner_id": "uuid-of-practitioner",
  "clinic_id": "uuid-of-clinic",
  "delivery_speed": "next-day",
  "is_refrigerated": false,
  "shipment_group_id": "optional-group-id",
  "medication_requests": [
    {
      "medication": "Metformin",
      "dose": "850mg",
      "quantity": "30",
      "direction": "Take 1 tablet by mouth twice daily with meals",
      "refills": "2",
      "days_supply": 30,
      "flavor": null,
      "notes": "Patient prefers generic",
      "do_not_refill": false,
      "do_not_fill": false,
      "patient_contact_requested": false,
      "reason_for_compounding": "N/A",
      "reason_for_compounding_additional_context": "N/A"
    }
  ]
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "medication_order_id": "uuid",
    "medications": [
      {
        "id": "uuid",
        "medication": "Metformin",
        "dose": "850mg",
        "quantity_authorized": "30",
        "days_supply": 30,
        "refills_authorized": 2,
        "sig": "Take 1 tablet by mouth twice daily with meals",
        "date_issued": "2024-01-15T10:00:00.000Z"
      }
    ]
  },
  "message": "Medication order was created successfully."
}
```

**Key Details**:
- `delivery_speed` must be either `"next-day"` or `"business-day"`
- Order is validated against Pharmetika before creation
- If validation fails, prescription is soft-deleted and error returned
- Multiple medications can be included in a single order
- Order status starts as "send_to_pharmacy"

**Validation Rules**:
- Patient must exist and belong to specified clinic
- Practitioner must exist
- Medication order is validated via Pharmetika API
- Invalid orders are rejected with specific error messages

---

### 4. **Create Refill Request**
**Endpoint**: `POST /api/external/refill-request`  
**Purpose**: Request a refill for an existing medication order

**Request Schema**:
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

**Key Details**:
- Only medication requests with status "shipped" or "delivered" can be refilled
- Cannot create refill if one already exists (unless fulfilled/delivered)
- Creates new prescription and medication order automatically
- Uses original medication details from parent prescription

**Error Cases**:
- Medication request not found: 400 error
- Medication request not yet shipped: 400 error
- Refill request already exists: 400 error

---

### 5. **Create Webhook**
**Endpoint**: `POST /api/external/webhook`  
**Purpose**: Register a webhook URL to receive real-time updates

**Request Schema**:
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

**Key Details**:
- Webhook URL must be a valid HTTPS URL
- Clinic must exist
- Webhooks are used for order status updates, shipment notifications, etc.

---

### 6. **Delete Webhook**
**Endpoint**: `DELETE /api/external/webhook/:id`  
**Purpose**: Remove a webhook configuration

**Response**:
```json
{
  "success": true,
  "message": "Webhook deleted successfully"
}
```

**Key Details**:
- Webhook is soft-deleted (not permanently removed)
- Returns 404 if webhook not found

---

## 🔐 Authentication

All external API endpoints require authentication via API key.

**Header**: `X-API-Key: your-api-key-here`

**Error Response** (401 Unauthorized):
```json
{
  "success": false,
  "error": {
    "message": "Unauthorized"
  }
}
```

**How Partners Get API Keys**:
1. Contact Nimbus OS API Support (api@nimbus-os.com)
2. Provide clinic information and use case
3. Receive API key after approval
4. API keys are tied to specific clinics

---

## 📊 Error Handling

### Standard Error Response Format

```json
{
  "success": false,
  "error": {
    "message": "Error description"
  }
}
```

### Common HTTP Status Codes

- **200**: Success
- **400**: Bad Request (validation errors, invalid data)
- **401**: Unauthorized (missing or invalid API key)
- **404**: Not Found (resource doesn't exist)
- **500**: Internal Server Error

### Validation Errors

Validation errors follow Joi schema validation and return specific field-level errors:

```json
{
  "success": false,
  "error": {
    "message": "Validation error",
    "details": [
      {
        "field": "npi",
        "message": "NPI must be exactly 10 digits."
      }
    ]
  }
}
```

---

## 🔄 Webhook Events

Partners can configure webhooks to receive real-time updates. Webhook events include:

### Order Status Updates
- Order created
- Order validated
- Order sent to pharmacy
- Order shipped
- Order delivered
- Order cancelled

### Refill Updates
- Refill requested
- Refill fulfilled
- Refill shipped

**Webhook Payload Format** (to be documented):
```json
{
  "event": "order.shipped",
  "timestamp": "2024-01-15T10:00:00.000Z",
  "data": {
    "order_id": "uuid",
    "status": "shipped",
    "tracking_number": "1Z999AA10123456784"
  }
}
```

**Webhook Security**:
- Webhooks should verify HMAC signature (to be implemented)
- Use HTTPS endpoints only
- Implement idempotency handling

---

## 📝 OpenAPI Specification Structure

### Base Information

```yaml
openapi: 3.1.0
info:
  title: Nimbus OS External API
  version: 1.0.0
  description: |
    External API for partners to integrate with NovaMed pharmacy operations.
    This API enables programmatic access to practitioner management, patient records,
    medication orders, and refill requests.
  
  contact:
    name: Nimbus OS API Support
    email: api@nimbus-os.com
  
  license:
    name: API License Terms & Conditions
    url: https://developer.nimbus-os.com/api-terms

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
      description: API key for partner authentication
```

### Tags

```yaml
tags:
  - name: Practitioners
    description: Healthcare practitioner management
  - name: Patients
    description: Patient record management
  - name: Medication Orders
    description: Prescription order submission and management
  - name: Refills
    description: Prescription refill requests
  - name: Webhooks
    description: Webhook configuration for real-time updates
```

### Example Endpoint Definition

```yaml
paths:
  /practitioner:
    post:
      tags:
        - Practitioners
      summary: Create a new practitioner
      description: |
        Register a new healthcare practitioner in the NovaMed system.
        The practitioner will be created with "pending" status and will
        require verification before they can prescribe medications.
      operationId: createPractitioner
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PractitionerRequest'
            examples:
              humanPractitioner:
                summary: Human healthcare practitioner
                value:
                  first_name: "John"
                  last_name: "Doe"
                  email: "john.doe@example.com"
                  npi: "1234567890"
                  assigned_clinic: "550e8400-e29b-41d4-a716-446655440000"
                  is_veterinarian: false
                  phone: "+15551234567"
                  address_line_1: "123 Main St"
                  address_line_2: "Suite 100"
                  city: "Austin"
                  state_code: "TX"
                  zip_code: "78701"
      responses:
        '200':
          description: Practitioner created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PractitionerResponse'
        '400':
          description: Bad request (validation error or clinic not found)
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Unauthorized (missing or invalid API key)
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
```

### Schema Definitions

```yaml
components:
  schemas:
    PractitionerRequest:
      type: object
      required:
        - first_name
        - last_name
        - email
        - npi
        - assigned_clinic
        - is_veterinarian
        - address_line_1
        - city
        - state_code
        - zip_code
      properties:
        first_name:
          type: string
          minLength: 1
          maxLength: 100
          description: Practitioner's first name
          example: "John"
        last_name:
          type: string
          minLength: 1
          maxLength: 100
          description: Practitioner's last name
          example: "Doe"
        email:
          type: string
          format: email
          description: Practitioner's email address
          example: "john.doe@example.com"
        npi:
          type: string
          pattern: '^\d{10}$'
          description: National Provider Identifier (exactly 10 digits)
          example: "1234567890"
        assigned_clinic:
          type: string
          format: uuid
          description: UUID of the clinic this practitioner is assigned to
          example: "550e8400-e29b-41d4-a716-446655440000"
        is_veterinarian:
          type: boolean
          description: Whether the practitioner is a veterinarian
          example: false
        phone:
          type: string
          maxLength: 20
          description: Practitioner's phone number
          example: "+15551234567"
        address_line_1:
          type: string
          maxLength: 100
          description: Primary address line
          example: "123 Main St"
        address_line_2:
          type: string
          maxLength: 100
          description: Secondary address line (optional)
          example: "Suite 100"
        city:
          type: string
          maxLength: 100
          description: City name
          example: "Austin"
        state_code:
          type: string
          enum:
            - AL
            - AK
            - AZ
            # ... all US state codes
          description: Two-letter US state code
          example: "TX"
        zip_code:
          type: string
          maxLength: 10
          description: ZIP or postal code
          example: "78701"
    
    PractitionerResponse:
      type: object
      properties:
        success:
          type: boolean
          example: true
        data:
          type: object
          properties:
            practitioner_id:
              type: string
              format: uuid
              description: Unique identifier for the created practitioner
            status:
              type: string
              description: Current status of the practitioner
              example: "Pending verification"
        message:
          type: string
          example: "Practitioner created successfully"
    
    ErrorResponse:
      type: object
      properties:
        success:
          type: boolean
          example: false
        error:
          type: object
          properties:
            message:
              type: string
              description: Human-readable error message
              example: "Clinic not found"
```

---

## 🎯 Implementation Checklist

### Phase 1: Core Endpoints
- [ ] Document `/practitioner` endpoint (POST)
- [ ] Document `/patient` endpoint (POST)
- [ ] Document `/medication-order` endpoint (POST)
- [ ] Document `/refill-request` endpoint (POST)
- [ ] Document `/webhook` endpoints (POST, DELETE)

### Phase 2: Schemas & Examples
- [ ] Create PractitionerRequest schema
- [ ] Create PatientRequest schema
- [ ] Create MedicationOrderRequest schema
- [ ] Create RefillRequest schema
- [ ] Create WebhookRequest schema
- [ ] Create all response schemas
- [ ] Add example requests/responses for each endpoint

### Phase 3: Error Documentation
- [ ] Document all error responses
- [ ] Create ErrorResponse schema
- [ ] Document validation error format
- [ ] Add error examples for each endpoint

### Phase 4: Authentication & Security
- [ ] Document API key authentication
- [ ] Document how to obtain API keys
- [ ] Document rate limiting (if applicable)
- [ ] Document webhook security (HMAC signatures)

### Phase 5: Guides & Examples
- [ ] Create Quickstart guide
- [ ] Create Authentication guide
- [ ] Create Practitioner management guide
- [ ] Create Patient management guide
- [ ] Create Medication order guide
- [ ] Create Refill request guide
- [ ] Create Webhook integration guide
- [ ] Add code examples (cURL, JavaScript, Python)

### Phase 6: Testing & Validation
- [ ] Test all endpoints against sandbox
- [ ] Verify all examples work
- [ ] Validate OpenAPI spec
- [ ] Review documentation accuracy

---

## 📚 Additional Documentation Needed

### Integration Guides

1. **Getting Started Guide**
   - How to obtain API keys
   - Setting up authentication
   - Making your first API call
   - Testing in sandbox environment

2. **Practitioner Onboarding Flow**
   - Step-by-step practitioner creation
   - Verification process
   - RPA workflow explanation

3. **Patient Management**
   - Creating patients
   - Handling duplicates
   - Updating patient information
   - Address and contact management

4. **Order Workflow**
   - Creating medication orders
   - Order validation
   - Tracking order status
   - Handling order errors

5. **Refill Process**
   - When refills are available
   - Creating refill requests
   - Refill status tracking

6. **Webhook Integration**
   - Setting up webhooks
   - Webhook event types
   - Webhook payload structure
   - Webhook security (HMAC verification)
   - Handling webhook failures
   - Idempotency best practices

### Code Examples

Provide working examples in:
- **cURL**: Command-line examples
- **JavaScript/Node.js**: Using fetch or axios
- **Python**: Using requests library
- **Go**: Using net/http (optional)

### Best Practices

- Error handling strategies
- Retry logic for failed requests
- Rate limiting considerations
- Idempotency patterns
- Webhook reliability
- Testing strategies

---

## 🔗 Related Systems

### Pharmetika Integration
- NovaMed integrates with Pharmetika for pharmacy operations
- Patient and practitioner data syncs to Pharmetika
- Medication orders are validated and submitted via Pharmetika API
- Partners don't need direct Pharmetika integration

### RPA Workflow
- Practitioner creation triggers RPA workflow
- RPA handles practitioner onboarding automation
- Partners don't need to interact with RPA directly

---

## 📞 Support & Resources

- **API Support Email**: api@nimbus-os.com
- **Documentation**: https://developer.nimbus-os.com
- **Sandbox Environment**: https://api-sandbox.nimbus-os.com
- **Production Environment**: https://api.nimbus-os.com

---

## 🚀 Next Steps

1. **Review this plan** with engineering team
2. **Prioritize endpoints** based on partner needs
3. **Create OpenAPI spec** for each endpoint
4. **Write integration guides** for each feature
5. **Add code examples** in multiple languages
6. **Test documentation** against sandbox environment
7. **Deploy to developer portal** and gather partner feedback

---

**Last Updated**: December 2025  
**Status**: Ready for Implementation
