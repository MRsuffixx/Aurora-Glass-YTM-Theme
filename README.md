# Aurora Glass — a YouTube Music custom theme

**Languages:** English (this file) · [Türkçe](README.tr.md)

A custom YouTube Music theme written in SCSS: glass surfaces (glassmorphism), layered gradients and a coherent motion system. It is built on the supplied default SCSS file; every functional fix from that file is kept, and only the look has been redesigned.

> This theme is not affiliated with or endorsed by YouTube, Google or YouTube Music. It only changes how the interface looks and does not touch application behavior.

---

## Table of contents

- [Features](#features)
- [Requirements](#requirements)
- [Building](#building)
- [Installing](#installing)
- [Configuration](#configuration)
- [File structure](#file-structure)
- [Selector status: verified vs. assumed](#selector-status-verified-vs-assumed)
- [Performance and accessibility](#performance-and-accessibility)
- [Troubleshooting](#troubleshooting)
- [What was kept and fixed from the default file](#what-was-kept-and-fixed-from-the-default-file)
- [Known limitations](#known-limitations)

---

## Features

**Look**

- Dark, slightly violet-tinted base; primary accent `#9b8cff`, secondary accent `#ff7ab8`.
- A very slowly drifting aurora layer built from gradients behind the whole app.
- Real `backdrop-filter` glass on the top bar and the bottom player; menus, dialogs, tooltips and search suggestions use the same glass.
- Cards, chips and the sidebar get a glass look from translucent fills, thin borders and a top-edge highlight (no blur there, because nothing scrolls underneath them).
- Gradient-filled progress and volume bars, a glowing knob, and a glass-disc play button.
- Gradient outline or fill on the selected sidebar item, the current queue row and selected chips.
- A thin dark-theme scrollbar, a themed text selection color and a visible keyboard focus ring.

**Motion**

- The only continuous animation is the aurora drift (80 s, `transform` only).
- Entrances: home-page shelves rise in one after another, and menu lists ease open.
- Hover: cover zoom, a gradient overlay rising from the bottom, a glow on the play button, a slight slide on sidebar and queue items, and a focus ring plus glow on the search field.
- Press feedback is done with `scale`.
- All transitions share one easing curve and three central durations; there is no `transition: all` anywhere.

---

## Requirements

- **Dart Sass 1.50 or newer** (the `sass` command). The theme needs no `@use` / `@import` of its own.
- A YouTube Music client or browser style extension that can load custom CSS.
- A Chromium-based engine is recommended. The theme uses `:is()`, `:has()`, `inset`, and the individual `scale` / `translate` properties.

Installing Sass:

```bash
npm install -g sass
sass --version
```

---

## Building

```bash
# Readable output
sass --no-source-map youtube-music-theme.scss youtube-music-theme.css

# Minified output (about 24 KB)
sass --no-source-map --style=compressed youtube-music-theme.scss youtube-music-theme.min.css

# While developing: recompile on every change
sass --watch --no-source-map youtube-music-theme.scss:youtube-music-theme.css
```

---

## Installing

Add the compiled `.css` file to the custom CSS area of whatever you use:

- **Desktop client with custom CSS support:** add the compiled file in the client's custom CSS / theme settings. The menu name differs from client to client.
- **Browser:** in a user-style extension such as Stylus, create a new style, paste the CSS and enable it for `music.youtube.com`.

Reload the page after adding it.

---

## Configuration

### 1. Compile-time switches

The three switches at the top of the file are declared with `!default`, so you can change them from outside without editing the file:

| Switch | Default | What it does |
|---|---|---|
| `$enable-glass-blur` | `true` | When `false`, no `backdrop-filter` is emitted anywhere and surfaces fall back to opaque colors. |
| `$enable-ambient` | `true` | When `false`, the aurora background layer is not generated. |
| `$enable-ambient-motion` | `true` | When `false`, the aurora stays still and its drift animation is not generated. |

To change them with a wrapper file (`custom.scss`):

```scss
@use 'youtube-music-theme' with (
  $enable-ambient-motion: false,
  $enable-glass-blur: true
);
```

```bash
sass --no-source-map custom.scss custom.css
```

### 2. Colors, radii and durations

These are not `!default`; edit them directly in `youtube-music-theme.scss`, in section 2 ("Design tokens"):

| Variable | Meaning |
|---|---|
| `$bg-0` … `$bg-3` | Background layers (darkest to lightest) |
| `$text-primary`, `$text-secondary`, `$text-tertiary` | Text colors |
| `$accent-1`, `$accent-2`, `$accent-3` | Primary accent, secondary accent and aurora light accent |
| `$radius-xs` … `$radius-pill` | Corner radius scale |
| `$dur-fast`, `$dur-base`, `$dur-slow` | Transition durations |
| `$ease-out`, `$ease-in-out` | Easing curves |
| `$logo-hue` | Logo color (the `hue-rotate` value that turns the red logo violet) |
| `$playerIconSize`, `$playerButtonSize` | Player icon and button size (from the default file) |

If you change an accent color, the `$grad-accent` and `$grad-accent-soft` gradients update automatically.

### 3. Runtime variables

These are CSS custom properties defined on `ytmusic-app`. You can change them without recompiling, from DevTools, a script, or an extra CSS file:

| Variable | Default | Use |
|---|---|---|
| `--ytmx-glow` | accent color, 42% | Glow color around the cover art, the play button and the player |
| `--ytmx-glow-2` | secondary accent, 30% | Secondary glow |
| `--ytmx-dur-fast` / `-base` / `-slow` | 160 / 260 / 420 ms | All theme transitions |
| `--ytmx-zoom` | `1.06` | Hover zoom of card covers (set it to `1` to turn the zoom off) |
| `--ytmx-blur-sm` / `-md` / `-lg` | 10 / 18 / 28 px | Blur radii |

**Colors from the album cover:** CSS cannot read the pixels of an image, so `--ytmx-glow` is only a hook. If you write a small script that extracts a color from the cover and sets this variable, the glows will follow the current song; the theme needs nothing else for that.

Example (to try in the browser console):

```js
document.querySelector('ytmusic-app')
  .style.setProperty('--ytmx-glow', 'rgba(255, 140, 60, 0.45)');
```

---

## File structure

The file is divided into numbered sections:

| Section | Contents |
|---|---|
| 1 | Switches |
| 2 | Design tokens (color, gradient, shadow, radius, spacing, blur, motion, typography) |
| 3 | Functions and mixins (`motion`, `glass`, `gradient-outline`, `focus-ring`) |
| 4 | Root layer: runtime variables, overrides of native YTM/Polymer variables, aurora |
| 5 | Keyframes |
| 6 | Global: canvas, selection color, scrollbar, focus |
| 7 | Header: top bar, logo, search, avatar |
| 8 | Sidebar |
| 9 | Home page: shelves, headings, chips, carousel arrows |
| 10 | Cards |
| 11 | Song lists |
| 12 | Bottom player |
| 13 | Player page and queue |
| 14 | Menus, dialogs, tooltips, modal backdrop |
| 15 | Buttons, icons, form controls |
| 16 | Responsive |
| 17 | Reduced motion (`prefers-reduced-motion`) |

Comments in the code mark whether each rule is verified or assumed (see below).

---

## Selector status: verified vs. assumed

Three tags are used in the comments:

- **`[V]` verified:** the selector or variable exists in the default SCSS file this theme is based on.
- **`[A]` assumed:** based on the known YouTube Music / Polymer structure, but not present in the default file. The YouTube Music interface may have changed since.
- **`[D]` design:** a theme token or hook with no DOM dependency.

**Main verified (`[V]`) selectors:** `#progress-bar.ytmusic-player-bar`, `#sliderContainer`, `ytmusic-player-bar`, `.image.ytmusic-player-bar`, `.play-pause-button.ytmusic-player-bar`, `.spinner-container.ytmusic-player-bar`, `#right-controls`, `.middle-controls-buttons`, `ytmusic-player-queue`, `ytmusic-player-queue-item`, `ytmusic-player-page`, `ytmusic-tab-renderer.ytmusic-player-page`, `ytmusic-browse-response`, `ytmusic-two-row-item-renderer`, `.thumbnail-overlay`, `.ytmusic-play-button-renderer`, `.logo.ytmusic-nav-bar`, `#player-bar-background.ytmusic-app-layout`, `tp-yt-paper-dialog`, `tp-yt-paper-listbox.ytmusic-menu-popup-renderer`.

**Assumed (`[A]`), with the least certain ones listed:**

- `#nav-bar-background.ytmusic-app-layout` (top bar glass)
- Transparency of `ytmusic-app-layout` and `#content.ytmusic-app-layout` (the aurora being visible depends on it)
- Sidebar: `tp-yt-app-drawer#guide`, `ytmusic-guide-entry-renderer` and its `[active]` state
- Search: `ytmusic-search-box`, `#input-box`, the suggestion components
- Chips: `ytmusic-chip-cloud-chip-renderer` and `[is-selected]`
- Song rows: `ytmusic-responsive-list-item-renderer` and the `[play-button-state]` attribute for the playing state
- `ytmusic-thumbnail-renderer[thumbnail-crop=…]` (keeps artist cards circular)
- Slider internals: `#primaryProgress`, `.slider-knob-inner`
- The `--ytmusic-*`, `--yt-spec-*` and `--paper-*` variable names
- The `.yt-spec-button-shape-next*` button classes

---

## Performance and accessibility

- `backdrop-filter` is used only on surfaces with scrolling content underneath: the top bar, the bottom player, menus, dialogs, tooltips and search suggestions. The modal backdrop is blurred only while a modal is open.
- When it is unsupported, or when `$enable-glass-blur: false`, all those surfaces fall back to opaque colors.
- Below a 1100 px window width the blur radii shrink.
- The aurora is made of gradients only (no blur filter) and moves with `transform`, so its render cost is low. If you still do not want any continuous animation, set `$enable-ambient-motion: false`.
- With `prefers-reduced-motion: reduce`, transition durations are zeroed and the aurora drift and entrance animations are turned off. The app's own animations are left alone (many depend on the `transitionend` event).
- Text contrast: secondary text is about 6.5:1 and tertiary text about 4.6:1 (on `$bg-1`).
- Keyboard focus stays visible; no interactive area is hidden.
- On touch devices (`hover: none`) the cover zoom is turned off.

---

## Troubleshooting

| Problem | Likely cause and fix |
|---|---|
| Nothing changed | The CSS is not loaded or is cached. Hard-reload the page and check that the CSS is enabled for `music.youtube.com` / your app. |
| Dark colors applied but no aurora | The `ytmusic-app-layout` background may not have become transparent. Check the layout element's `background` in DevTools; you can also disable the aurora with `$enable-ambient: false`. |
| Top bar glass is missing | The `#nav-bar-background` selector is an `[A]` assumption. Find the real element in DevTools and update the selector. |
| Active sidebar item is not highlighted | `[active]` and `.iron-selected` are tried for the active state; if your app uses a different attribute, adapt the selector in section 8. |
| Playing song row is not highlighted | The `[play-button-state]` attribute names are an assumption; edit section 11 to match the real attribute. |
| The font did not change | The theme only uses fonts installed on your computer (`Inter` if present, otherwise `Roboto`). Remote fonts are not loaded. |
| Player panel looks wrong in a narrow window | The below-968 px rule from the default file (section 13) is kept as-is; adjust the `#main-panel` padding there. |
| Blur hurts performance | Compile with `$enable-glass-blur: false`, or reduce the `--ytmx-blur-*` values. |

---

## What was kept and fixed from the default file

**Kept:** the rule that stretches the progress bar to full width, the `ytmusic-browse-response` visibility fix, the queue scrollbar-hiding trick, the player icon and button sizes, the spinner size, and the below-968 px player panel layout.

**Fixed:**

- The lines `--ytmusic-player-bar-background: $playerBarBackground` and `--yt-icon-button-icon-height: $playerIconSize - 4px` were printed to the output as raw text and had no effect, because Dart Sass does not evaluate `$variables` inside custom properties. They are now written with `#{}` (the output contains `#0b0914` and `16px`).
- The overlay of artist (circular) cards stayed square; it is now circular via `:has()`.
- The commented-out personal background image rule was removed.
- The generic card selector (`ytmusic-two-row-item-renderer`) was widened to cover cards in grids such as Explore / Library, not only those in carousels.

---

## Known limitations

- Carousel containers clip overflowing content, so cards do not lift on hover; cover zoom, the overlay and the play button effect are used instead.
- On album / playlist header areas only the cover art and its shadow are styled; the whole header is not redesigned.
- The sidebar and cards use translucent fills instead of blur, so their glass effect is softer than that of the top bar and player; this is a deliberate performance choice.
- The theme only changes appearance. Features that need behavior, such as extracting colors from the album cover, need additional JavaScript.
