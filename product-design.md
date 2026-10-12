# Product design

The design guidelines for every Brave product surface: features in the Brave browser on desktop,
Android and iOS, and separate apps such as desktop, mobile and terminal clients. This file says
where design values come from, which platform conventions a surface follows, and how its text is
written. It applies in any repository. Repository-specific details, such as where a project keeps
its semantic tokens, are in that repository's own design guidelines; [Bravebot](#example-bravebot)
is given as an example at the end.

## Using this file

This section is for coding agents and the people directing them.

1. **Find the surface.** Before changing UI, work out where it ships, and read the matching
   section:
   - a browser feature on desktop (native views or WebUI): [Desktop](#desktop-follow-chromium)
   - a browser feature on Android: [Android](#android-follow-chromium-and-material-3-expressive)
   - a browser feature on iOS: [iOS](#ios-follow-liquid-glass-and-the-human-interface-guidelines)
   - anything outside the browser: [Separate apps](#separate-apps)

   The [Principles](#principles), [Nala](#nala-is-the-source-of-design-values) and
   [Writing](#writing) sections apply to every surface.
2. **Find the repository's own layer.** Look for the repository's design guidelines, its semantic
   token file and its shared components before writing new UI. Reuse what is there.
3. **Precedence.** Where a repository's guidelines are more specific than this file, follow the
   repository. Where they contradict this file, follow this file and point out the conflict.
4. **Do not invent design values.** If no Nala token, semantic role or component fits, do not pick
   a value that looks close. Use the nearest semantic role, say so in the change description, and
   list it as an open question for a designer. The same applies to a missing icon, a missing
   string pattern, or a platform convention you cannot confirm.
5. **Do not approve for the person.** Never add a default, shortcut or auto-advance that answers a
   permission, privacy or payment decision, even to make a flow faster or a test pass.
6. **Check before finishing.** Go through [Before calling a surface done](#before-calling-a-surface-done)
   and report which checks you ran and which you could not.

## Principles

These hold on every platform.

- **Quiet, neutral chrome.** Surfaces are monochrome and dividers are hairlines. The accent colour
  marks primary actions, focus, selection and links only. Status colour (success, warning, error,
  info) is used only for status.
- **Every element has a purpose.** Each piece of UI helps a person read, decide or act at that
  moment. An element that does not is removed, merged into another, or moved to where it is needed.
  Text that repeats state the UI already shows is removed.
- **Content goes where it is needed.** A header carries identity and actions. A note about an item
  sits with the item. A problem with an input sits at the input. An application error is a toast or
  the platform's equivalent.
- **Nothing approves for the person.** No shortcut, gesture, default focus or auto-advance answers
  a decision for them. A permission prompt is answered by a deliberate action on that prompt.
- **Trust markings stay unmistakable.** Untrusted content and the trust label on a decision are
  visually distinct on every platform, and never by colour alone: a marking also has a text label
  or a border that survives high-contrast and forced-colour modes.
- **Every state is designed.** Hover, pressed, focus, disabled, empty, loading and error exist for
  every interactive element, list and panel. Nothing shifts layout when a label changes, a row
  appears, or content streams in.
- **Accessible by default.** Text and icons meet WCAG 2.2 AA contrast in light and dark. Every
  control has an accessible name, is reachable by keyboard or the platform's assistive navigation,
  and respects the platform's text-size, reduced-motion and increased-contrast settings.
- **Fast.** Typing, scrolling and opening a menu never wait on the rest of the UI. Each product
  sets its own performance budgets and holds them.

## Nala is the source of design values

Nala ([brave/leo](https://github.com/brave/leo), published as `@brave/leo`) is Brave's design
system. Colour, typography, spacing, radius, elevation, gradients and icons come from it. A Brave
surface does not define a value that Nala already defines.

### Where to read the tokens

Nala builds the same tokens for each platform. Use the build for the platform you are on.

| Platform | Tokens | Components and icons |
| --- | --- | --- |
| Web, Electron, browser WebUI | `@brave/leo/tokens/css/variables.css` (`--leo-*`), or a variant such as `variables-browser.css`; a Tailwind preset in `tokens/tailwind` | React wrappers in `@brave/leo/react`, web components in `@brave/leo/web-components`, SVGs in `@brave/leo/icons` |
| Desktop browser native views (C++) | `tokens/skia/nala_color_id.h` (`nala::kColor*` colour ids for the Chromium colour pipeline), `spacing.h`, `radius.h` | Leo icons in `icons-skia` |
| Android | `tokens/android` resources: `values` and `values-night` colours, `dimens.xml`, `styles.xml` | Leo icons as drawables |
| iOS | `tokens/ios-swift`: `Colors.xcassets`, `ColorSetAccessors.swift`, `Gradients.swift` | Leo icons in the asset catalog |
| Anything else (terminal, Rust, scripts) | `tokens/json/styles-*.json` | None |

### How to use them

- **Semantic tokens first.** Use the token named for a role (`color-text-primary`,
  `color-container-background`, `color-divider-subtle`, `color-systemfeedback-error-icon`,
  `font-default-regular`). Use a primitive (`color-primitive-primary-40`) only when no semantic
  token describes the role, and record why next to the use.
- **One semantic layer per product.** Each product names its roles once, in one file, and aliases
  each to a Nala token. Components read the roles, not raw Nala names, so a Nala upgrade or a theme
  change is made in one file.
- **No raw values.** No hex colours, no hand-written shadows, no pixel values off the Nala spacing
  scale (2, 4, 8, 12, 16, 24, 32, 40, 48 and up). A value Nala does not have is added as a role in
  the semantic layer, and the gap is raised with Nala so the role can alias a real token later.
- **Copies name their source.** Where a platform cannot read Nala at build time, such as terminal
  colours, a literal value is copied from the Nala JSON and the comment names the token it copies
  (for example `color-primitive-brands-rorange-1` for Brave orange).
- **Light and dark come from Nala.** Use Nala's light and dark values and its theme switching
  (`data-theme`, `values-night`, the asset catalog's appearances). Do not derive one theme from the
  other with opacity or filters.
- **Typography uses the system font.** Nala's type tokens set the system UI stack (SF on Apple
  platforms, Roboto on Android, Segoe UI on Windows). Use the role tokens (`font-heading-h1` to
  `-h4`, `font-large-*`, `font-default-*`, `font-small-*`, `font-x-small-*`, `font-monospace-*`)
  instead of setting size, weight and line height separately.
- **Icons come from Nala.** Take every icon from the [Nala repository](https://github.com/brave/leo/),
  through the platform build in the table above, and check that the icon name exists in the pinned
  Nala version. Do not draw icons, copy SVGs from elsewhere, or add another icon set (Material
  Symbols, Lucide, Font Awesome, emoji used as icons). If Nala has no icon for a concept, use the
  closest Nala icon, flag it in the change description, and request the icon in Nala. The only
  exception is where a platform convention requires a system symbol (an iOS system control, an
  Android system bar).
- **Components before composition.** Use a Nala component when one exists. When none fits, build
  from Nala tokens and icons, keep it next to the other shared components, and propose it to Nala.
- **Pin and upgrade deliberately.** Nala is pinned to a version or commit in each repository's
  package manifest. An upgrade is its own change, checked in light and dark on every surface that
  uses it.

## Writing

Text is part of the design. It follows the same rule as everything else: every word has a purpose.

### Voice

- **Plain and direct.** Say what happens, in the words the person would use. Prefer short, common
  words and short sentences. No jargon, internal code names, or engineering terms (no "request
  failed with 403", no "invalid payload").
- **Calm and honest.** No exclamation marks, no hype, no guilt ("No thanks, I don't care about
  privacy"), no false urgency. Do not overstate what a feature does or what Brave can guarantee.
- **Address the person as "you".** Refer to Brave as "Brave", not "we", unless a repository's
  guidelines say otherwise.
- **Consistent names.** Write Brave product and feature names exactly as Brave writes them
  (for example Brave Shields, Brave Search, Brave Wallet, Leo) and use one term for one thing
  throughout a surface. Do not rename a setting or feature in a new surface.

### Patterns

- **Capitalisation follows the platform** and the browser UI around the surface. Within one
  surface, it is consistent.
- **Buttons are verbs** that name the result ("Save", "Delete history", "Allow once"), not "OK" or
  "Yes" when a specific verb fits. The two buttons of a decision are never ambiguous when read
  alone.
- **Errors say what happened and what to do next**, in that order, in one or two sentences. They
  do not blame the person and do not show raw error codes as the main message; a code, if useful
  for support, comes after the explanation.
- **Permission and privacy prompts say who is asking, for what, and what happens on each
  choice.** Declining is as easy and as clearly worded as accepting. A prompt that sends data off
  the device says so.
- **Empty states say what will appear here and how to get it**, with one action if one helps.
- **No filler.** No "Please", "Successfully", "Simply" or "Just" unless removing it changes the
  meaning. A success state that the UI already shows needs no message.
- **Numbers, dates and times** are formatted by the platform's locale APIs, never by hand.

### Localization

Brave ships in many languages. Every surface is built so it can be translated without code
changes.

- **All UI text is in string resources**, in the platform's own system (GRD/GRDP strings in
  brave-core, `strings.xml` on Android, string catalogs on iOS, the repository's message files
  elsewhere). No user-visible string is hard-coded.
- **Strings are written in US English** as the source language.
- **Whole sentences only.** Never build a sentence by concatenating strings or by inserting a
  translated word into another string. Use placeholders for values, with a translator description
  that says what each placeholder holds.
- **Plurals and gender use the platform's message format** (ICU plurals, Android quantity strings,
  iOS plural variants), never `count == 1 ? "item" : "items"`.
- **Every string has a description for translators** saying where it appears and what it does,
  especially for short strings ("Clear" as a verb or adjective).
- **Leave room to grow.** Translated text is often 30–40% longer than English, and short labels
  can double. Labels wrap or the layout adapts; text is not truncated where the full text matters,
  and nothing has a fixed width sized to the English string.
- **Right-to-left works.** Layout uses logical properties (start and end, not left and right).
  Directional icons such as back arrows mirror; icons that are not directional, such as a clock or
  a checkmark, do not. Check the surface in an RTL language before calling it done.
- **No text in images.** Text is never baked into an icon, illustration or screenshot.

## Surfaces inside the Brave browser

A surface that ships in the Brave browser
([brave/brave-core](https://github.com/brave/brave-core)) looks and behaves like the browser
around it. It uses Nala as above and follows the platform conventions of the browser it is in.

Each browser release is built on a Chromium milestone and targets a range of OS versions, and
those set the conventions it follows. Before designing, check which Chromium version the target
brave-core branch is on and which OS versions it supports. Design for the newest of those, and
check that the surface still works on the oldest, falling back to the previous pattern where a
newer one is unavailable. When Chromium, Google or Apple change a system pattern, the surface
adopts it in the browser release that adopts it, not before.

### Desktop: follow Chromium

- Use Chromium's surfaces for what they are for: the side panel for a persistent companion view,
  a bubble anchored to its toolbar button or omnibox icon for a short task, a tab-modal dialog for a
  decision about the current page, and a WebUI page (`brave://`) for settings and full-page tools.
  Do not build a surface Chromium already provides.
- Match Chromium's layout: its dialog widths and button order per OS, its menu structure, its
  spacing and corner radii (Chromium's layout provider supplies these to native views), and its
  focus rings.
- Native views take colours from `nala::kColor*` ids through the colour mixer so they follow the
  browser's theme, including custom themes and high contrast. WebUI uses `@brave/leo` components
  and `--leo-*` variables.
- Follow Chromium's keyboard and accessibility behaviour: focus order, Esc to close, F6 between
  panes, and the platform's screen reader.

### Android: follow Chromium and Material 3 Expressive

- Use Chromium Android's surfaces: bottom sheets for short tasks, full-screen activities for
  settings, and the toolbar and menu entry points the browser already has.
- Follow Material 3 Expressive for components, shape, type emphasis and motion (spring-based
  transitions). Map Nala's `schemes` colour tokens (`primary`, `on-primary`, `primary-container`,
  `surface`, `outline` and the rest, which follow Material's colour roles) onto the Material
  theme, so Material components draw in Brave's colours.
- Draw edge to edge with correct insets, support predictive back, keep touch targets at least
  48dp, and respect the system font scale and dark theme.

### iOS: follow Liquid Glass and the Human Interface Guidelines

- Follow Apple's [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
  for navigation, sheets, menus, gestures and text.
- Liquid Glass is the material for controls and navigation (toolbars, tab bars, sheets, floating
  buttons), which sit above the content. Use the system's glass material and standard components
  instead of drawing opaque bars or imitating glass with blur and colour. Content is not glass.
- Tint glass with a Nala colour only for a primary action, and sparingly. Keep text on glass at the
  system's legibility settings.
- Support Dynamic Type, Reduce Transparency, Increase Contrast, Reduce Motion, safe areas, the
  back-swipe gesture, and touch targets of at least 44pt.
- Colours come from Nala's asset catalog so they follow light and dark. Use SF Symbols for system
  controls and Leo icons for Brave concepts.

## Separate apps

A Brave app that is not part of the browser (a desktop app, a mobile app, a terminal tool) holds
the same standard as the browser does.

- Use Nala components and tokens wherever the platform can use them, and follow the principles
  above.
- Follow the conventions of the OS the app runs on: the macOS HIG on macOS, the platform's own
  conventions on Windows and Linux, Material 3 Expressive on Android, and Liquid Glass and the HIG
  on iOS. Where Nala and a platform convention disagree on behaviour (a gesture, where a button
  goes, how a sheet is dismissed), follow the platform. Where they disagree on a value (a colour,
  a size), use Nala.
- A terminal interface cannot use Nala components. It takes colours from Nala as described under
  "Copies name their source", and respects `NO_COLOR` and the terminal's own palette.

## Before calling a surface done

- Every value comes from a Nala token or a semantic role that aliases one.
- Every icon is a Nala icon, or a system symbol where the platform requires one.
- The surface follows the platform conventions listed above for where it ships.
- It has been seen, in light and dark, at the smallest and largest supported size, with large text,
  with high contrast, and with reduced motion.
- Every state is designed, and trust markings are distinct without colour.
- Every string is in a string resource, has a translator description, follows [Writing](#writing),
  and the layout has been checked with long text and in a right-to-left language.
- Any gap (a missing token, component, icon or convention) is listed in the change description.
- The repository's own design checks pass.

## Example: Bravebot

[Bravebot](https://github.com/brave/bravebot/) applies these guidelines as follows:

- Its desktop app's layout, patterns, performance budgets and gates are in the
  [design skill](https://github.com/brave/bravebot/blob/main/agents/skills/design/SKILL.md), which
  applies to `ui/`.
- The semantic layer for the desktop app is `ui/src/renderer/styles/tokens.css`.
- Terminal colours copied from Nala are in `crates/tui/src/theme.rs`.
- Nala is pinned in `ui/package.json`.
