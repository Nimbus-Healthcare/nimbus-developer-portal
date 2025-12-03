---
title: Nimbus OS Developer Portal
description: Build with the operating system for personalized medicine
---

# Nimbus OS API

Build powerful healthcare integrations with the Nimbus OS API. Create orders, manage refills, and receive real-time updates through webhooks.

## Get Started

Start building in minutes with our simple API key authentication and comprehensive documentation.

[Get Started →](/guides/quickstart)

## Base URLs

Use the appropriate base URL for your environment:

**Sandbox**
```
https://api-sandbox.nimbus-os.com
```

**Production**
```
https://api.nimbus-os.com
```

## Authentication

All API requests require an API key in the `X-API-Key` header:

```bash
curl https://api-sandbox.nimbus-os.com/orders \
  -H "X-API-Key: your-api-key-here" \
  -H "Content-Type: application/json"
```

[Learn more about authentication →](/guides/authentication)

## Quick Links

<div class="card-grid">
  <div class="card">
    <h3>Quickstart</h3>
    <p>Get your first API call working in minutes.</p>
    <a href="/guides/quickstart">View guide →</a>
  </div>
  
  <div class="card">
    <h3>API Reference</h3>
    <p>Complete endpoint documentation with examples.</p>
    <a href="/api-reference">View reference →</a>
  </div>
  
  <div class="card">
    <h3>Webhooks</h3>
    <p>Receive real-time event notifications.</p>
    <a href="/guides/webhooks">Learn more →</a>
  </div>
</div>

## What You Can Build

- **Order Management**: Create and track pharmacy orders programmatically
- **Refill Workflows**: Automate prescription refill requests
- **Real-time Updates**: Receive webhook notifications for order status changes
- **HIPAA-Compliant**: Built with healthcare data security in mind

## Next Steps

1. [Get your API key](/guides/quickstart#get-your-api-key)
2. [Make your first request](/guides/quickstart#make-your-first-request)
3. [Set up webhooks](/guides/webhooks)
4. [Explore the API reference](/api-reference)

---

## API Terms

**Nimbus OS API Terms (Approved Partners Only).** By accessing Nimbus Healthcare Corporation APIs ("Nimbus OS", including Nimbus Healthcare, NovaMed) you agree to these terms. Access is by written approval only; credentials are unique per tenant and must be protected. APIs may process PHI; production PHI access requires a BAA. Do not send PHI to Sandbox. No scraping, reverse engineering, benchmarking, resale/sublicensing (except serving authorized customers), or security testing without written approval. No PCI/card data. Nimbus may change/suspend APIs; best efforts notice. APIs "as is"; liability capped to fees paid in prior 3 months. Notices: legal@nimbushealthcare.com. Travis County, TX.

[View full API License Terms & Conditions →](/api-terms)

---

Need help? Contact [api@nimbus-os.com](mailto:api@nimbus-os.com)


