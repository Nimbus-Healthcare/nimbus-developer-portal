# Theme Customization Guide

This guide explains how to customize the Nimbus OS Developer Portal theme using Redocly Realm's CSS variable system.

## Overview

Theme customization is done through CSS variables in `@theme/styles.css`. Redocly Realm automatically loads this file and applies the customizations.

## File Structure

```
@theme/
└── styles.css    # Custom theme CSS with CSS variables
```

## Nimbus Brand Colors

The portal uses the following Nimbus OS brand colors:

| Color | Hex Code | Usage |
|-------|----------|-------|
| **Primary Teal** | `#0ED2C3` | Primary links, buttons, accents |
| **Signal Green** | `#2FFFC7` | Success states, hover effects |
| **Crimson** | `#FF4F7B` | Error states, warnings |
| **Warning Orange** | `#FFA726` | Warning messages |
| **Azure** | `#0041C2` | Secondary actions |
| **Violet** | `#8A7CFF` | Tertiary accents |

## CSS Variables

### Primary Colors

```css
:root {
  --color-primary: #0ED2C3;           /* Nimbus OS Teal */
  --color-primary-hover: #0bb8a8;   /* Darker teal for hover */
}
```

### Link Colors

```css
:root {
  --link-text-color: #0ED2C3;         /* Teal for links */
  --link-text-color-hover: #0bb8a8;   /* Darker teal on hover */
}
```

### Semantic Colors

```css
:root {
  --color-success: #2FFFC7;          /* Signal Green */
  --color-warning: #FFA726;          /* Warning orange */
  --color-error: #FF4F7B;             /* Crimson */
}
```

### Typography

```css
:root {
  --h1-text-color: #111827;           /* Dark text for headings */
  --text-color: #374151;              /* Body text */
}
```

## Dark Mode Support

Dark mode variables are defined under `:root.dark`:

```css
:root.dark {
  --color-primary: #0ED2C3;           /* Keep teal bright in dark mode */
  --color-primary-hover: #2FFFC7;     /* Signal green on hover */
  --link-text-color: #0ED2C3;
  --link-text-color-hover: #2FFFC7;
  --h1-text-color: #E6E9F0;           /* Light text for headings */
  --text-color: #9DA9B8;              /* Muted text */
}
```

## Customizing Colors

### Change Primary Color

Edit `@theme/styles.css`:

```css
:root {
  --color-primary: #YOUR_COLOR;
  --link-text-color: #YOUR_COLOR;
}
```

### Add New Color Variables

```css
:root {
  --color-custom: #HEX_CODE;
}
```

Then use in specific selectors:

```css
.custom-element {
  color: var(--color-custom);
}
```

## Available CSS Variables

Redocly Realm supports many CSS variables. Common ones include:

- `--color-primary` - Primary brand color
- `--color-primary-hover` - Primary color on hover
- `--link-text-color` - Link text color
- `--link-text-color-hover` - Link hover color
- `--color-success` - Success state color
- `--color-warning` - Warning state color
- `--color-error` - Error state color
- `--h1-text-color` - Heading text color
- `--text-color` - Body text color
- `--navbar-bg-color` - Navigation bar background
- `--sidebar-background-color` - Sidebar background
- `--content-background-color` - Content area background
- `--border-color` - Border color

For a complete list, see [Redocly CSS Variables Documentation](https://redocly.com/docs/realm/branding/css-variables/common).

## Specific Overrides

You can add specific CSS rules to override default styles:

```css
/* Override all links */
a {
  color: var(--link-text-color) !important;
}

a:hover {
  color: var(--link-text-color-hover) !important;
}

/* Override buttons */
button[class*="primary"] {
  background-color: var(--color-primary) !important;
}
```

## Logo Configuration

Logo is configured in `redocly.yaml`:

```yaml
logo:
  image: ./assets/logo.png
  altText: Nimbus OS Logo
```

To change the logo:
1. Replace `assets/logo.png` with your new logo
2. Ensure it's a PNG file (recommended: transparent background)
3. Recommended size: 200x50px or similar aspect ratio

## Typography

Typography is controlled via CSS variables and can be customized:

```css
:root {
  --font-family: 'Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif';
  --font-family-headings: 'Montserrat, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif';
  --font-family-code: '"JetBrains Mono", "Fira Code", monospace';
}
```

## Testing Changes

1. **Edit `@theme/styles.css`**
2. **Commit and push**:
   ```bash
   git add @theme/styles.css
   git commit -m "feat: update theme colors"
   git push origin main
   ```
3. **Wait for deployment** (automatic)
4. **Verify on live site**: [nimbus-os.redocly.app](https://nimbus-os.redocly.app)
5. **Hard refresh** browser (Cmd+Shift+R / Ctrl+Shift+R) to clear cache

## Best Practices

1. **Use CSS Variables**: Always use CSS variables instead of hardcoded colors
2. **Test Both Modes**: Verify changes work in both light and dark mode
3. **Use !important Sparingly**: Only when necessary to override defaults
4. **Maintain Brand Colors**: Stick to Nimbus brand color palette
5. **Document Changes**: Update this file when adding new variables

## Troubleshooting

### Colors Not Applying

1. **Check CSS syntax**: Ensure variables are properly defined
2. **Hard refresh**: Clear browser cache (Cmd+Shift+R)
3. **Verify file location**: CSS file must be in `@theme/styles.css`
4. **Check deployment**: Ensure latest version is deployed

### Dark Mode Not Working

1. **Verify `:root.dark` selector**: Must match Redocly's dark mode class
2. **Test toggle**: Use the theme toggle in the header
3. **Check variable inheritance**: Dark mode variables should override light mode

## Resources

- [Redocly Theme Customization](https://redocly.com/docs/realm/branding/customize-styles)
- [Redocly CSS Variables](https://redocly.com/docs/realm/branding/css-variables/common)
- [Nimbus Brand Guidelines](https://github.com/Nimbus-Healthcare/nimbus-os/blob/main/NIMBUS-OS-BRAND-GUIDELINES-2025-11-24.md)

---

**Last Updated**: December 2025
