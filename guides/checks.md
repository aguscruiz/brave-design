# Checks you can run

Part of Brave's [product design guidelines](../product-design.md). Run these before going through
[Before calling a surface done](../product-design.md#before-calling-a-surface-done).

These find the common mistakes. A match is not always wrong (a token file legitimately contains
hex values, and the hex check also matches issue references such as `#1234`), but each one needs
a reason. They look only at the lines the change adds, printed as `file:line:text`, and need
bash, not POSIX `sh`. Adjust the paths to the repository.

```bash
BASE=main  # the branch the change will merge into
# Compares the working tree with $BASE; stage new files first so they are included.
added() {
  git diff -U0 --diff-filter=d "$BASE" -- "$@" | awk '
    /^\+\+\+ b\// { file = substr($0, 7); next }
    /^@@/ { match($0, /\+[0-9]+/); line = substr($0, RSTART + 1, RLENGTH - 1); next }
    /^\+/ { print file ":" line ":" substr($0, 2); line++ }'
}

# Web: raw colours, off-scale pixel values, hand-written shadows and timing, foreign icon sets.
WEB=('*.css' '*.scss' '*.ts' '*.tsx' '*.svelte')
added "${WEB[@]}" | grep -E \
  '(^|[^&[:alnum:]_])#([[:xdigit:]]{3,4}|[[:xdigit:]]{6}|[[:xdigit:]]{8})\b|rgba?\(|hsla?\('
# 18 and 20 are icon sizes, so they pass here. Radii 26, 28 and 38 are
# platform radii and need a reason.
added "${WEB[@]}" | awk -F: '{
  t = $0; sub(/^[^:]*:[^:]*:/, "", t)
  while (match(t, /[0-9.]*[0-9]px/)) {
    v = substr(t, RSTART, RLENGTH - 2); t = substr(t, RSTART + RLENGTH)
    if (v !~ /^(0|1|2|4|8|12|16|18|20|24|32|40|48|64|80|96|160)$/)
      print $1 ":" $2 ": " v "px"
  }
}'
added "${WEB[@]}" | grep -E 'box-shadow:' | grep -v 'var(--leo-effect'
added "${WEB[@]}" | grep -E '(transition|animation)[^;]*[0-9.]+m?s\b' \
  | grep -v 'var(--leo-duration'
added "${WEB[@]}" | grep -E \
  "from '(lucide|react-icons|@mui/icons|@heroicons|@fortawesome|material-symbols)"

# Web: --leo-* variables that do not exist in the pinned Nala version. Run from the project
# root; leo-check is the bin that @brave/leo ships (src/scripts/audit-tokens.js in Nala).
npx leo-check

# Every Leo icon name used exists in the pinned Nala version.
added "${WEB[@]}" | grep -ohE '<(Icon|leo-icon)[^>]*name="[a-z0-9-]+"' \
  | sed -E 's/.*name="([^"]+)".*/\1/' | sort -u | while read -r icon; do
    [ -f "node_modules/@brave/leo/icons/$icon.svg" ] || echo "missing icon: $icon"
  done

# Desktop native views: literal colours instead of nala::kColor* ids.
added '*.cc' '*.h' | grep -E 'SkColorSetA?RGB|SK_Color[A-Z]'

# Android: literal colours and sizes in resources and layouts.
added '*.xml' | grep -E '"#[0-9a-fA-F]{6,8}"|"[0-9]+(dp|sp)"'

# iOS: literal colours and font sizes.
added '*.swift' | grep -E 'Color\(red:|UIColor\(red:|\.system\(size:'
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
