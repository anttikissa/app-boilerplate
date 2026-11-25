# Style

The look of every client. Values marked placeholder are the
boilerplate's; a new project replaces them.

Every component uses these values and the app's existing CSS. When a
needed value is missing, pick a plain default, add it here marked
placeholder and tell the user.

## App icon

- Placeholder: a gray circle, oklch(0.55 0 0) (#717171), with a white
  bold "?", on a light gray square, #f2f2f2 (`public/icon.svg`).
- The dev environment's icon adds an orange ring, oklch(0.72 0.17 55)
  (#f3821d), so a dev tab or home screen icon never passes for
  production (`public/icon-dev.svg`).
- PNG sizes: 32 (favicon), 180 (iOS home screen), 192 and 512 (web
  manifest), 1024 (iOS app). `NEW-PROJECT.md` has the command that
  makes them.

## Dev environment

- The dev site is marked with orange, oklch(0.72 0.17 55) (#f3821d):
  the icon ring and the theme color.

## Look

- Contrast: every shape stands out from what lies behind it, by WCAG
  contrast ratio. A fill: at least 1.2:1 in a light theme and 1.35:1
  in a dark one (aim for 1.5:1). A border that is the only edge: at
  least 2:1 in a light theme; in a dark one 2.5:1 at 1 px or wider and
  3:1 below 1 px. No edge is thinner than 0.5 px. Check it in every
  state.
- Dark type: light text on dark looks heavier (the irradiation
  illusion), so a dark theme sets each font weight one step lighter:
  400 becomes 300.
- Review: with a dark theme, add dark screenshots for a new component
  or a new combination of colors or backgrounds; UI built from existing
  ones needs one theme.
- Shapes: pick one family and keep it. Pills mix with square corners,
  never with lightly rounded boxes. A rounded box inside another takes
  the parent's radius minus the padding between them (concentric).
- Pill padding: side padding at least half the pill's height; less
  only beside a trailing mark such as a chevron.
- Focus: one focus style for the whole app, keyboard only
  (`:focus-visible`). A ring follows the element's shape and sits
  outside its padding, so it never touches the content.
