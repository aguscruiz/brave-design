# Design styles

Brave's product design guidelines, written so that developers and their coding agents can apply
them in any repository and keep every surface at the same design standard.

The guidelines cover where design values come from ([Nala](https://github.com/brave/leo/), Brave's
design system), which platform conventions each surface follows (Chromium desktop, Android, iOS
and separate apps), motion, brand, how UI text is written and localized, privacy and consent,
agent and AI surfaces, and what to check before calling a surface done.

## Guidelines

- [Product design](product-design.md)

## Using them with an agent

Add this to your repository's `CLAUDE.md`, `AGENTS.md` or equivalent agent instructions:

```markdown
## Design

For any change to UI (layout, styling, components, icons, motion or user-visible text), read and
follow Brave's product design guidelines:
https://raw.githubusercontent.com/aguscruiz/design-styles/v1.0.0/product-design.md

Start with its "Using this file" section. This repository's own design guidelines, if any, add to
it.
```

The link is pinned to a release, so the rules your agent follows only change when you update the
version. To always follow the latest guidelines, replace `v1.0.0` with `main`. Releases are listed
in the [changelog](CHANGELOG.md).

If your agent cannot fetch URLs, copy `product-design.md` into your repository and reference the
local copy instead.

## Proposing a change

Open an issue or a pull request. Each change that is merged adds an entry to the
[changelog](CHANGELOG.md) and is released as a new version; see the changelog for what counts as a
major, minor or patch change. Changes are reviewed by the [code owners](.github/CODEOWNERS).

## Licence

[Mozilla Public License 2.0](LICENSE.md).
