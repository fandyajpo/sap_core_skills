---
name: motion-engineering
description: Uses Motion for accessible, purposeful SAP Core interaction feedback.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Motion Engineering

## Purpose
Use animation to explain state changes and guide attention without slowing operational work.

## Scope
Motion.dev React integration, transitions, presence, layout animation, and reduced-motion behavior.

## When to Use This Skill
Use for meaningful transitions, progressive disclosure, and feedback where motion improves comprehension.

## When NOT to Use This Skill
Do not animate every state change, large financial tables, or essential information that must be visible immediately.

## SAP Core Context
Motion (motion.dev) is approved alongside React/Tailwind. Motion is optional; accessible static behavior is the baseline.

## Core Principles
Motion should be purposeful, brief, interruptible, and nonessential to task completion.

## Mandatory Rules
- Respect `prefers-reduced-motion` and provide a reduced/no-motion path.
- Preserve focus order and semantics during enter/exit transitions.
- Do not use animation to conceal errors, loading delays, or permission changes.
- Verify package/import/API names against the pinned Motion release; motion.dev has version-specific integration details.

## Architecture Guidelines
Animate small UI changes (dialog, disclosure, notification) rather than whole document workflows. Keep business state independent of animation completion. Avoid layout animations on rapidly changing data grids. Do not delay submitting/posting a business operation until a visual transition ends.

## Implementation Guidelines
**Example:** A confirmation dialog animates opacity/position on open, but reduced-motion users receive an immediate state change. The dialog traps/manages focus according to the accessible dialog implementation and its close action remains available even if animation is interrupted.

## Data and Security Considerations
A disappearing row is not proof that a mutation succeeded; show authoritative server outcome. Do not animate sensitive records into a DOM subtree that remains accessible after authorization changes. Respect CPU/battery and avoid motion during large data updates.

## Common Anti-Patterns
Infinite decorative motion; essential status conveyed only by movement; motion-driven business transitions; ignoring reduced motion; animating thousands of table rows; focus lost on exit.

## Validation Requirements
**Implementation checklist:** reason for motion; static/reduced behavior; focus/keyboard; failure state independent; version API verified. **Review checklist:** reduced-motion test; screen-reader semantics; rapid update performance; no interaction blocked by animation.

## Related Skills
react-engineering, tailwind-engineering, application-security, storybook-engineering.

## Reference Documents
Motion official docs https://motion.dev/docs and accessibility guidance https://motion.dev/docs/react-accessibility. Verify APIs for the installed Motion package/version.
