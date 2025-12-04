# Internal API Documentation Best Practices

**Date**: December 2025  
**Purpose**: Guide for documenting internal APIs (RPA, clinic management, etc.)

---

## 🎯 Overview

Internal APIs are for **internal use only** and should be documented separately from partner-facing APIs. This guide outlines best practices for documenting internal APIs.

---

## 📋 What Are Internal APIs?

Internal APIs include:
- **RPA Workflows** - Robotic Process Automation for practitioner onboarding
- **Clinic Management** - Creating and managing clinics
- **Practitioner Management** - Internal practitioner operations
- **Patient Management** - Internal patient operations
- **Admin Operations** - Super admin and system management
- **Payment Processing** - Internal payment operations
- **Pharmetika Integration** - Internal pharmacy system integration

---

## 🛠️ Best Practices for Internal API Documentation

### 1. **Use Redocly for Internal Documentation**

**Recommendation**: Create a **separate Redocly Realm project** for internal APIs.

**Benefits**:
- ✅ Separate from partner-facing documentation
- ✅ Access control (internal team only)
- ✅ Different branding/styling if needed
- ✅ Independent versioning and deployment
- ✅ Can use same Redocly features (preview, validation, etc.)

**Setup**:
1. Create new Redocly Cloud project: "Nimbus OS Internal APIs"
2. Create separate GitHub repository: `nimbus-internal-api-docs`
3. Configure access control (internal team only)
4. Use same OpenAPI specification format
5. Deploy to internal URL: `internal-api-docs.nimbus-os.com`

### 2. **Alternative: Private GitHub Repository**

If Redocly isn't preferred for internal docs:
- Use **private GitHub repository** with markdown documentation
- Use **GitHub Wiki** for internal API documentation
- Use **Confluence** or **Notion** for internal documentation
- Use **Swagger UI** hosted internally

### 3. **Documentation Structure**

```
internal-api-docs/
├── README.md                    # Overview and setup
├── openapi.yaml                 # OpenAPI spec for all internal APIs
├── guides/
│   ├── rpa-workflows.md         # RPA workflow documentation
│   ├── clinic-management.md     # Clinic operations
│   ├── practitioner-management.md
│   ├── patient-management.md
│   └── admin-operations.md
└── examples/
    ├── rpa-examples.md
    └── integration-examples.md
```

### 4. **Access Control**

**Redocly Cloud**:
- Set project to "Private" or "Organization Only"
- Use Redocly's access control features
- Restrict to internal team members only

**GitHub**:
- Use private repository
- Grant access to internal team only
- Use GitHub's access control

**Internal Hosting**:
- Host on internal network/VPN
- Use internal authentication (SSO, LDAP)
- Restrict external access

---

## 📝 Documentation Content

### What to Include

1. **API Endpoints**
   - All internal endpoints
   - Request/response schemas
   - Authentication methods
   - Error responses

2. **Workflow Documentation**
   - RPA workflow steps
   - Integration flows
   - Data flow diagrams
   - Process documentation

3. **Integration Guides**
   - How to integrate with internal systems
   - Authentication setup
   - Example requests/responses
   - Common use cases

4. **Troubleshooting**
   - Common issues
   - Error codes and meanings
   - Debugging tips
   - Support contacts

### What to Exclude

- ❌ Partner-specific information
- ❌ Public-facing examples
- ❌ Marketing content
- ❌ External support information

---

## 🔐 Security Considerations

### Access Control
- **Authentication**: Require authentication for internal docs
- **Authorization**: Role-based access if needed
- **Audit Logging**: Track who accesses internal docs
- **IP Restrictions**: Restrict to internal IPs if possible

### Information Security
- **No Sensitive Data**: Don't include API keys, passwords, or tokens
- **Environment Variables**: Use environment-specific examples
- **Redacted Examples**: Remove sensitive information from examples
- **Secure Storage**: Store documentation securely

---

## 🚀 Recommended Approach: Redocly for Internal APIs

### Why Redocly?

1. **Consistency**: Same tooling as partner portal
2. **Features**: Preview, validation, versioning
3. **Maintenance**: Easy to maintain and update
4. **Collaboration**: Team can collaborate on docs
5. **Professional**: Professional documentation appearance

### Setup Steps

1. **Create Redocly Project**:
   ```
   Project Name: Nimbus OS Internal APIs
   Organization: Nimbus (internal)
   Visibility: Private
   ```

2. **Create Repository**:
   ```
   Repository: nimbus-internal-api-docs
   Access: Internal team only
   ```

3. **Configure Access**:
   - Add internal team members
   - Set up SSO if available
   - Configure branch protection

4. **Document APIs**:
   - Create OpenAPI spec for internal APIs
   - Add guides and examples
   - Set up CI/CD for automatic deployment

5. **Deploy**:
   - Deploy to internal URL
   - Set up custom domain if needed
   - Configure access control

---

## 📊 Comparison: Internal vs Partner Documentation

| Aspect | Internal APIs | Partner APIs |
|--------|--------------|--------------|
| **Audience** | Internal team | External partners |
| **Access** | Private/Internal | Public (with API key) |
| **Documentation Tool** | Redocly (private) or internal wiki | Redocly (public) |
| **Focus** | All operations | Pharmacy operations only |
| **Examples** | Internal use cases | Partner integration |
| **Security** | Internal auth | API key auth |
| **Support** | Internal Slack/email | api@nimbus-os.com |

---

## 🎯 Action Items

### For Internal API Documentation

1. **Decide on Tool**:
   - [ ] Redocly (recommended)
   - [ ] GitHub Wiki
   - [ ] Confluence/Notion
   - [ ] Internal hosting

2. **Set Up Documentation**:
   - [ ] Create repository/project
   - [ ] Configure access control
   - [ ] Set up deployment pipeline

3. **Document APIs**:
   - [ ] Create OpenAPI spec
   - [ ] Document RPA workflows
   - [ ] Document clinic management
   - [ ] Document admin operations

4. **Maintain Documentation**:
   - [ ] Keep docs up to date
   - [ ] Review regularly
   - [ ] Gather team feedback

---

## 📚 Resources

- **Redocly Documentation**: https://redocly.com/docs
- **OpenAPI Specification**: https://spec.openapis.org/oas/v3.1.0
- **Internal Support**: Contact engineering team

---

## 💡 Recommendations

### Primary Recommendation: Redocly Private Project

**Pros**:
- ✅ Same tooling as partner portal
- ✅ Professional appearance
- ✅ Easy to maintain
- ✅ Built-in access control
- ✅ Preview and validation features

**Cons**:
- ⚠️ Requires Redocly Cloud account
- ⚠️ May have additional costs

### Alternative: Private GitHub Repository

**Pros**:
- ✅ Free
- ✅ Version control built-in
- ✅ Easy collaboration
- ✅ Can use GitHub Pages for hosting

**Cons**:
- ⚠️ Less polished than Redocly
- ⚠️ Manual documentation formatting
- ⚠️ No built-in OpenAPI preview

---

**Last Updated**: December 2025  
**Status**: Best Practices Guide
