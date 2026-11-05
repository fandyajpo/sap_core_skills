---
name: tailwind-engineering
description: Applies Tailwind CSS consistently to SAP Core UI without obscuring semantics or accessibility.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Tailwind Engineering

## Purpose
Provide a consistent, responsive visual system using the approved primary styling tool.

## Scope
Utility classes, design tokens/theme, responsive states, component styling, and class composition.

## When to Use This Skill
Use for new or changed UI styling and shared visual patterns.

## When NOT to Use This Skill
Do not use classes as a substitute for semantic HTML, accessibility, or component behavior.

## SAP Core Context
Tailwind CSS is primary. No mandatory component library is approved. Shared tokens should support dense ERP data surfaces and consistent business forms.

## Core Principles
Use semantic markup and a small coherent design vocabulary; keep visual state legible at operational density.

## Mandatory Rules
- Inspect the pinned Tailwind major version before configuring; v3/v4 setup and configuration differ.
- Do not construct class names from arbitrary runtime fragments that the build scanner cannot detect; use explicit class maps or supported composition.
- Provide focus, disabled, invalid, hover, and responsive states without relying only on color.
- Respect contrast and readable table/form density.

## Architecture Guidelines
Centralize approved color, spacing, typography, and state tokens through the project's configured Tailwind mechanism. Compose repeatable patterns in feature/shared components only when semantics align. Avoid global styles that silently change ERP widgets. Keep conditional class logic explicit and testable.

## Implementation Guidelines
**Example:** A field component applies explicit classes for default, invalid, and disabled states from a finite mapping; it includes a label and described error text. A status badge uses text/icon plus color so “Overdue” remains distinguishable without color vision.

## Data and Security Considerations
Styling does not protect data. Ensure sensitive data isn't rendered in hidden responsive panes or print states without authorization. Avoid unsafe user-controlled CSS/class injection.

## Common Anti-Patterns
Unbounded arbitrary values; dynamically interpolated classes; color-only status; conflicting utility piles; duplicated token values; custom CSS that bypasses design-system review; using CSS `display:none` as authorization.

## Validation Requirements
**Implementation checklist:** pinned version/config; tokens; responsive and focus states; contrast; finite variants. **Review checklist:** keyboard focus visible; errors understandable; no runtime class loss; dense tables remain legible at supported breakpoints; hidden fields are not security controls.

## Related Skills
react-engineering, motion-engineering, storybook-engineering, lucide-icons.

## Reference Documents
Official Tailwind documentation https://tailwindcss.com/docs. Verify configuration and class-generation behavior against the project's pinned Tailwind version.
