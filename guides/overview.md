---
title: Overview
description: Introduction to the NovaMed Partner API
---

# Overview

The NovaMed Integration API is a set of HTTP endpoints that help your app integrate with NovaMed for complete patient care management, prescription fulfillment, and order tracking.

## Base URLs

<div class="env-table">
  <div class="env-row">
    <span class="env-label">Development</span>
    <code class="env-url">https://novamed-feapidev.stackmod.info</code>
  </div>
  <div class="env-row">
    <span class="env-label">Production</span>
    <code class="env-url">https://feapi.novamed.care</code>
  </div>
</div>

## Request and Response Format

All API access is over HTTPS. All data is sent and received as JSON.

**Required Headers:**

| Header | Value | Description |
|--------|-------|-------------|
| `Content-Type` | `application/json` | Request body format |
| `Accept` | `application/json` | Expected response format |
| `x-api-key` | Your API key | Authentication |

## Key Capabilities

### Practitioners

Create and manage practitioners (doctors, veterinarians) in the NovaMed system.

- Onboard new practitioners with credentials
- Assign practitioners to clinics
- Manage practitioner details and signatures

### Patients

Register and manage patients in the NovaMed platform.

- Create patient profiles with demographics
- Manage patient contact and address information
- Link patients to clinics

### Medication Requests

Create and manage medication orders for patients.

- Submit medication requests with prescribing practitioner
- Specify medication details, dosing, and refills
- Track medication order status

### Refills

Request prescription refills for existing medication orders.

- Initiate refill requests for shipped/delivered medications
- Automatic eligibility checking
- Track refill status through fulfillment

### Webhooks

Receive real-time notifications for important events.

- Shipment creation and updates
- Medication order status changes
- Prescription status updates

## Use Cases

### Pharmacy Management Systems

Integrate order creation and tracking into existing pharmacy workflows. Automate fulfillment processes and reduce manual data entry.

### Patient Portals

Enable patients to request refills and track orders through your application. Provide real-time status updates and delivery estimates.

### Care Coordination Platforms

Connect pharmacy operations with care management systems. Automate refill requests based on patient needs and care plans.

### Telemedicine Platforms

Integrate prescription workflows into virtual care. Create medication orders directly from consultations.

## Architecture

The NovaMed API follows RESTful principles with JSON request and response bodies. All endpoints use HTTPS and require API key authentication.

### Authentication

All requests require an API key sent in the `x-api-key` header. You'll also need a Clinic ID for most operations.

[Learn more about authentication →](/guides/authentication)

## HIPAA & Security

The NovaMed API handles Protected Health Information (PHI) and is designed to comply with HIPAA requirements. All data is encrypted in transit using TLS 1.2 or higher.

When building integrations:

- Store API keys securely
- Use HTTPS for all requests
- Implement proper access controls
- Follow your organization's HIPAA compliance policies

**Important**: API access is limited to Approved Partners only. Production access involving PHI requires a Business Associate Agreement (BAA). Do not send PHI to the Development environment. See [API Terms & Conditions](/api-terms) for complete requirements.

## Getting Started

Ready to start building? Follow our [Quickstart guide](/guides/quickstart) to make your first API call in minutes.

## Support

Need help? Contact our API support team at [api@nimbus-os.com](mailto:api@nimbus-os.com).
