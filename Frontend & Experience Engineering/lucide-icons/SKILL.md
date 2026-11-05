---
name: lucide-icons
description: Standardizes accessible Lucide React icon usage in SAP Core interfaces.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Lucide Icons

## Purpose
Use a consistent icon set to clarify actions and status without reducing accessibility or interface clarity.

## Scope
Lucide React imports, sizing, color, accessible names, and icon-only controls.

## When to Use This Skill
Use when adding or changing visual icons in React interfaces.

## When NOT to Use This Skill
Do not use an icon alone when users need a text label or when an icon's meaning is ambiguous.

## SAP Core Context
Lucide React is the approved icon source; no mandatory component library is assumed. Tailwind styles surrounding controls.

## Core Principles
Icons support, but do not replace, accessible names and textual business meaning.

## Mandatory Rules
- Use icons from the approved package with explicit imports; avoid runtime lookup by untrusted string.
- Icon-only buttons require an accessible name and tooltip/visible affordance appropriate to context.
- Mark decorative icons as hidden from assistive technology when an adjacent label already names the action.
- Do not encode status exclusively by icon shape or color; include text or another accessible cue.

## Architecture Guidelines
Use consistent size/stroke/color tokens and align icons within shared controls. Avoid custom ad hoc SVG where Lucide has the intended symbol; if a domain symbol is not clear, prefer text label over a misleading icon. Keep action semantics on the button/link, not the SVG.

## Implementation Guidelines
**Example:** A “Reverse journal entry” button includes the text label and a decorative undo-style icon; an icon-only compact toolbar action has an accessible name describing the exact business action and a visible focus indicator.

## Data and Security Considerations
Never hide a protected action only by omitting its icon; server authorization is required. Avoid rendering untrusted SVG content as an icon.

## Common Anti-Patterns
Unlabeled icon-only buttons; confusing symbols for Post/Reverse/Delete; inconsistent sizes; icon-only status; action rendered as non-interactive SVG; importing entire icon registry dynamically.

## Validation Requirements
**Implementation checklist:** semantic button/link; accessible name; decorative/meaningful treatment; size/focus. **Review checklist:** screen-reader name, keyboard focus, tooltip not sole label for critical action, icon meaning clear at actual size.

## Related Skills
react-engineering, tailwind-engineering, application-security.

## Reference Documents
Lucide React official guide https://lucide.dev/guide/packages/lucide-react and accessibility guidance https://lucide.dev/guide/. Verify package API for the installed release.
