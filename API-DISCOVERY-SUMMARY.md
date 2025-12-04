# NovaMed API Discovery Summary

**Date**: December 2025  
**Discovery Source**: `/Users/jobbyjohn/Documents/NovaMed-FoundersBox/novamed-api`

---

## 🎯 Executive Summary

I've analyzed the NovaMed API codebase and identified **6 core endpoints** that partners need access to. These endpoints enable partners to integrate with NovaMed's pharmacy operations for practitioner management, patient records, medication orders, and refills.

---

## 📋 Discovered APIs

### External API Endpoints (`/api/external`)

1. **POST `/api/external/practitioner`** - Create healthcare practitioner
2. **POST `/api/external/patient`** - Create patient record
3. **POST `/api/external/medication-order`** - Submit prescription order
4. **POST `/api/external/refill-request`** - Request prescription refill
5. **POST `/api/external/webhook`** - Register webhook endpoint
6. **DELETE `/api/external/webhook/:id`** - Remove webhook

### Authentication
- **Method**: API Key via `X-API-Key` header
- **Middleware**: `external.auth.js` validates API keys from `ExternalAPIKey` table

---

## 🔍 Key Findings

### 1. **Practitioner Management**
- Creates practitioners with "pending" status
- Requires NPI (10 digits), clinic assignment, and address
- Triggers RPA workflow for onboarding
- Supports both human and veterinary practitioners

### 2. **Patient Management**
- Creates patients with full demographic data
- Syncs to Pharmetika system automatically
- Supports multiple contact methods and addresses
- Includes duplicate detection
- Supports veterinary patients (with owner information)

### 3. **Medication Orders**
- Validates orders via Pharmetika before creation
- Supports multiple medications per order
- Includes delivery speed options (next-day, business-day)
- Handles compounding, flavors, and special instructions
- Creates prescription and medication order records

### 4. **Refill Requests**
- Only available for shipped/delivered medications
- Prevents duplicate refill requests
- Automatically creates new prescription and order
- Uses original medication details

### 5. **Webhooks**
- Simple registration system
- Clinic-scoped webhooks
- Soft-delete functionality

---

## 📊 Data Flow

```
Partner System
    ↓
External API (/api/external)
    ↓
NovaMed API (validation & processing)
    ↓
Pharmetika Integration (pharmacy operations)
    ↓
RPA Workflow (practitioner onboarding)
```

---

## 🛠️ Technical Details

### Validation
- Uses **Joi** for request validation
- Validators located in `src/validators/external/`
- Comprehensive field-level validation with custom messages

### Database Models
- `Practitioner` - Healthcare provider records
- `Patient` - Patient records
- `Prescription` - Prescription records
- `MedicationRequest` - Individual medication items
- `MedicationOrder` - Order records
- `RefillRequest` - Refill requests
- `ExternalAPIKey` - Partner API keys
- `ExternalWebhook` - Webhook configurations

### Integration Points
- **Pharmetika Service**: Handles pharmacy operations
- **RPA Service**: Handles practitioner onboarding automation
- **State Management**: US states lookup for validation

---

## 📝 Documentation Status

### ✅ Completed
- [x] API discovery and analysis
- [x] Endpoint identification
- [x] Request/response schema documentation
- [x] Validation rules documentation
- [x] Error handling patterns
- [x] OpenAPI specification plan

### 🔄 Next Steps
- [ ] Create OpenAPI 3.1 specification file
- [ ] Write integration guides
- [ ] Add code examples (cURL, JavaScript, Python)
- [ ] Document webhook events and payloads
- [ ] Test all endpoints against sandbox
- [ ] Deploy to developer portal

---

## 📚 Documentation Files Created

1. **NOVAMED-API-DOCUMENTATION-PLAN.md**
   - Complete API documentation plan
   - OpenAPI spec structure
   - Schema definitions
   - Implementation checklist

2. **ENGINEERING-HANDOFF.md**
   - Handoff document for engineering team
   - Next steps and priorities
   - Testing procedures

---

## 🎯 Recommended Implementation Order

### Phase 1: Core Documentation (Week 1)
1. Create OpenAPI spec for all 6 endpoints
2. Document request/response schemas
3. Add example requests/responses
4. Document authentication

### Phase 2: Guides & Examples (Week 2)
1. Quickstart guide
2. Authentication guide
3. Practitioner management guide
4. Patient management guide
5. Medication order guide
6. Refill guide
7. Webhook integration guide

### Phase 3: Code Examples (Week 2-3)
1. cURL examples
2. JavaScript/Node.js examples
3. Python examples
4. Error handling examples

### Phase 4: Testing & Deployment (Week 3)
1. Test all endpoints in sandbox
2. Validate OpenAPI spec
3. Review documentation accuracy
4. Deploy to developer portal
5. Gather partner feedback

---

## 🔗 Related Files

- **API Routes**: `novamed-api/src/routes/external.js`
- **Controllers**: `novamed-api/src/controllers/external.controller.js`
- **Validators**: `novamed-api/src/validators/external/`
- **Auth Middleware**: `novamed-api/src/middleware/external.auth.js`

---

## 💡 Key Insights

1. **API is well-structured** with clear separation of concerns
2. **Validation is comprehensive** using Joi schemas
3. **Error handling is consistent** with standard error response format
4. **Integration with Pharmetika** is abstracted from partners
5. **RPA workflow** is automated and transparent to partners
6. **Webhook system** is simple but may need enhancement for production

---

## ⚠️ Areas Needing Clarification

1. **Webhook Events**: What events are sent? What's the payload format?
2. **Rate Limiting**: Are there rate limits? What are they?
3. **API Key Management**: How are API keys generated? Can partners rotate them?
4. **Error Codes**: Are there specific error codes beyond HTTP status codes?
5. **Order Status**: How do partners track order status? Polling or webhooks?
6. **Sandbox Data**: Is there test data available in sandbox?

---

## 📞 Next Actions

1. **Review** `NOVAMED-API-DOCUMENTATION-PLAN.md` with engineering
2. **Prioritize** endpoints based on partner needs
3. **Clarify** webhook events and payloads
4. **Create** OpenAPI specification
5. **Write** integration guides
6. **Test** documentation against sandbox
7. **Deploy** to developer portal

---

**Last Updated**: December 2025  
**Status**: Discovery Complete - Ready for Documentation
