---
title: NovaMed Partner API Documentation
description: The NovaMed Integration API helps your app integrate with NovaMed
---

# NovaMed <span class="accent">Partner API</span>

The NovaMed Integration API is a set of HTTP endpoints that help your app integrate with NovaMed for complete patient care management, prescription fulfillment, and order tracking.

<div class="info-card">
  <div class="info-card-icon">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"/><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"/></svg>
  </div>
  <div class="info-card-content">
    <h4>Postman Documentation</h4>
    <a href="https://documenter.getpostman.com/view/49178248/2sB3QML9Jg" target="_blank">View in Postman →</a>
  </div>
</div>

## Base URLs

Use the appropriate base URL for your environment:

<div class="env-table">
  <div class="env-row">
    <span class="env-label">Development</span>
    <code class="env-url">https://novamed-feapidev.nimbushealthcaretest.com</code>
  </div>
  <div class="env-row">
    <span class="env-label">Production</span>
    <code class="env-url">https://feapi.novamed.care</code>
  </div>
</div>

## Authentication

We will provide an **API Key** and **Clinic Id**. All requests need to pass a header `x-api-key` with the provided value.

```bash
curl https://novamed-feapidev.nimbushealthcaretest.com/api/external/practitioner \
  -H "x-api-key: your-api-key-here" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```

[Learn more about authentication →](/guides/authentication)

## Quick Links

{% cards %}
  {% card title="Quickstart" to="/guides/quickstart" %}
    Get your first API call working in minutes.
  {% /card %}
  
  {% card title="API Reference" to="/api-reference" %}
    Complete endpoint documentation with examples.
  {% /card %}
  
  {% card title="Webhooks" to="/guides/webhooks" %}
    Receive real-time event notifications.
  {% /card %}
{% /cards %}

## API Endpoints

The NovaMed Partner API supports the following core capabilities:

<ol class="workflow-list">
  <li><strong>Practitioners</strong> — Create and manage practitioners (doctors, veterinarians)</li>
  <li><strong>Patients</strong> — Register and manage patients in the platform</li>
  <li><strong>Medication Requests</strong> — Create medication orders for patients</li>
  <li><strong>Refills</strong> — Request prescription refills for existing orders</li>
  <li><strong>Webhooks</strong> — Receive real-time notifications for status updates</li>
</ol>

## Request and Response Format

All API access is over HTTPS. All data is sent and received as JSON.

**Required Headers:**

| Header | Value |
|--------|-------|
| `Content-Type` | `application/json` |
| `Accept` | `application/json` |
| `x-api-key` | Your API key |

## Next Steps

1. [Get your API key](/guides/quickstart#get-your-api-key)
2. [Make your first request](/guides/quickstart#make-your-first-request)
3. [Set up webhooks](/guides/webhooks)
4. [Explore the API reference](/api-reference)

---

## API Terms

**NovaMed API Terms (Approved Partners Only).** By accessing NovaMed APIs you agree to these terms. Access is by written approval only; credentials are unique per tenant and must be protected. APIs may process PHI; production PHI access requires a BAA. Do not send PHI to Development environment. No scraping, reverse engineering, benchmarking, resale/sublicensing (except serving authorized customers), or security testing without written approval. No PCI/card data. NovaMed may change/suspend APIs; best efforts notice. APIs "as is"; liability capped to fees paid in prior 3 months. Notices: legal@nimbushealthcare.com. Travis County, TX.

[View full API License Terms & Conditions →](/api-terms)

---

Need help? Contact [api@nimbus-os.com](mailto:api@nimbus-os.com)
