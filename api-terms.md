---
title: API License Terms & Conditions
description: Terms and conditions for using the Nimbus OS API
---

# NIMBUS OS — API LICENSE TERMS AND CONDITIONS

**Last Updated:** January 15, 2025

Nimbus Healthcare Corporation, a Delaware C corporation, doing business as Nimbus Healthcare, NovaMed, and Nimbus (collectively, "Nimbus OS", "Nimbus," "Company," "we," "us") provides application programming interfaces and related developer resources (collectively, the "API"). These API License Terms and Conditions (the "Agreement") govern access to and use of the API by developers and their organizations ("Developer," "you").

By requesting access, receiving credentials, or using the API, you agree to be bound by this Agreement. If you are using the API on behalf of an entity, you represent that you have authority to bind that entity.

Nimbus may update this Agreement from time to time. Material changes will be effective after notice through the developer portal or other reasonable method, and continued use after the effective date constitutes acceptance.

---

## 1. Definitions

- **"Approved Partner"** means a Developer that has been expressly approved in writing by Nimbus to access the API.

- **"Credentials"** means API keys, tokens, secrets, client identifiers, or other access mechanisms issued by Nimbus.

- **"Sandbox"** means Nimbus-provided non-production environment for testing.

- **"Documentation"** means the API reference, guides, examples, specifications, and related materials provided by Nimbus.

- **"PHI"** has the meaning in HIPAA (as amended).

- **"BAA"** means a business associate agreement, if required under HIPAA.

---

## 2. Approved Partner Access; Developer Account; Credentials

### 2.1 Approved Partners Only

Access to the API is limited to Approved Partners. Nimbus may approve or deny access in its sole discretion.

### 2.2 Registration and Accurate Information

To access the API, you may be required to create a developer account and provide accurate, current information. You will promptly update information as needed.

### 2.3 Credential Security; Tenant Separation

Credentials are issued per customer/tenant (or as Nimbus otherwise determines). You must protect Credentials, keep them confidential, and ensure they are only used by authorized personnel and systems. You are responsible for all usage under your Credentials, including any unauthorized use resulting from your failure to safeguard them.

### 2.4 No Misrepresentation

You will not misrepresent or mask your identity when using the API.

---

## 3. License Grant

Subject to this Agreement, Nimbus grants you a limited, non-exclusive, non-transferable, non-sublicensable, revocable license during the term to:

- access and use the API and Documentation solely to develop, test, and operate your integration with Nimbus OS services for authorized customers; and
- access and use the Sandbox solely for testing and development.

Nimbus may impose limits on features, environments, or API Calls and may modify the API at any time.

---

## 4. Restrictions (IP and Acceptable Use)

You will not, and will not allow any third party to:

### 4.1 Reverse Engineering / Derivation

Reverse engineer, decompile, disassemble, or attempt to derive source code, underlying ideas, algorithms, structure, or organization of the API, Sandbox, or Documentation.

### 4.2 Scraping / Circumvention

Scrape, harvest, crawl, or extract data from Nimbus systems by any means other than the API; bypass or circumvent rate limits, access controls, or intended technical restrictions.

### 4.3 Benchmarking / Competitive Use

Use the API to benchmark, compare, or test Nimbus services for competitive purposes, or to build or support a competing product or service.

### 4.4 Resale / Sublicensing

Resell, sublicense, publish, distribute, or make the API available to any third party, except that you may provide API-powered functionality through your Developer application for authorized customers consistent with your Approved Partner authorization.

### 4.5 Security Testing

Perform penetration testing, vulnerability scanning, fuzzing, or security assessments of Nimbus systems without Nimbus's prior written approval.

### 4.6 PCI / Card Data

Transmit, process, or store payment card data regulated by PCI DSS through the API.

### 4.7 Malicious Content

Introduce malware or harmful code; interfere with or disrupt the API; attempt unauthorized access to Nimbus systems.

---

## 5. PHI, HIPAA, and Security

### 5.1 PHI and BAA Requirement

The API may involve PHI. Production access involving PHI requires an executed BAA with Nimbus and any other agreements Nimbus reasonably requires.

### 5.2 Sandbox PHI Prohibited / Not Recommended

You agree that PHI is not recommended and should not be transmitted to the Sandbox. If you transmit PHI to Sandbox notwithstanding this restriction, you do so at your own risk and must ensure compliance with applicable law and your agreements.

### 5.3 Security Safeguards

You will implement administrative, physical, and technical safeguards no less rigorous than industry standards to protect data, including encryption in transit and appropriate access controls.

### 5.4 FHIR / AWS HealthLake Practices

You acknowledge Nimbus operates on FHIR-based workflows and aligns operating practices to applicable HIPAA requirements and AWS HealthLake-related security controls. (This is a general statement of approach and does not constitute a warranty or certification.)

### 5.5 Incident Notification

You will promptly notify Nimbus of any suspected compromise of Credentials or any security incident impacting Nimbus data or your integration.

---

## 6. Rate Limits; Monitoring; Suspension

### 6.1 Rate Limits

Nimbus may apply rate limits, quotas, or other controls. You must comply with them and not attempt to evade controls.

### 6.2 Suspension / Changes

Nimbus may change, suspend, or discontinue the API or Sandbox at any time. Nimbus will use best efforts to provide advance notice when reasonably practicable, but is not required to do so, including in emergencies, suspected misuse, or security risk.

---

## 7. Ownership; Feedback

### 7.1 Nimbus IP

Nimbus retains all right, title, and interest in the API, Sandbox, Documentation, and any Nimbus trademarks and materials. No rights are granted except as expressly stated.

### 7.2 Developer IP

You retain all right, title, and interest in your software and proprietary technology, excluding Nimbus IP.

### 7.3 Feedback

Any suggestions or feedback you provide may be used by Nimbus without restriction or obligation.

---

## 8. Trademark and Publicity

You may not use Nimbus (including Nimbus Healthcare, NovaMed, Nimbus OS) names, logos, or marks in marketing, press releases, or public statements without Nimbus's prior written approval. Any approved use must comply with Nimbus brand guidelines.

---

## 9. Fees

Nimbus may charge fees for API access and related services as set forth in a separate written agreement, order form, pricing schedule, or invoice.

---

## 10. Disclaimers

THE API, SANDBOX, AND DOCUMENTATION ARE PROVIDED "AS IS" AND "AS AVAILABLE." NIMBUS DISCLAIMS ALL WARRANTIES, EXPRESS OR IMPLIED, INCLUDING MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, AND NON-INFRINGEMENT, TO THE MAXIMUM EXTENT PERMITTED BY LAW.

---

## 11. Limitation of Liability

TO THE MAXIMUM EXTENT PERMITTED BY LAW, NIMBUS WILL NOT BE LIABLE FOR INDIRECT, CONSEQUENTIAL, INCIDENTAL, SPECIAL, PUNITIVE, OR EXEMPLARY DAMAGES, OR FOR LOSS OF PROFITS, REVENUE, DATA, OR GOODWILL. NIMBUS'S TOTAL LIABILITY ARISING OUT OF OR RELATING TO THE API OR THIS AGREEMENT WILL NOT EXCEED THE FEES PAID BY DEVELOPER TO NIMBUS FOR API ACCESS IN THE THREE (3) MONTHS PRIOR TO THE EVENT GIVING RISE TO LIABILITY.

---

## 12. Indemnification

You will defend, indemnify, and hold harmless Nimbus from third-party claims arising from (a) your integration or application, (b) your use of the API in violation of this Agreement, or (c) your violation of applicable law.

---

## 13. Term and Termination

This Agreement is effective upon first access to the API and continues until terminated. Nimbus may terminate or revoke access at any time for violations, security concerns, or operational necessity. Upon termination, you must cease using the API and destroy Nimbus Confidential Information as applicable.

---

## 14. Governing Law; Venue

This Agreement is governed by the laws of the State of Texas, without regard to conflict-of-law principles. Exclusive venue and jurisdiction shall lie in Travis County, Texas, unless otherwise required by applicable law.

---

## 15. Notices

Notices to Nimbus must be sent to: [legal@nimbushealthcare.com](mailto:legal@nimbushealthcare.com)

---

**Questions?** Contact [api@nimbus-os.com](mailto:api@nimbus-os.com) for API support or [legal@nimbushealthcare.com](mailto:legal@nimbushealthcare.com) for legal inquiries.
