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

   These sections apply to every surface: [Principles](#principles),
   [Nala](#nala-is-the-source-of-design-values), [Motion](#motion),
   [Interaction](#interaction), [Brand](#brand), [Writing](#writing) and
   [Privacy and consent](#privacy-and-consent). A surface where an AI
   model or agent produces content or takes actions also follows
   [Agent and AI surfaces](#agent-and-ai-surfaces).
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
  scale (`spacing-xs` 2, `s` 4, `m` 8, `l` 12, `xl` 16, `2xl` 24, `3xl` 32, `4xl` 40, `5xl` 48 and
  up), no radius off the radius scale, and no icon size off the icon scale (`icon-xs` 16, `s` 18,
  `m` 20, `l` 24, `xl` 32). A 1px hairline border is the one allowed literal. A value Nala does not have is added as a role in
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
- **A component's decisions belong to the component.** Tokens are the values a component can use.
  The component decides which one: its shape, sizes, padding, kinds and states. For example, every
  Nala button is a pill (`radius-full`), and an icon-only button (`fab`) is a circle. Its height
  comes from its size (`tiny` to `jumbo`), and its colours come from its kind (`filled`, `outline`,
  `plain`, `plain-faint`, `hero`). Do not restyle a Nala component, and do not rebuild one from
  tokens with different choices. Using only real tokens does not make a lookalike correct.
- **Where the component cannot be imported**, as in a standalone prototype, a mock-up, or a
  platform build that lacks it, recreate it from its source in
  [`src/components`](https://github.com/brave/leo/tree/main/src/components). Match its shape,
  sizes, kinds and states, and say in the change description that it is a copy and which Nala
  version it copies.
- **Pin and upgrade deliberately.** Nala is pinned to a version or commit in each repository's
  package manifest. An upgrade is its own change, checked in light and dark on every surface that
  uses it.

### Examples

Names below follow Nala's generated output. If a name does not resolve, check the generated file
for the platform (listed in the table above) rather than guessing a similar one.

**Web: a semantic layer, then components that read it**

```css
/* The product's semantic layer: one file, every role aliases a Nala token. */
:root {
  --panel-background: var(--leo-color-container-background);
  --panel-border: var(--leo-color-divider-subtle);
  --panel-text: var(--leo-color-text-primary);
  --panel-text-muted: var(--leo-color-text-secondary);
}

/* Do: components read roles and Nala scales. */
.panel {
  background: var(--panel-background);
  border: 1px solid var(--panel-border);
  border-radius: var(--leo-radius-m);
  padding: var(--leo-spacing-xl);
  gap: var(--leo-spacing-m);
  font: var(--leo-font-default-regular);
  color: var(--panel-text);
  transition: background var(--leo-duration-s) var(--leo-easing-out);
}

/* Don't: raw values, off-scale sizes, hand-written shadow and timing. */
.panel {
  background: #fff;
  border: 1px solid #e4e4e5;
  border-radius: 7px;
  padding: 15px;
  font-size: 14px;
  color: #1c1c1d;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
  transition: background 0.25s ease;
}
```

```tsx
// Do: Nala components and icons.
import Button from '@brave/leo/react/button'
import Icon from '@brave/leo/react/icon'

<Button kind="filled" onClick={save}>Save</Button>
<Button kind="plain-faint" onClick={cancel}>Cancel</Button>
<Icon name="settings" />

// Don't: a hand-styled <button>, or an icon from another set.
import { Settings } from 'lucide-react'
```

**Desktop native views (C++)**

```cpp
// Do: resolve a Nala colour id through the colour provider, so themes and high contrast apply.
SkColor text = GetColorProvider()->GetColor(nala::kColorTextPrimary);

// Don't: a literal colour that ignores the theme.
SkColor text = SkColorSetRGB(0x1C, 0x1C, 0x1D);
```

**Android**

```xml
<!-- Do: Nala colour resources, which switch with values-night. -->
<TextView
    android:textColor="@color/text_primary"
    android:background="@color/container_background" />

<!-- Don't -->
<TextView
    android:textColor="#1C1C1D"
    android:background="#FFFFFF" />
```

**iOS**

```swift
// Do: Nala colours from the asset catalog, which follow light and dark.
Text("Saved").foregroundStyle(Color(braveSystemName: .textPrimary))

// Don't
Text("Saved").foregroundStyle(Color(red: 0.11, green: 0.11, blue: 0.11))
```

## Motion

Motion explains what changed. It is never decoration.

- **Use Nala's motion tokens.** Durations are `duration-xs` (50ms), `s` (100ms), `m` (200ms),
  `l` (300ms) and `xl` (500ms). Easings are `easing-out` for things that enter, `easing-in` for
  things that leave, `easing-in-out` for things that move or change in place, and `easing-linear`
  only for progress. `easing-in-out-back` overshoots and is kept for rare moments of emphasis. On
  Android and iOS, use the platform's system motion (Material springs, SwiftUI's standard
  animations) for platform components, and Nala durations for anything custom.
- **Short and proportional.** Small changes (hover, press, a toggle) use `xs` or `s`. Menus,
  tooltips and popovers use `s` or `m`. Panels, sheets and page transitions use `m` or `l`. Nothing
  routine uses more than `l`. Leaving is quicker than entering.
- **Animate what is cheap.** Animate opacity and transform, not width, height, top or left, so
  motion never drops frames or shifts layout.
- **Never block.** A person can act during an animation. Nothing waits for an animation to finish
  before responding to input.
- **No loops and no flashing.** Nothing animates continuously except a progress indicator while
  work is happening. Nothing flashes more than three times a second.
- **Reduced motion.** When the platform asks for reduced motion, replace movement with a short fade
  or an instant change. No information is carried by motion alone.

## Interaction

### Destructive actions

- **Undo over confirmation.** An action that can be undone happens at once and offers undo (a
  toast, a snackbar, or the platform's equivalent) long enough to be noticed and used.
- **Confirm only what cannot be undone.** The confirmation names the consequence, and its button
  repeats it ("Delete 12 files", "Clear all history"), not "OK" or "Yes". Default focus is on the
  choice that keeps things as they are.

### Loading and network

- **No flicker.** Work that usually finishes quickly shows no indicator at first. Show one only
  after a delay of `duration-xl` (500ms), and once shown, keep it long enough to be read.
- **Match the indicator to what is known.** A skeleton when the shape of the content is known,
  sized like the content so nothing shifts when it arrives. A determinate progress bar when the
  length of the work is known. A labelled spinner otherwise.
- **Offline is a state, not an error.** When the network is unavailable, say so, keep what is
  already loaded, offer a retry, and recover on its own when the connection returns. Do not
  discard what the person typed.

### Forms

- **Labels stay visible.** Every field has a label outside the field. A placeholder is an example
  of the input, never the label.
- **Mark required or optional, whichever is fewer**, and do it the same way throughout a surface.
- **Validate when the person leaves a field or submits**, not on every keystroke. Once an error is
  shown, clear it as soon as the input is fixed.
- **Errors sit at the field** and are linked to it for assistive technology (for example
  `aria-describedby` on the web). On submit, move focus to the first field with an error.
- **Do not disable submit to signal invalid input.** Let the person submit, then show what needs
  fixing.
- **Never block paste or autofill.** Use the right input type and autofill hints (`type`,
  `autocomplete`, Android autofill hints, iOS `textContentType`) so password managers and the
  platform keyboard work.

### Focus

- **Focus follows the person.** Opening a dialog, sheet or menu moves focus into it. A modal keeps
  focus inside it until it closes. Closing returns focus to the element that opened it.
- **Focus is always visible**, using the platform's focus ring. Never remove a focus indicator
  without replacing it.
- **New content does not steal focus.** A row that appears, a toast or a streamed reply leaves
  focus where it was.
- **Nothing is available only on hover.** Anything revealed on hover is also revealed on keyboard
  focus and reachable on touch, where there is no hover.

### Keyboard shortcuts

- **Do not override browser or OS shortcuts.** Check a new shortcut against Chromium's and the
  OS's before using it. A shortcut that conflicts is not shipped.
- **Shortcuts are accelerators.** Every shortcut has a visible control that does the same thing.
- **Show the platform's keys.** ⌘ and ⌥ on macOS and iOS, Ctrl and Alt elsewhere, in the
  platform's order and format, in tooltips and menus.
- **Esc closes one layer at a time**: the topmost menu, popover or dialog, then the next. While a
  model is generating or an agent is working, Esc stops it first; the next Esc closes as usual.

## Brand

The brand is used sparingly so that it means something.

- **Brave orange is for the brand mark, not the UI.** The accent for buttons, links, focus and
  selection is Nala's interactive colour (`color-button-background`, `color-text-interactive`).
  Brave orange (`color-primitive-brands-rorange-*`) appears in the logo and in places Nala's brand
  tokens assign it.
- **Logos come from Nala.** Use the Brave icon for the release channel the surface represents
  (`brave-icon-release-color`, `brave-icon-beta-color`, `brave-icon-nightly-color`,
  `brave-icon-dev-color`), or `brave-icon-monochrome` where colour is not possible. Product marks
  are the `product-*` icons (for example `product-brave-leo`, `product-brave-wallet`). Do not
  redraw, recolour, stretch, rotate, crop, outline or add effects to a logo, and leave clear space
  around it.
- **The lion is a logo, not an icon.** Do not use it as a generic button, status or decoration.
- **Gradients come from Nala and are used for what they are named for:** `hero` for hero areas,
  `brave-release`, `brave-beta` and `brave-nightly` for release-channel identity, and the `braveai`
  gradients for AI identity. Never put body text on a gradient, and never invent one.
- **Third-party marks** use Nala's `*-color` brand icons where one exists, unmodified. Do not
  imply a partnership the product does not have.
- **Marketing, launch and press material** goes through Brave's brand team. These guidelines cover
  product UI only.

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

## Privacy and consent

Privacy is the reason people choose Brave. A surface never trades it for a metric.

- **Off until chosen.** A feature that sends data off the device, beyond what it needs to work at
  all, is off until the person turns it on. Nothing is opted in silently, by an update, or by a
  pre-checked box.
- **Say what leaves the device.** Before data is sent, say what is sent, to whom (Brave, a named
  third party, or nowhere), why, and whether it is stored. Link to the relevant part of the privacy
  policy for the detail.
- **Equal choices.** Accepting and declining have the same size, weight and prominence, and are
  equally easy to reach. Declining is never hidden behind a link, a second screen or a scroll.
- **Ask once, at the moment of need.** Ask when the person first uses the feature, not at launch
  or in a sequence of prompts. A "no" is respected: do not ask again unless the person starts the
  feature again. A declined feature stays available in settings.
- **Every choice can be changed.** Anything a person turns on can be turned off where they would
  look for it, and turning it off is as easy as turning it on.
- **No dark patterns.** No confirmshaming, false urgency, fake scarcity, disguised ads, nagging,
  hidden costs, or forced continuity. A paid feature says it is paid before the flow begins.
- **Promotions are rare and dismissable.** A callout for a feature appears where it is relevant,
  can be dismissed in one action, and "Don't show again" is honoured.
- **Measure only through privacy-preserving analytics.** Usage is measured only through the
  product's approved system (P3A in the browser), which follows the person's analytics setting.
  No identifiers, no content, no URLs, and no third-party analytics or tracking SDKs. If a question
  cannot be answered that way, it is not measured.

## Agent and AI surfaces

These apply wherever an AI model writes content or an agent acts for the person: Leo in the
browser, Bravebot, and anything similar.

### Identity

- **AI content is labelled.** Content written by a model is always distinguishable from content
  written by the person, by Brave, or by a web page. Use the `braveai` gradients for AI identity
  (an entry point, the assistant's mark), sparingly. They are not a background for whole surfaces.
- **Say where the model runs.** The surface says whether a model runs on the device, on Brave's
  servers or with a named third party, and what is sent to it, following
  [Privacy and consent](#privacy-and-consent).

### Streaming and control

- **Stream in place.** Text appears where it will stay. Content above it does not move, and
  formatting (lists, code blocks, tables) settles without reflowing what is already shown.
- **Scrolling follows only if the person is at the bottom.** If they scroll up, streaming does not
  pull them back down; a "jump to latest" control appears instead.
- **Stop is always available.** While a model is generating or an agent is working, a visible stop
  control replaces send, and Esc stops on desktop (see [Keyboard shortcuts](#keyboard-shortcuts)).
  Stopping keeps what was produced and marks it as stopped.
- **The person stays in control of the conversation.** They can copy, retry, edit their message,
  and delete a conversation. The surface says how long conversations are kept.

### Showing the work

- **Say what the agent is doing**, in plain words, as it does it ("Searching the web", "Reading
  example.com", "Editing 3 files"). Each step can be expanded to show what went in and what came
  out. No fake progress bars and no spinner without a label on long work.
- **Cite sources.** A claim drawn from the web, a page or a file links to its source where it is
  made, showing the domain or file name. Content without a source is not presented as if it had
  one.
- **Errors are not answers.** When a model or tool fails, say what failed and offer a retry. Never
  present a failure, a refusal or a partial result as a complete answer, and do not overstate
  certainty.

### Actions and permissions

- **Ask before acting.** Before an action with effects outside the conversation (sending,
  submitting, purchasing, deleting, writing files, sharing data, changing settings), the agent asks.
  The prompt shows the exact action, its target, and the data involved, in words and not only as
  raw parameters.
- **Choices are specific.** For example "Allow once", "Always allow on example.com" and "Deny".
  Default focus is never on an allow choice, and nothing allows automatically, including after a
  timeout.
- **Say where the request came from.** A prompt makes clear whether the action follows from what
  the person asked, or from content the agent read (a page, an email, a document, tool output).
- **Show what was done.** After an action, the result is shown in the conversation, with undo where
  the action allows it.

### Screen readers

- **Announce a reply once.** Streamed text is not read token by token. A polite live region (or
  the platform's announcement API) says that a reply is being written, then announces it when it
  is complete or stopped.
- **Announce agent steps and their outcome** ("Reading example.com", "Done", "Failed: could not
  reach example.com") politely, without moving focus.
- **Permission prompts take focus.** A prompt that needs a decision is announced and receives
  focus on the least permissive choice, so a screen reader user meets it before anything else.
- **Messages have structure.** Each message is navigable as a unit, says who wrote it (the person,
  the assistant, or the named source of untrusted content), and keeps headings, lists and code
  blocks as real structure rather than styled text.

### Trust markings

- **Untrusted content is contained and labelled.** Content the agent read from outside the
  person's own instructions (web pages, emails, documents, tool output) is shown in a container
  with a border and a text label naming its source. The marking survives high contrast and forced
  colours, and does not rely on colour.
- **Untrusted content never looks like UI.** Text, links or instructions inside untrusted content
  are never rendered as buttons, prompts, or messages from the person or Brave.
- **Trust labels on decisions are fixed.** The label on a permission prompt is drawn by the
  surface, never from model or page output, and looks the same everywhere.

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
- Every control that Nala has a component for uses that component, or copies it exactly
  (shape, sizes, kinds and states).
- Every icon is a Nala icon, or a system symbol where the platform requires one.
- The surface follows the platform conventions listed above for where it ships.
- It has been seen, in light and dark, at the smallest and largest supported size, with large text,
  with high contrast, and with reduced motion.
- Every state is designed, and trust markings are distinct without colour.
- Every string is in a string resource, has a translator description, follows [Writing](#writing),
  and the layout has been checked with long text and in a right-to-left language.
- Motion uses Nala durations and easings, and the surface works with reduced motion.
- Destructive actions offer undo or a specific confirmation, loading and offline states exist,
  forms follow [Forms](#forms), focus moves and returns correctly, and new shortcuts conflict
  with nothing in the browser or OS.
- Anything that sends data off the device follows [Privacy and consent](#privacy-and-consent),
  and any measurement goes only through the approved analytics system.
- An AI or agent surface follows [Agent and AI surfaces](#agent-and-ai-surfaces), including stop,
  sources, permission prompts, screen reader announcements and trust markings.
- Any gap (a missing token, component, icon or convention) is listed in the change description.
- The repository's own design checks pass.

### Checks you can run

These find the common mistakes. A match is not always wrong (a token file legitimately contains
hex values), but each one needs a reason. Run them on the files the change touches, and adjust the
paths to the repository.

```sh
BASE=main  # the branch the change will merge into
# Compares the working tree with $BASE; stage new files first so they are included.
changed() { git diff --name-only "$BASE" -- "$@"; }

# Web: raw colours, off-scale pixel values, hand-written shadows and timing, foreign icon sets.
WEB=('*.css' '*.scss' '*.ts' '*.tsx' '*.svelte')
changed "${WEB[@]}" | xargs grep -nE '#[0-9a-fA-F]{3,8}\b|rgba?\(|hsla?\('
changed "${WEB[@]}" | xargs grep -noE '\b[0-9]+px\b' \
  | grep -vE ':(0|1|2|4|8|12|16|24|32|40|48|64|80|96|160)px$'
changed "${WEB[@]}" | xargs grep -nE 'box-shadow:' | grep -v 'var(--leo-effect'
changed "${WEB[@]}" | xargs grep -nE '(transition|animation)[^;]*[0-9.]+m?s\b' \
  | grep -v 'var(--leo-duration'
changed "${WEB[@]}" | xargs grep -nE \
  "from '(lucide|react-icons|@mui/icons|@heroicons|@fortawesome|material-symbols)"

# Every Leo icon name used exists in the pinned Nala version.
changed "${WEB[@]}" | xargs grep -ohE '<(Icon|leo-icon)[^>]*name="[a-z0-9-]+"' \
  | sed -E 's/.*name="([^"]+)".*/\1/' | sort -u | while read -r icon; do
    [ -f "node_modules/@brave/leo/icons/$icon.svg" ] || echo "missing icon: $icon"
  done

# Desktop native views: literal colours instead of nala::kColor* ids.
changed '*.cc' '*.h' | xargs grep -nE 'SkColorSetA?RGB|SK_Color[A-Z]'

# Android: literal colours and sizes in resources and layouts.
changed '*.xml' | xargs grep -nE '"#[0-9a-fA-F]{6,8}"|"[0-9]+(dp|sp)"'

# iOS: literal colours and font sizes.
changed '*.swift' | xargs grep -nE 'Color\(red:|UIColor\(red:|\.system\(size:'
```

For web surfaces, run an automated accessibility check (for example
[axe-core](https://github.com/dequelabs/axe-core) through Playwright) and take screenshots in each
mode the surface must support:

```ts
await page.emulateMedia({ colorScheme: 'dark', reducedMotion: 'reduce', forcedColors: 'active' })
await page.evaluate(() => {
  document.documentElement.dataset.theme = 'dark'
  document.documentElement.dir = 'rtl'
})
```

Automated checks catch a fraction of accessibility problems. Also go through the surface with a
keyboard only and with the platform's screen reader.

## Example: Bravebot

[Bravebot](https://github.com/brave/bravebot/) applies these guidelines as follows:

- Its desktop app's layout, patterns, performance budgets and gates are in the
  [design skill](https://github.com/brave/bravebot/blob/main/agents/skills/design/SKILL.md), which
  applies to `ui/`.
- The semantic layer for the desktop app is `ui/src/renderer/styles/tokens.css`.
- Terminal colours copied from Nala are in `crates/tui/src/theme.rs`.
- Nala is pinned in `ui/package.json`.
