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
