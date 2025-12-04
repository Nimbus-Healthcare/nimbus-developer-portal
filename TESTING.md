# Testing Guide

This guide outlines testing procedures for the Nimbus OS Developer Portal.

## Navigation Link Testing

### Main Navigation Links

Test all navigation links in the header:

- [x] **Home** (`/`) - Landing page
- [x] **Overview** (`/guides/overview`) - API overview guide
- [x] **Quickstart** (`/guides/quickstart`) - Getting started guide
- [x] **Authentication** (`/guides/authentication`) - Authentication guide
- [ ] **Orders** (`/guides/orders`) - Orders API guide
- [ ] **Refills** (`/guides/refills`) - Refills API guide
- [ ] **Tracking** (`/guides/tracking`) - Tracking API guide
- [ ] **Webhooks** (`/guides/webhooks`) - Webhooks guide
- [ ] **Errors & Idempotency** (`/guides/errors-idempotency`) - Error handling guide
- [ ] **Environments** (`/guides/environments`) - Environment setup guide
- [x] **API Reference** (`/api-reference`) - API reference page
- [ ] **Changelog** (`/changelog`) - Version history
- [ ] **Terms** (`/api-terms`) - API terms and conditions

### Footer Links

Test all footer links:

- [ ] Documentation group links
- [ ] Resources group links
- [ ] Support group links (email links)
- [ ] External links (Redocly Docs, Support)

## Visual Testing

### Logo Verification

- [x] Logo displays in header (top-left)
- [x] Logo is clear and properly sized
- [x] Logo links to home page (`/`)

### Brand Colors

- [x] Primary links use teal color (`#0ED2C3`)
- [x] Links change color on hover
- [x] Primary buttons use teal background
- [ ] Success states use signal green (`#2FFFC7`)
- [ ] Error states use crimson (`#FF4F7B`)
- [ ] Warning states use orange (`#FFA726`)

### Favicon

- [ ] Favicon displays in browser tab
- [ ] Favicon is Nimbus logo

## Responsive Design Testing

### Mobile View (375x667 - iPhone SE)

- [x] Page renders correctly on mobile
- [ ] Navigation menu is accessible (hamburger menu)
- [ ] Content is readable without horizontal scrolling
- [ ] Links are tappable (adequate touch targets)
- [ ] Logo scales appropriately

### Tablet View (768x1024 - iPad)

- [ ] Layout adapts appropriately
- [ ] Sidebar navigation is accessible
- [ ] Content columns adjust properly

### Desktop View (1920x1080)

- [x] Full layout displays correctly
- [x] Sidebar navigation visible
- [x] Content area properly sized

## Dark Mode Testing

### Light Mode

- [x] Background is white/light
- [x] Text is dark and readable
- [x] Links are teal (`#0ED2C3`)

### Dark Mode

- [ ] Toggle dark mode using moon icon in header
- [ ] Background changes to dark
- [ ] Text changes to light color
- [ ] Links remain teal and visible
- [ ] Logo remains visible
- [ ] All content is readable

## Functionality Testing

### Search

- [ ] Search icon is clickable
- [ ] Search opens search interface
- [ ] Search returns relevant results
- [ ] Keyboard shortcut (⌘K / Ctrl+K) works

### Code Blocks

- [ ] Code examples display correctly
- [ ] Syntax highlighting works
- [ ] Copy buttons function properly
- [ ] Code is readable in both light/dark mode

### API Reference

- [ ] OpenAPI spec renders correctly
- [ ] Endpoints are expandable/collapsible
- [ ] Request/response examples display
- [ ] Try it out functionality works (if enabled)

## Performance Testing

### Load Times

- [ ] Initial page load < 3 seconds
- [ ] Navigation between pages is fast
- [ ] Images load quickly
- [ ] No console errors

### Browser Compatibility

Test in:
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest)
- [ ] Edge (latest)
- [ ] Mobile Safari (iOS)
- [ ] Chrome Mobile (Android)

## Accessibility Testing

### Keyboard Navigation

- [ ] Tab navigation works through all links
- [ ] Focus indicators are visible
- [ ] Skip to content link works
- [ ] All interactive elements are keyboard accessible

### Screen Reader

- [ ] Page title is descriptive
- [ ] Alt text on logo is present
- [ ] Headings are properly structured
- [ ] Links have descriptive text

### Color Contrast

- [ ] Text meets WCAG AA contrast ratios
- [ ] Links are distinguishable from body text
- [ ] Focus states are visible

## Content Verification

### Accuracy

- [ ] All API endpoints are documented
- [ ] Base URLs are correct (sandbox/production)
- [ ] Code examples are accurate
- [ ] Links to external resources work

### Completeness

- [ ] All guide pages have content
- [ ] No placeholder text remains
- [ ] All sections are filled out
- [ ] Changelog is up to date

## Deployment Testing

### Build Process

- [x] Build completes successfully
- [x] Lint passes without errors
- [x] Link checker passes
- [x] Respect Monitoring passes

### Preview Deployments

- [ ] PR preview deployments work
- [ ] Preview URLs are accessible
- [ ] Preview shows latest changes
- [ ] Preview matches production after merge

## Regression Testing

After any changes, verify:

- [ ] Logo still displays
- [ ] Brand colors still applied
- [ ] Navigation still works
- [ ] All pages still accessible
- [ ] No broken links
- [ ] Build still succeeds

## Automated Testing

### CI/CD Checks

The `.github/workflows/validate.yml` workflow automatically:

- [x] Validates OpenAPI spec
- [x] Checks for linting errors
- [ ] Runs link checker (if configured)
- [ ] Verifies build succeeds

## Reporting Issues

If you find issues during testing:

1. Document the issue with:
   - Page URL
   - Browser and version
   - Steps to reproduce
   - Screenshot (if visual issue)
   - Expected vs actual behavior

2. Create an issue in this repository or contact:
   - **API Support**: [api@nimbus-os.com](mailto:api@nimbus-os.com)
   - **Technical Issues**: Open a GitHub issue

---

**Last Updated**: December 2025
