# NovaMed Internal API Documentation

**Last Updated**: December 2025  
**Status**: Internal Use Only  
**Base URL**: `https://api.nimbus-os.com/api`

---

## 📋 Overview

This document outlines all internal APIs used by the NovaMed system. These APIs are **not exposed to partners** and are for internal team use only.

---

## 🔐 Authentication

Most internal APIs use JWT Bearer token authentication:

```
Authorization: Bearer <jwt-token>
```

Some endpoints use role-based access control:
- `super_admin` - Full system access
- `admin` - Clinic admin access
- `practitioner` - Healthcare practitioner access
- `practitioner_staff` - Practitioner staff access
- `clinic_staff` - Clinic staff access

---

## 📚 API Endpoints by Category

### 1. Authentication (`/api/auth`)

#### Register User
- **POST** `/api/auth/register`
- **Description**: Register a new user account
- **Access**: Public

#### Login
- **POST** `/api/auth/login`
- **Description**: Authenticate user and receive JWT token
- **Access**: Public

#### Logout
- **POST** `/api/auth/logout`
- **Description**: Logout user and invalidate token
- **Access**: Private (authenticated users)

#### Refresh Token
- **POST** `/api/auth/refresh`
- **Description**: Refresh access token
- **Access**: Public (with refresh token)

#### Forgot Password
- **POST** `/api/auth/forgot-password`
- **Description**: Request password reset email
- **Access**: Public

#### Reset Password
- **POST** `/api/auth/reset-password`
- **Description**: Reset password with reset token
- **Access**: Public

#### Verify Email
- **POST** `/api/auth/verify-email`
- **Description**: Verify user email address
- **Access**: Public

---

### 2. Super Admin (`/api/super-admin`)

#### Login
- **POST** `/api/super-admin/login`
- **Description**: Super admin authentication
- **Access**: Public

#### Forgot Password
- **POST** `/api/super-admin/forgot-password`
- **Description**: Request password reset
- **Access**: Public

#### Reset Password
- **POST** `/api/super-admin/reset-password/:resetToken`
- **Description**: Reset super admin password
- **Access**: Public

#### Clinic Management
- **GET** `/api/super-admin/clinics` - Get all clinics (paginated)
- **POST** `/api/super-admin/clinics` - Create new clinic
- **GET** `/api/super-admin/clinics/:id` - Get clinic by ID
- **PUT** `/api/super-admin/clinics/:id` - Update clinic
- **DELETE** `/api/super-admin/clinics/:id` - Delete clinic (soft delete)
- **Access**: Super admin only

#### Organization Management
- **GET** `/api/super-admin/organizations` - Get all organizations (paginated)
- **GET** `/api/super-admin/organizations/list` - Get organizations list (for dropdowns)
- **GET** `/api/super-admin/organizations/:id` - Get organization by ID
- **POST** `/api/super-admin/organizations` - Create new organization
- **PUT** `/api/super-admin/organizations/:id` - Update organization
- **DELETE** `/api/super-admin/organizations/:id` - Delete organization (soft delete)
- **Access**: Super admin only

#### States Management
- **GET** `/api/super-admin/states` - Get all states
- **Access**: Super admin only

---

### 3. Clinic Management (`/api/clinic`)

#### Authentication
- **POST** `/api/clinic/login` - Clinic admin login
- **GET** `/api/clinic/me` - Get logged-in clinic admin profile
- **Access**: Public (login), Private (me)

#### Patient Management
- **POST** `/api/clinic/save-patient` - Save new patient
- **POST** `/api/clinic/patients/list` - Get patients list (paginated)
- **GET** `/api/clinic/patients/:patientId` - Get patient by ID
- **GET** `/api/clinic/patients/:patientId/prescriptions` - Get patient prescriptions (paginated)
- **Access**: Clinic admin, practitioner, clinic staff

#### Practitioner Management
- **GET** `/api/clinic/:clinicId/practitioners` - Get clinic practitioners
- **Access**: Clinic admin, practitioner

#### Prescription Management
- **GET** `/api/clinic/:clinicId/prescriptions` - Get clinic prescriptions (paginated)
- **POST** `/api/clinic/patients/save-medication-request` - Save medication request
- **Access**: Clinic admin, practitioner, clinic staff

#### SOAP Notes
- **POST** `/api/clinic/soap-notes` - Create SOAP note
- **GET** `/api/clinic/soap-notes` - Get SOAP notes (paginated)
- **GET** `/api/clinic/soap-notes/:id` - Get SOAP note by ID
- **PUT** `/api/clinic/soap-notes/:id` - Update SOAP note
- **DELETE** `/api/clinic/soap-notes/:id` - Delete SOAP note
- **Access**: Clinic admin, practitioner, clinic staff

#### Clinic Users
- **POST** `/api/clinic/users` - Create clinic user
- **GET** `/api/clinic/users` - Get clinic users
- **GET** `/api/clinic/users/:id` - Get clinic user by ID
- **PUT** `/api/clinic/users/:id` - Update clinic user
- **DELETE** `/api/clinic/users/:id` - Delete clinic user
- **Access**: Clinic admin

---

### 4. Practitioner Management (`/api/practitioners`)

#### Profile
- **GET** `/api/practitioners/profile` - Get practitioner profile
- **Access**: Practitioner

#### Clinics
- **GET** `/api/practitioners/clinics` - Get practitioner's clinics
- **Access**: Practitioner

#### Statistics
- **GET** `/api/practitioners/stats` - Get practitioner statistics
- **Access**: Practitioner

#### Patients
- **GET** `/api/practitioners/patients/:patientId` - Get patient by ID
- **GET** `/api/practitioners/:practitionerId/patients` - Get practitioner's patients (paginated)
- **GET** `/api/practitioners/:practitionerId/patients/simple` - Get practitioner's patients (simple list)
- **Access**: Practitioner, super admin

#### Prescriptions
- **GET** `/api/practitioners/:practitionerId/prescriptions` - Get practitioner's prescriptions (paginated)
- **Access**: Practitioner, super admin

#### Invitations
- **POST** `/api/practitioners/:practitionerId/invite` - Invite practitioner
- **Access**: Super admin only

---

### 5. Prescriptions (`/api/prescriptions`)

#### Search
- **GET** `/api/prescriptions/search` - Search prescriptions with advanced filtering
- **Access**: Practitioner, super admin

#### Get Prescription
- **GET** `/api/prescriptions/:prescriptionId` - Get prescription by ID with full details
- **Access**: Practitioner, super admin

---

### 6. Pharmacy Operations (`/api/pharmacy`)

#### Authentication
- **POST** `/api/pharmacy/login` - Pharmacy user login
- **POST** `/api/pharmacy/logout` - Pharmacy user logout
- **GET** `/api/pharmacy/me` - Get pharmacy user profile
- **POST** `/api/pharmacy/forgot-password` - Request password reset
- **POST** `/api/pharmacy/reset-password/:token` - Reset password
- **Access**: Public (login, forgot password), Private (others)

#### Prescriptions
- **POST** `/api/pharmacy/prescriptions` - Get prescriptions sent to pharmacy
- **GET** `/api/pharmacy/prescriptions/:id` - Get prescription details
- **POST** `/api/pharmacy/prescriptions/:id/acknowledge` - Acknowledge prescription receipt
- **POST** `/api/pharmacy/prescriptions/:id/fill` - Fill prescription
- **PATCH** `/api/pharmacy/prescriptions/:id/status` - Update prescription status
- **POST** `/api/pharmacy/prescriptions/verify-shipment` - Verify shipment
- **Access**: Pharmacy users

#### Refill Requests
- **POST** `/api/pharmacy/refill-requests` - Get refill requests (paginated)
- **GET** `/api/pharmacy/refill-requests/:id` - Get refill request details
- **Access**: Pharmacy users

#### Inventory
- **GET** `/api/pharmacy/inventory` - Get pharmacy inventory
- **PUT** `/api/pharmacy/inventory/:medicationId` - Update medication stock
- **POST** `/api/pharmacy/inventory/validate` - Validate medication availability
- **Access**: Pharmacy users

#### Shipping
- **GET** `/api/pharmacy/shipments/delivery-options` - Get delivery options
- **POST** `/api/pharmacy/shipments/list` - Get all shipments (paginated)
- **GET** `/api/pharmacy/shipments/:id` - Get shipment details
- **POST** `/api/pharmacy/shipments` - Create new shipment
- **POST** `/api/pharmacy/shipments/:id/cancel` - Cancel shipment
- **PATCH** `/api/pharmacy/shipments/:id/status` - Update shipment status
- **PATCH** `/api/pharmacy/shipments/:id/tracking` - Update tracking information
- **GET** `/api/pharmacy/shipments/label/:id` - Get shipment label
- **Access**: Pharmacy users

#### User Management
- **POST** `/api/pharmacy/users` - Create pharmacy user
- **GET** `/api/pharmacy/users` - Get pharmacy users
- **GET** `/api/pharmacy/users/:id` - Get pharmacy user by ID
- **PUT** `/api/pharmacy/users/:id` - Update pharmacy user
- **DELETE** `/api/pharmacy/users/:id` - Delete pharmacy user
- **POST** `/api/pharmacy/users/:id/resend-invitation` - Resend invitation
- **Access**: Pharmacy admin

#### Invitations (Public)
- **POST** `/api/pharmacy/validate-invitation` - Validate invitation token
- **POST** `/api/pharmacy/accept-invitation` - Accept invitation
- **GET** `/api/pharmacy/check-invitation/:acceptToken` - Check invitation status
- **Access**: Public

---

### 7. Payments (`/api/payments`)

#### Payment Accounts
- **POST** `/api/payments/clinics/:clinicId/payment-account` - Setup clinic payment account
- **GET** `/api/payments/clinics/:clinicId/payment-account` - Get clinic payment account
- **POST** `/api/payments/clinics/:clinicId/bank-account` - Add bank account
- **Access**: Clinic admin

#### Payment Processing
- **POST** `/api/payments/process` - Process payment for prescription
- **POST** `/api/payments/:transactionId/refund` - Process refund
- **Access**: Clinic admin, pharmacy

#### Transactions
- **GET** `/api/payments/transactions` - List transactions (paginated)
- **GET** `/api/payments/transactions/:transactionId/receipt` - Download transaction receipt
- **GET** `/api/payments/stats` - Get payment statistics
- **Access**: Clinic admin, pharmacy

#### Payouts
- **GET** `/api/payments/clinics/:clinicId/balance` - Get merchant balance
- **POST** `/api/payments/clinics/:clinicId/payouts` - Create payout
- **Access**: Clinic admin

#### Reports
- **GET** `/api/payments/reports/generate` - Generate payment reports (daily/weekly/monthly)
- **GET** `/api/payments/reports/clinics/:clinicId/payout` - Get clinic payout report
- **GET** `/api/payments/reports/periods` - Get available report periods
- **Access**: Clinic admin

#### Webhooks
- **POST** `/api/payments/webhooks/finix` - Finix webhook endpoint (basic auth)
- **Access**: Finix (webhook authentication)

---

### 8. Pharmetika Integration (`/api/pharmetika`)

#### Connection Testing
- **GET** `/api/pharmetika/test-connection` - Test Pharmetika API connection
- **GET** `/api/pharmetika/config` - Get API configuration
- **Access**: Super admin

#### Practitioner Sync
- **POST** `/api/pharmetika/sync-practitioners` - Sync all practitioners TO Pharmetika
- **POST** `/api/pharmetika/sync-practitioner/:id` - Sync single practitioner TO Pharmetika
- **GET** `/api/pharmetika/practitioner/:id` - Get practitioner from Pharmetika (with optional sync)
- **GET** `/api/pharmetika/practitioners/sync-status` - Get sync status for all practitioners
- **POST** `/api/pharmetika/sync-practitioners/clinic/:clinicId` - Sync practitioners by clinic TO Pharmetika
- **POST** `/api/pharmetika/sync-from-pharmetika` - Sync practitioners FROM Pharmetika INTO our system
- **POST** `/api/pharmetika/sync-practitioner-from-pharmetika/:entityId` - Sync single practitioner FROM Pharmetika
- **GET** `/api/pharmetika/practitioners-from-pharmetika` - Get practitioners from Pharmetika (without syncing)
- **Access**: Super admin

#### Clinic Operations
- **POST** `/api/pharmetika/clinic/test` - Test clinic creation (mock)
- **POST** `/api/pharmetika/clinic` - Create clinic in Pharmetika
- **GET** `/api/pharmetika/clinic/:id` - Get clinic by ID
- **Access**: Super admin

#### Practitioner Operations
- **POST** `/api/pharmetika/practitioner/test` - Test practitioner creation (mock)
- **POST** `/api/pharmetika/practitioner` - Create practitioner in Pharmetika
- **Access**: Super admin

#### Patient Operations
- **POST** `/api/pharmetika/patient/test` - Test patient creation (mock)
- **POST** `/api/pharmetika/patient` - Create patient in Pharmetika
- **GET** `/api/pharmetika/patient/:id` - Get patient by ID
- **Access**: Super admin

#### Medication Operations
- **POST** `/api/pharmetika/medication/validate` - Validate medication order
- **POST** `/api/pharmetika/medication/submit` - Submit medication order
- **GET** `/api/pharmetika/medication/order/:id` - Get medication order by ID
- **POST** `/api/pharmetika/medication/order/:id/cancel` - Cancel medication order
- **Access**: Super admin

#### Webhooks
- **POST** `/api/pharmetika/webhook` - Receive webhooks from Pharmetika
- **Access**: Pharmetika (webhook authentication)

---

### 9. RPA Workflows (`/api/rpa`)

#### Practitioner RPA
- **POST** `/api/rpa/practitioner/success` - RPA workflow success callback
- **PATCH** `/api/rpa/practitioner/:practitionerId/status` - Update practitioner RPA status
- **Access**: Public (RPA system)

**Note**: RPA endpoints are called by the RPA automation system, not by internal users directly.

---

### 10. Products (`/api/products`)

#### Product Management
- **GET** `/api/products` - Get products (paginated)
- **POST** `/api/products` - Create product
- **GET** `/api/products/:id` - Get product by ID
- **PUT** `/api/products/:id` - Update product
- **DELETE** `/api/products/:id` - Delete product
- **Access**: Super admin, clinic admin

---

### 11. Programs (`/api/programs`)

#### Program Management
- **GET** `/api/programs` - Get health programs (paginated)
- **POST** `/api/programs` - Create program
- **GET** `/api/programs/:id` - Get program by ID
- **PUT** `/api/programs/:id` - Update program
- **DELETE** `/api/programs/:id` - Delete program
- **Access**: Super admin, clinic admin

---

### 12. Subscriptions (`/api/subscriptions`)

#### Subscription Management
- **GET** `/api/subscriptions` - Get subscriptions (paginated)
- **POST** `/api/subscriptions` - Create subscription
- **GET** `/api/subscriptions/:id` - Get subscription by ID
- **PUT** `/api/subscriptions/:id` - Update subscription
- **DELETE** `/api/subscriptions/:id` - Cancel subscription
- **Access**: Clinic admin, practitioner

---

### 13. Roles (`/api/roles`)

#### Role Management
- **GET** `/api/roles` - Get all roles
- **POST** `/api/roles` - Create role
- **GET** `/api/roles/:id` - Get role by ID
- **PUT** `/api/roles/:id` - Update role
- **DELETE** `/api/roles/:id` - Delete role
- **Access**: Super admin

---

### 14. States (`/api/states`)

#### State Lookup
- **GET** `/api/states` - Get all states (paginated)
- **GET** `/api/states/list` - Get states list (for dropdowns)
- **GET** `/api/states/code/:code` - Get state by code
- **GET** `/api/states/:id` - Get state by ID
- **Access**: Public (read), Super admin (write)

---

### 15. Storefront (`/api/storefront`)

#### Public Storefront
- **GET** `/api/storefront/programs` - Get all programs (public)
- **GET** `/api/storefront/programs/:id` - Get program details (public)
- **GET** `/api/storefront/programs/search` - Search programs (public)
- **Access**: Public (no authentication required)

---

### 16. SMART FHIR (`/api/smart-fhir`)

#### FHIR Integration
- **GET** `/api/smart-fhir/launch` - SMART on FHIR launch
- **POST** `/api/smart-fhir/token` - Exchange authorization code for token
- **GET** `/api/smart-fhir/patient` - Get patient data
- **Access**: Public (FHIR authentication)

---

### 17. Notifications (`/api/notifications`)

#### Notification Management
- **GET** `/api/notifications` - Get notifications (paginated)
- **POST** `/api/notifications` - Create notification
- **PUT** `/api/notifications/:id/read` - Mark notification as read
- **DELETE** `/api/notifications/:id` - Delete notification
- **Access**: Authenticated users

---

### 18. Health Checks (`/api/health`)

#### Health Endpoints
- **GET** `/api/health` - Basic health check
- **GET** `/api/health/detailed` - Detailed health check
- **GET** `/api/health/ready` - Kubernetes readiness probe
- **GET** `/api/health/live` - Kubernetes liveness probe
- **Access**: Public

---

## 🔄 Data Flow

### Practitioner Onboarding Flow
1. Practitioner created via external API or admin panel
2. RPA workflow triggered automatically
3. RPA completes onboarding steps
4. RPA calls `/api/rpa/practitioner/success` when complete
5. Practitioner status updated to "active"
6. Webhook sent to partners (if configured)

### Prescription Flow
1. Medication request created via clinic portal or external API
2. Prescription created with "pending" status
3. Prescription validated via Pharmetika
4. Prescription submitted to Pharmetika
5. Prescription sent to pharmacy
6. Pharmacy fills prescription
7. Shipment created
8. Shipment shipped
9. Webhooks sent to partners at each stage

---

## 🔔 Webhook Events (Internal)

### Practitioner Events
- `practitioner:activated` - Sent when practitioner is activated

### Shipment Events
- `shipment:created` - Sent when shipment is created
- `shipment:cancelled` - Sent when shipment is cancelled

### Medication Events
- `medication:updated` - Sent when medication status changes
  - Sub-events: `validated`, `submitted`, `filled`, `shipped`, `delivered`, `cancelled`

---

## 📊 Database Models

Key models used across APIs:
- `User` - User accounts
- `Clinic` - Clinic/organization records
- `Practitioner` - Healthcare practitioner records
- `Patient` - Patient records
- `Prescription` - Prescription records
- `MedicationRequest` - Individual medication items
- `MedicationOrder` - Order records
- `RefillRequest` - Refill requests
- `Shipment` - Shipment records
- `Transaction` - Payment transactions
- `ExternalWebhook` - Partner webhook configurations
- `ExternalAPIKey` - Partner API keys

---

## 🛠️ Integration Points

### Pharmetika Integration
- Base URL: `https://testlakehills.pharmetika.com/api/v5`
- Authentication: Basic Auth + `x-pmk-authentication-token` header
- Used for: Pharmacy operations, patient/practitioner sync

### Finix Integration
- Payment processing platform
- Marketplace model with sub-merchants
- Webhook integration for payment events

### RPA Integration
- Automated practitioner onboarding
- Webhook callbacks for workflow completion
- Status updates via API

---

## 📝 Notes

- All timestamps are in ISO 8601 format (UTC)
- UUIDs are used for all IDs
- Pagination uses `page` and `limit` parameters
- Soft deletes are used (records marked as deleted, not removed)
- Most endpoints require authentication via JWT Bearer token
- Role-based access control enforced on protected endpoints

---

## 🚨 Important Reminders

1. **These APIs are INTERNAL ONLY** - Do not expose to partners
2. **Authentication Required** - Most endpoints require JWT tokens
3. **Role-Based Access** - Check user roles before accessing endpoints
4. **Error Handling** - All endpoints return standardized error responses
5. **Rate Limiting** - Be aware of rate limits on external integrations
6. **Webhooks** - Internal webhooks are separate from partner webhooks

---

**Last Updated**: December 2025  
**Maintained By**: Engineering Team  
**Questions**: Contact engineering team
