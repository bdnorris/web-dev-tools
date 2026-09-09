---
name: Web Dev Tools
description: Civic-poster utility bench — navy chassis, cream field, signal color as marks.
colors:
  chart-navy: "#003049"
  signal-red: "#d62828"
  safety-orange: "#f77f00"
  flag-gold: "#fcbf49"
  chart-paper: "#f8e7c1"
  white: "#ffffff"
  ink: "#2c3e50"
  quiet-ink: "#6c757d"
  hairline: "#e1e5e9"
  inset-paper: "#f8f9fa"
  code-slate: "#2d3748"
typography:
  display:
    fontFamily: "IBM Plex Sans, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "2rem"
    fontWeight: 600
    lineHeight: 1.2
    letterSpacing: "normal"
  headline:
    fontFamily: "IBM Plex Sans, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "1.5rem"
    fontWeight: 600
    lineHeight: 1.3
    letterSpacing: "normal"
  title:
    fontFamily: "IBM Plex Sans, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "1.25rem"
    fontWeight: 600
    lineHeight: 1.3
    letterSpacing: "normal"
  body:
    fontFamily: "IBM Plex Sans, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "1rem"
    fontWeight: 400
    lineHeight: 1.6
    letterSpacing: "normal"
  label:
    fontFamily: "IBM Plex Sans, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif"
    fontSize: "0.9rem"
    fontWeight: 500
    lineHeight: 1.4
    letterSpacing: "normal"
  mono:
    fontFamily: "IBM Plex Mono, Monaco, Menlo, Ubuntu Mono, monospace"
    fontSize: "0.9rem"
    fontWeight: 400
    lineHeight: 1.5
    letterSpacing: "normal"
rounded:
  sm: "4px"
  md: "6px"
  lg: "8px"
  xl: "12px"
spacing:
  xs: "0.5rem"
  sm: "0.75rem"
  md: "1rem"
  lg: "1.5rem"
  xl: "2rem"
  section: "2.5rem"
  handle: "13.5rem"
  remnant: "2.75rem"
components:
  button-primary:
    backgroundColor: "{colors.chart-navy}"
    textColor: "{colors.white}"
    rounded: "{rounded.lg}"
    padding: "0.75rem 1rem"
  button-primary-hover:
    backgroundColor: "{colors.signal-red}"
    textColor: "{colors.white}"
    rounded: "{rounded.lg}"
    padding: "0.75rem 1rem"
  button-copy:
    backgroundColor: "{colors.safety-orange}"
    textColor: "{colors.white}"
    rounded: "{rounded.md}"
    padding: "0.5rem 1rem"
  button-copy-hover:
    backgroundColor: "{colors.signal-red}"
    textColor: "{colors.white}"
    rounded: "{rounded.md}"
    padding: "0.5rem 1rem"
  button-ghost:
    backgroundColor: "transparent"
    textColor: "{colors.chart-navy}"
    rounded: "{rounded.lg}"
    padding: "0.75rem 1.5rem"
  input:
    backgroundColor: "{colors.white}"
    textColor: "{colors.ink}"
    rounded: "{rounded.lg}"
    padding: "0.75rem"
  card-tool:
    backgroundColor: "{colors.white}"
    textColor: "{colors.ink}"
    rounded: "{rounded.xl}"
    padding: "2rem"
  nav-button:
    backgroundColor: "transparent"
    textColor: "{colors.white}"
    padding: "0.65rem 1rem 0.65rem 1.15rem"
  nav-button-active:
    backgroundColor: "{colors.signal-red}"
    textColor: "{colors.white}"
    padding: "0.65rem 1rem 0.65rem 1.15rem"
  chip:
    backgroundColor: "{colors.flag-gold}"
    textColor: "{colors.safety-orange}"
    rounded: "{rounded.sm}"
    padding: "0.25rem 0.5rem"
  result-primary:
    backgroundColor: "{colors.chart-navy}"
    textColor: "{colors.white}"
    rounded: "{rounded.lg}"
    padding: "1.5rem"
---

# Design System: Web Dev Tools

## Overview

**Creative North Star: "The Civic Sign Shop"**

This is a sign shop counter at work: dense, practical, slightly loud color, zero atmosphere for its own sake. The suite is a personal in-browser bench, and the chrome should read like wayfinding on a municipal poster — IBM Plex, flat fields, a handful of signal inks — not like a product dashboard.

Chart Navy is the chassis (handle, titles, primary fills). Chart Paper is the shop floor. The tool itself sits as a white sheet on that floor. Safety Orange, Signal Red, and Flag Gold are marks: focus, selection, and labels. They do not become moods or atmospheres.

Depth is poster-flat. Color blocks do the layering. Any card shadow in the code is incidental and must not grow into a floating-card system.

**Key Characteristics:**

- One sans (IBM Plex Sans) for all UI; Mono only for values, CSS, and diffs
- Navy chassis + cream floor + white sheet; signal colors as marks, never as page fills
- Flat poster fields; shadows are leftovers, not the model
- 12px on the tool sheet, 8px on controls, 4px on chips
- 200ms ease-out clip unfold on the sheet; reduced motion is an instant swap

## Colors

Signal inks on cream stock. The palette is loud so the chrome can stay plain.

### Primary
- **Chart Navy**: Chassis and authority. Sidebar fill, tool titles, primary button fills, decimal result cards. The only color allowed to own a large region besides Chart Paper and white.

### Secondary
- **Signal Red**: Selection and commit-hover. Unfolded implement, primary/copy hover, the folded-handle remnant hover. Not a page fill, not body text.

### Tertiary
- **Safety Orange**: Focus and copy. Input focus border, copy buttons, the 4px active-nav tick, conversion arrows. The spark, not a field.
- **Flag Gold**: Title spark and tags. Sidebar wordmark, category chips, the “best format” card wash. A flag, not a background.

### Neutral
- **Chart Paper**: Page ground behind the tool sheet.
- **White**: The tool sheet and control fills.
- **Ink**: Body text on white and cream.
- **Quiet Ink**: Descriptions, units, secondary labels.
- **Hairline**: Default control and card strokes (2px on inputs, 1px on inset panels).
- **Inset Paper**: Nested wells — drop zones, preview boxes, format cards, link cards.
- **Code Slate**: Code preview wells only.

### Named Rules
**The Poster Field Rule.** Chart Navy, Chart Paper, and white are the only full-field colors. Signal Red, Safety Orange, and Flag Gold are marks: they label, select, and focus. If a signal color owns more than a card or a control, it has escaped.

**The One Spark Rule.** Safety Orange is the focus/copy spark. It is not a hover fill (that is Signal Red) and not a title color on the cream floor (titles stay Chart Navy).

## Typography

**Display Font:** IBM Plex Sans (system-sans fallback)
**Body Font:** IBM Plex Sans (same)
**Label/Mono Font:** IBM Plex Mono (Monaco / Menlo fallback)

**Character:** One workhorse grotesque for the whole shop. Weight and size do hierarchy; there is no display face. Mono is for things you copy: CSS, formulas, diffs, URLs.

### Hierarchy
- **Display** (600, 2rem, ~1.2): Tool titles on the white sheet.
- **Headline** (600, 1.5rem, ~1.3): Product name in the handle.
- **Title** (600, 1.25rem, ~1.3): Section headings inside a tool.
- **Body** (400, 1rem, 1.6): Descriptions and running UI copy.
- **Label** (500, 0.9rem): Field labels, units, quiet metadata. Category chips go smaller (0.75rem, uppercase).

Result values inside color cards jump to 2rem / 700 — a data shout, not a new display role.

### Named Rules
**The One Voice Rule.** IBM Plex Sans is the only UI face. Do not pair a serif or a second grotesque. Mono is reserved for copyable values.

**The Fixed-Rem Rule.** Type is stepped in rem (2 / 1.5 / 1.25 / 1 / 0.9). No fluid `clamp()` headings. Product UI is viewed at a desk, not as a marketing hero.

## Layout

A square Chart Navy handle (13.5rem) on the left; Chart Paper filling the rest; one white tool sheet docked flush to the handle (max 800px, 1100px for Text Compare, radius `0 12px 12px 0`). Tool padding is 2rem (1.5rem under 768px). Section stacks inside a tool use 2.5rem gaps. Input grids are auto-fit at 200px; result cards auto-fit at 250px.

At 768px the handle folds to a 2.75rem edge remnant with vertical “Tools” lettering; the same unfold opens the list, then the sheet. The remnant stays docked to the sheet. At 1024px, main padding tightens; format cards go two-up, then one-up under 768px.

**The Same-Sheet Rule.** Switching tools must not invent a new page layout. The handle, the paper, and the white sheet stay; only the sheet’s interior changes.

## Elevation & Depth

Flat poster. Navy against cream is the layer. The white sheet sits on the paper because it is a different field, not because it casts a designed shadow.

### Shadow Vocabulary
- **Incidental sheet** (`0 4px 6px rgba(0, 0, 0, 0.1)`): Present on the tool sheet. Do not thicken it or spread it to every tile.
- **Hover nudge** (`0 4px 8px` / `0 4px 12px rgba(0, 0, 0, 0.1)`): Format cards and link cards on hover, with a 2px lift. Optional, not a second elevation system.
- **Focus ring** (`0 0 0 3px rgba(247, 127, 0, 0.1)`): Inputs and textareas. This is the real “lift” — a Safety Orange halo, not a drop shadow.

### Named Rules
**The Color-Does-Depth Rule.** If you need a layer, change the field (navy / paper / white / inset paper). Do not introduce glass, blur, or a shadow scale.

## Shapes

Gently squared poster sheets. The tool and large tiles use a 12px corner. Controls, inputs, inner panels, and result cards use 8px. Chips and small tags use 4px. Copy buttons and code wells use 6px. Circles are reserved for icon-only hits (clear, slider thumb). The handle stays square.

Borders are 2px Hairline at rest on inputs and outlined tiles; they shift to Safety Orange on focus. No hairline on the navy handle. The unfolded implement has a 4px Safety Orange leading bar, separated from the Signal Red fill by a 4px Chart Navy gutter so the tick reads as a mark.

**The Sheet-Then-Control Rule.** 12px is the sheet. 8px is the control. Do not round the handle. Do not pill buttons.

## Components

### Buttons
Primary fills are Chart Navy on white type, 8px corners, 0.75rem 1rem (download and other commits). Hover and the remnant hover go Signal Red. Copy is Safety Orange at 6px / 0.5rem 1rem, hover Signal Red. Ghost/reset is a 2px Chart Navy stroke on transparent, inverting to navy fill on hover.

Focus follows the input halo (Safety Orange ring). State changes use 200ms ease-out. Do not add extra shadows to make buttons “pop.”

### Chips
Flag Gold fill, Safety Orange uppercase type, 4px corners, 0.25rem 0.5rem. Used as category tags on link cards and as format badges. Not selectable filters.

### Cards / Containers
The tool sheet is white, 12px, 2rem padding, incidental shadow. Inset wells (previews, drop zones, link cards, format cards) are Inset Paper with Hairline. Format/link cards hover to a white fill, orange border, and a slight lift. Result cards are poster blocks: Chart Navy, Signal Red, or Safety Orange, white type, centered, 8px, no stroke.

### Inputs / Fields
White fill, 2px Hairline, 8px, 0.75rem padding. Focus: Safety Orange border plus the 3px orange halo. Units sit outside the field in Quiet Ink. Textareas share the same stroke language and use IBM Plex Mono. Errors are a pale rose well (`#f8d7da` / `#721c24`) — keep them as state, not a fifth brand color.

### Navigation
The handle is Chart Navy, full viewport height, 13.5rem, square. The wordmark is Flag Gold 1.5rem / 600. Nested implements are full-bleed text buttons, white, left-aligned. Hover is 10% white wash. The unfolded implement is Signal Red fill plus a 4px Safety Orange leading bar with a navy gutter. Mobile: handle folds to a 2.75rem square remnant; overlay is navy at 55% opacity. The sheet unfolds from the handle’s right edge (`clip-path` inset, 200ms ease-out).

### Result Cards (signature)
Three poster blocks for numeric outcomes (decimal / fraction / percent). They are the loudest objects on a tool sheet. Do not recast them as outlined tiles; the fill is the point.

### Code Preview
Code Slate well, light type, IBM Plex Mono ~0.9rem, 6px corners. The copy control sits below, not inside the well.

## Do's and Don'ts

### Do:
- **Do** keep Chart Navy as the chassis and Chart Paper as the floor; put work on a white sheet.
- **Do** use Safety Orange for focus and copy, Signal Red for selection and hover-commit, Flag Gold for the wordmark and chips.
- **Do** set UI in IBM Plex Sans and copyable values in IBM Plex Mono, in rem steps.
- **Do** use 12px on sheets, 8px on controls, 4px on chips.
- **Do** let color fields do depth; treat existing card shadows as incidental.

### Don't:
- **Don't** drift into SaaS: no glass, no purple-blue gradients, no Inter as display, no floating-card dashboards.
- **Don't** introduce a second type family or a fluid display heading.
- **Don't** fill large regions with Signal Red, Safety Orange, or Flag Gold.
- **Don't** pill buttons or round the navy handle.
- **Don't** build a shadow elevation scale; if a layer is needed, change the field color.
