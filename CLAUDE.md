# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Shopify Skeleton Theme - a minimal, carefully structured Shopify theme designed as a foundational starting point. It follows Shopify's best practices and emphasizes modularity and maintainability. The theme is intentionally minimalist and should not become a fully-featured theme.

## Development Commands

### Preview and Development
```bash
# Start local development server with hot reload
shopify theme dev
```

### Theme Management
```bash
# Pull theme from Shopify store
shopify theme pull

# Push theme to Shopify store
shopify theme push
```

## Architecture

### Theme Structure
The theme follows Shopify's standard architecture:

- **assets/** - Static assets (CSS, JS, images, SVGs). Contains `critical.css` which holds essential CSS loaded on every page.
- **blocks/** - Reusable, nestable UI components (e.g., `text.liquid`, `group.liquid`)
- **config/** - Global theme settings (`settings_schema.json` defines theme customization options)
- **layout/** - Top-level page wrappers (`theme.liquid` is the main layout, `password.liquid` for password-protected stores)
- **locales/** - Translation files for internationalization (e.g., `en.default.json`)
- **sections/** - Modular full-width page components (e.g., `header.liquid`, `footer.liquid`, `product.liquid`)
- **snippets/** - Reusable Liquid code fragments (e.g., `css-variables.liquid`, `meta-tags.liquid`)
- **templates/** - JSON files combining sections to define page structures (auto-generated, can be overwritten by Shopify admin)

### Key Architecture Patterns

#### CSS & JavaScript Approach
- Use `{% stylesheet %}` and `{% javascript %}` tags within components - they can be included multiple times but code appears only once
- Critical CSS is separated into `assets/critical.css` and loaded with preload on every page
- CSS variables are defined in `snippets/css-variables.liquid` and inlined in the `<head>`

#### Schema-Based Settings
Components use `{% schema %}` blocks for customization:

**Single property settings** - Use CSS variables:
```liquid
<div style="--gap: {{ block.settings.gap }}px">
{% stylesheet %}
  .collection { gap: var(--gap); }
{% endstylesheet %}
```

**Multiple property settings** - Use CSS classes:
```liquid
<div class="{{ block.settings.layout }}">
{% stylesheet %}
  .collection--full-width { /* styles */ }
  .collection--narrow { /* styles */ }
{% endstylesheet %}
```

#### Font Loading Strategy
1. Preconnect to font CDN for faster connection
2. Preload only critical font variant (base weight)
3. Additional variants load on-demand via @font-face with `font_display: 'swap'`
4. All font variations are loaded in `css-variables.liquid`

#### Translation Keys
Use translation keys (e.g., `"t:general.text"`, `"t:labels.alignment"`) in schemas rather than hardcoded strings. These reference keys in `locales/en.default.json`.

### Template System
- Uses JSON templates (not Liquid templates) for merchant customization
- Templates are auto-generated and may be overwritten by Shopify admin
- Each template combines sections to define page structure
- Section groups (`header-group`, `footer-group`) are rendered via `{% sections %}` tag in layouts

### Blocks Architecture
- Blocks are rendered using `{% content_for 'blocks' %}` within sections
- Each block has its own schema defining settings
- Blocks can be of type `@theme` to accept any theme-level block

## Development Guidelines

### Minimalism Philosophy
- This theme must remain minimal and foundational
- Do not add features that make it a fully-featured theme
- Avoid including legacy or non-recommended Shopify features
- Keep solutions simple and focused on core functionality

### Component Development
- Blocks are small, reusable pieces with their own settings
- Sections are full-width modular components that can contain blocks
- Always include `{{ block.shopify_attributes }}` on block root elements
- Use `{% doc %}` comments to document component usage

### CSS Variable System
Global CSS variables defined in `css-variables.liquid`:
- `--font-primary--family`, `--font-primary--style`, `--font-primary--weight`
- `--page-width`, `--page-margin`
- `--color-background`, `--color-foreground`
- `--style-border-radius-inputs`

These are controlled by settings in `config/settings_schema.json`.

## Shopify Theme Store Compliance

This theme has been enhanced to meet all Shopify Theme Store requirements:

### Mandatory Features Implemented

**Product Page** (sections/product.liquid):
- Variant image selection with dynamic media gallery
- Unit pricing display on all variants
- Rich media support (videos, 3D models via `media_tag`)
- Shop Pay Installments banner (`payment_terms` filter)
- Related product recommendations via Shopify API
- Local pickup availability display using `store_availabilities`
- Compare-at pricing with automatic savings calculation
- Dynamic variant switching with URL updates

**Cart Page** (sections/cart.liquid):
- Line-level and cart-level discount display
- Accelerated checkout buttons (`content_for_additional_checkout_buttons`)
- Unit pricing on all line items
- Selling plan/subscription display
- AJAX cart updates via `/cart/update.js`
- Discount code input with checkout integration
- Comprehensive price breakdown

**Collection Page** (sections/collection.liquid):
- Faceted filtering using `collection.filters`
- Price range filters
- Sorting functionality (8 options including best-selling, price, date)
- Unit pricing on product cards
- Sale and sold-out badges
- Active filter display with removable chips
- Mobile-responsive filter sidebar
- Paginated results

**Search Page** (sections/search.liquid):
- Predictive search using `/search/suggest.json` API
- Live autocomplete suggestions
- Grouped results by type (products, articles, pages)
- Result type filtering
- Debounced search (300ms)
- No-results state handling

**Header** (sections/header.liquid):
- Multi-level navigation (3 levels deep)
- Dropdown and sub-dropdown menus
- Mobile hamburger menu
- Search toggle
- Cart count badge
- Sticky header option
- Logo upload support
- Mobile overlay

**Footer** (sections/footer.liquid):
- Newsletter signup form (customer form integration)
- Social media links (Facebook, Instagram, Twitter, YouTube)
- Multi-column menu support (up to 4 blocks)
- Payment icons display
- Success/error state handling

**Additional Features**:
- Image focal point support (snippets/image.liquid)
- Age verification modal for wine/spirits (sections/age-verification.liquid)
- Lazy loading for images
- Accessibility improvements (ARIA labels, keyboard navigation)
- Mobile-first responsive design

### Performance Considerations

- Critical CSS separated and preloaded
- JavaScript uses modern ES6 classes
- Lazy loading enabled by default
- CSS variables for theming
- Debounced search and filter interactions
- AJAX-based cart updates (no full page reload)

### Accessibility Features

- Proper ARIA labels on all interactive elements
- Keyboard navigation support
- Visible focus states
- Semantic HTML structure
- Alt text support on all images
- Touch target sizes of 24×24 CSS pixels minimum
- Color contrast compliance (4.5:1 for body text)

## Wine & Spirits Store Features

This theme includes specialized features for wine and spirits retail:

### Age Verification
- Customizable age gate modal (sections/age-verification.liquid)
- Cookie-based "remember me" functionality (30-day expiration)
- Configurable messaging and branding
- Under-age warning display
- Legal disclaimer support

### Product Display
- Support for vintage, region, ABV via metafields
- Unit pricing for volume-based pricing
- Rich media for product videos and 3D bottle views
- Recommendations for similar wines

### Filtering
- Advanced faceted filtering for wine attributes
- Price range filtering
- Type, region, vintage filtering via collection filters
- Availability filtering

## Contributing Standards

From CONTRIBUTING.md:
- This codebase must be minimalist, not a fully featured theme
- Must provide a common foundational starting point for most developers
- Do not include or reference legacy or non-recommended features
- All changes must preserve the principles defined in the README
