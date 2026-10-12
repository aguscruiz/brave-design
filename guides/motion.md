# Motion

Part of Brave's [product design guidelines](../product-design.md). It applies to every surface.

Motion explains what changed. It is never decoration.

| Token | Value | Use |
| --- | --- | --- |
| `duration-xs` | 50ms | Hover, press and toggle. Also the reduced-motion replacement. |
| `duration-s` | 100ms | Small changes, tooltips, small menus and popovers. |
| `duration-m` | 200ms | Larger menus and popovers, small panels. |
| `duration-l` | 300ms | Panels, sheets, page transitions. The longest routine duration. |
| `duration-xl` | 500ms | Not an animation length. The delay before a loading indicator. |

| Token | Value | Use |
| --- | --- | --- |
| `easing-out` | `cubic-bezier(0, 0, 0.58, 1)` | Things that enter. |
| `easing-in` | `cubic-bezier(0.5, 0, 0.5, 1)` | Things that leave. |
| `easing-in-out` | `cubic-bezier(0.42, 0, 0.58, 1)` | Things that move or change in place. |
| `easing-linear` | `cubic-bezier(0, 0, 1, 1)` | Progress only. |
| `easing-in-out-back` | `cubic-bezier(0.7, -0.4, 0.4, 1.4)` | Overshoots. Rare emphasis only. |

`easing-in` is symmetric, so it is closer to an in-out curve than a pure accelerate. It is still
the token for exits. Do not write your own curve in its place; raise it with Nala instead.

- **Use Nala's motion tokens.** They are generated for the web only: `--leo-duration-*` and
  `--leo-easing-*` in the CSS variables, the `duration` and `easing` exports in `variables.ts`, and
  `DurationS`, `EasingOut` and so on in the JSON. Skia, Android and iOS have none. On Android and
  iOS, use the platform's system motion (Material springs, SwiftUI's standard animations) for
  platform components. For anything custom, copy the values above and name the token, as
  described under [Copies name their source](../product-design.md#how-to-use-them):

  ```kotlin
  // duration-s, easing-out
  PathInterpolator(0f, 0f, 0.58f, 1f)
  ```

  ```swift
  // duration-s, easing-out
  .timingCurve(0, 0, 0.58, 1, duration: 0.1)
  ```

- **Short and proportional.** Hover, press and toggle use `xs` or `s`. Tooltips and small menus
  and popovers use `s`, larger ones use `m`. Panels, sheets and page transitions use `m` or `l`.
  Nothing routine uses more than `l`. Leaving is quicker than entering, so give an exit the
  shorter duration.
- **Animate what is cheap.** Animate opacity and transform, not width, height, top or left, so
  motion never drops frames or shifts layout.
- **Never block.** A person can act during an animation. Nothing waits for an animation to finish
  before responding to input.
- **No loops and no flashing.** Nothing animates continuously except a progress indicator while
  work is happening. Nothing flashes more than three times a second.
- **Reduced motion.** When the platform asks for reduced motion, replace movement with a short fade
  or an instant change. No information is carried by motion alone. Nala's components show both
  patterns on the web: CSS transitions use `@media (prefers-reduced-motion: reduce)`
  ([tabs.svelte](https://github.com/brave/leo/blob/main/src/components/tabs/tabs.svelte) sets `transition: none`), and JS-driven
  transitions call [`prefersReducedMotion()`](https://github.com/brave/leo/blob/main/src/components/prefersReducedMotion.ts) and
  switch to `duration-xs` with no scale, as [dialog.svelte](https://github.com/brave/leo/blob/main/src/components/dialog/dialog.svelte)
  does. Read the preference each time the transition starts, so a change in the OS setting applies
  mid-session.
