# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Primary user is the maker: a frontend developer in the middle of turning a design into CSS or compressing an asset. They open this as a private bench, not as a product they are selling or onboarding others into.

## Product Purpose

A growing personal suite of in-browser utilities for the design-to-code and asset-prep jobs that otherwise send the user to a random calculator or converter tab. Success is reaching for the right tool, getting a copyable result, and returning to the work without accounts, uploads to a server, or a new site to learn.

The current tool set is a starting collection, not the product identity. New utilities are expected as real needs appear.

## Positioning

A private, accumulating bench that stays on the machine. Neighboring public calculators are one-off, server-backed, or both. This suite can keep adding tools without changing that local, no-account contract.

## Operating Context

Used mid-task while implementing from Figma or a visual spec: converting units, cleaning pasted Figma typography CSS, checking line-height, scaling ratios, compressing images, and diffing text. The app is a Vue 3 + Vite single-page site with hash-routed tools (`#ratio`, `#pixel-em`, `#line-height`, `#figma-type`, `#image-webp`, `#text-compare`, `#helpful-links`). Image conversion uses in-browser codecs and requires cross-origin isolation (COOP/COEP) on the dev server.

## Capabilities and Constraints

Current tools (do not drop without asking):

- Ratio Converter — proportional A:B value conversion
- Pixel to Em — px ↔ em against a base font size, with a common-values table
- Line Height Calculator — px measurements to decimal / fraction / percent, plus CSS copy and a live preview
- Figma Type — paste Figma typography CSS; emit cleaner CSS with em units, fractional line-heights, and unused properties removed
- Image Converter — JPG/PNG in the browser to WebP, AVIF, PNG, and JPG with size comparison and download; 10MB file cap; recommended max ~5000×5000 (25MP), hard reject ~50MP
- Text Compare — line-by-line diff of two pasted texts
- Helpful Links — curated external bookmarks

Hard constraints:

- All computation stays in the browser. Files and pasted text never go to a server.
- No accounts, no backend, no analytics or tracking.
- New tools may be added; existing tools stay unless explicitly retired.

Open: product name. The UI currently says "Web Dev Tools" (package name `web-tools`); that name is not a binding brand commitment.

## Evidence on Hand

The runnable tools and their on-screen copy are the only product evidence. There are no testimonials, case studies, customers, pricing, or press. Future work must not fabricate them.

## Product Principles

- Stay on the machine. Nothing the user pastes or uploads leaves the browser.
- Accumulate with real need. Add tools when the work demands them; do not remove current ones without asking.
- Instant and accountless. Open a hash, get a result, copy it, leave.
- Accessible by default. Accessibility is a product requirement, not a polish pass.

## Accessibility & Inclusion

WCAG / accessibility is a hard requirement. Conformance level is undecided.
