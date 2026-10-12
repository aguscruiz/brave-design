# Changelog

Versions follow [semantic versioning](https://semver.org/), applied to the guidelines:

- **Major**: a rule changes or is added such that a surface that followed the previous version may
  no longer comply.
- **Minor**: new guidance that does not make a compliant surface non-compliant (a new section, a new
  example, a clarified recommendation).
- **Patch**: wording, links and typo fixes with no change in meaning.

## Unreleased

Adds rules, so this is a major change under the policy above:

- Interaction: destructive actions, loading and network states, forms, focus, and keyboard
  shortcuts, including how Esc works when a model is generating.
- Privacy and consent: measurement only through privacy-preserving analytics.
- Agent and AI surfaces: screen reader announcements and message structure.
- Desktop browser: how Nala colours follow the user's theme through the colour provider, which
  colours stay fixed, and the cases that need care (neutral barely showing an accent, custom
  themes, the darker theme, WebUI), with a test matrix.
- Android browser: the colour layers in Nala's `colors.xml`, how dynamic colour works through
  Chromium's theme and `BraveDynamicColors`, when to use a theme attribute or a Nala resource,
  icons, dimensions, and a test matrix.

Also in this release:

- Layout: motion, the desktop, Android and iOS guidance, and the checks move from
  `product-design.md` into `guides/`, which it links to. Agents that fetch the file by URL fetch
  each guide from the same version.
- Reduced motion links to the patterns in Nala's tabs and dialog components.

- Nala scales: the full spacing scale (including `spacing-none`), the radius scale with its
  platform radii, and a note that radius uses `xxl`, not `2xl`.
- Motion: duration and easing tables with values, `duration-xl` described as the loading delay
  rather than an animation length, which duration fits small and larger menus and popovers, where
  motion tokens are generated, how to copy them on Android and iOS, and a note that `easing-in`
  is symmetric.
- Checks: they scan only the lines a change adds and need bash, the hex check ignores HTML
  entities and odd lengths, the pixel check accepts icon sizes, and `npx leo-check` finds `--leo-*`
  variables that do not exist.

## 1.0.0 - 2026-10-12

First release of [`product-design.md`](product-design.md):

- Instructions for coding agents on applying the guidelines.
- Principles, Nala as the source of design values, and per-platform token sources.
- Code examples for web, desktop native views, Android and iOS.
- Motion, brand, writing and localization guidelines.
- Privacy and consent rules.
- Guidelines for agent and AI surfaces: identity, streaming, sources, permissions and trust markings.
- Platform conventions for the browser on desktop, Android and iOS, and for separate apps.
- A completion checklist with checks that can be run.
