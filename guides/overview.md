---
title: Overview
description: Introduction to the Nimbus OS API
---

# Overview

The Nimbus OS API provides programmatic access to pharmacy operations, order management, and patient care workflows. Build integrations that automate prescription fulfillment, manage refills, and receive real-time updates.

## Key Capabilities

### Order Management

Create and track pharmacy orders programmatically. The API handles the complete order lifecycle from creation through fulfillment and delivery.

- Create orders with idempotency support
- Retrieve order details and status
- Track shipments with carrier integration

### Refill Workflows

Automate prescription refill requests with eligibility checking and approval workflows.

- Initiate refill requests
- Check refill eligibility
- Track refill status through fulfillment

### Real-time Updates

Receive webhook notifications for important events in your integration.

- Order status changes
- Refill approvals
- Shipment tracking updates

## Use Cases

### Pharmacy Management Systems

Integrate order creation and tracking into existing pharmacy workflows. Automate fulfillment processes and reduce manual data entry.

### Patient Portals

Enable patients to request refills and track orders through your application. Provide real-time status updates and delivery estimates.

### Care Coordination Platforms

Connect pharmacy operations with care management systems. Automate refill requests based on patient needs and care plans.

## Architecture

The Nimbus OS API follows RESTful principles with JSON request and response bodies. All endpoints use HTTPS and require API key authentication.

### Base URLs

- **Sandbox**: `https://api-sandbox.nimbus-os.com`
- **Production**: `https://api.nimbus-os.com`

### Authentication

All requests require an API key sent in the `X-API-Key` header. [Learn more about authentication →](/guides/authentication)

### Rate Limits

API rate limits ensure fair usage across all integrations. Limits vary by endpoint and are documented in the [API Reference](/api-reference).

## HIPAA & Security

The Nimbus OS API handles Protected Health Information (PHI) and is designed to comply with HIPAA requirements. All data is encrypted in transit using TLS 1.2 or higher.

When building integrations:

- Store API keys securely
- Use HTTPS for all requests
- Implement proper access controls
- Follow your organization's HIPAA compliance policies

**Important**: API access is limited to Approved Partners only. Production access involving PHI requires a Business Associate Agreement (BAA). Do not send PHI to Sandbox. See [API Terms & Conditions](/api-terms) for complete requirements.

## Getting Started

Ready to start building? Follow our [Quickstart guide](/guides/quickstart) to make your first API call in minutes.

## Support

Need help? Contact our API support team at [api@nimbus-os.com](mailto:api@nimbus-os.com).
