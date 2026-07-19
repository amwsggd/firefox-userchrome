# ShyFox (Simplified)

A streamlined Firefox userChrome theme based on [ShyFox](https://github.com/Naezr/ShyFox), focused on **hiding the navbar** (address bar + navigation buttons) via keyboard shortcuts. All style customizations, sidebar/toolbar panel management, and color overrides have been stripped out — Firefox keeps its native appearance.

## What This Does

| Feature | Behavior |
|---------|----------|
| **Navbar Toggle** | Hide or show the URL bar + navigation buttons via shortcut |
| **Clean Mode** | Hide the navbar entirely except URL bar + extensions button |
| **Compact Menus** | Smaller context menus, grid layout for extensions panel |
| **Tab Progress** | Loading progress bar shown on the navbar when tabs load |

Everything else — sidebar, tabs, toolbar, window controls, colors — stays **exactly as Firefox's default**.

## How It Works

The theme toggles panel visibility by adding or removing **invisible Unicode characters** to the Firefox window title. CSS rules detect these characters via `[titlepreface*="..."]` selectors and apply show/hide styles accordingly.

| Character | Unicode | Titlepreface | Effect |
|-----------|---------|-------------|--------|
| `‌` | U+200C | Zero Width Non-Joiner | **Navbar hidden** — navbar slides up, shows indicator bar; hover or focus URL bar to reveal |
| `‍` | U+200D | Zero Width Joiner | **Clean mode** — navbar hidden with only URL bar + extensions visible |

When **no** special character is in the title, the navbar behaves like Firefox's normal navbar.

### Detail: Navbar Hidden (`‌`)

- The navbar slides up off-screen, leaving a thin **indicator bar** at the top of the window
- The indicator bar shows the theme color (or tab loading progress when a tab is loading)
- **Hover** the mouse near the top edge of the window to reveal the navbar
- **Focus** the URL bar (e.g. `Cmd+L` / `Ctrl+L`) to reveal the navbar
- The navbar appears as a centered floating panel with full functionality

### Detail: Clean Mode (`‍`)

- Same as Navbar Hidden, but **extra navbar items are hidden**
- Only the URL bar container and extensions button remain visible on reveal

---

## Installation

### 1. Enable userChrome.css

1. Open Firefox, navigate to `about:config`
2. Search for `toolkit.legacyUserProfileCustomizations.stylesheets`
3. Set it to **`true`**

### 2. Find Your Profile Directory

1. Navigate to `about:support` in Firefox
2. Find the **"Profile Directory"** row (or "Profile Folder")
3. Click **"Open Directory"** (macOS: "Show in Finder")

### 3. Install the Files

Copy the entire contents of this directory into the `chrome/` folder inside your profile:

```
<profile>/
└── chrome/
    ├── userChrome.css
    └── Shyfox/
        ├── shy-variables.css      ← CSS variables & settings
        ├── shy-global.css         ← Layout, z-index, fixes
        ├── shy-navbar.css         ← Navbar hide/show logic
        └── shy-compact.css        ← Compact menus & extension panel
```

**Restart Firefox** to apply.

### 4. Verify

After restarting, you should see the navbar at the top as usual. If it looks normal, the installation is successful. The navbar won't hide yet — you need keyboard shortcuts for that (see below).

---

## Keyboard Shortcuts

### Recommended: Userchrome Toggle Extended

The simplest way — no scripts, no config files.

1. Install **[Userchrome Toggle Extended](https://addons.mozilla.org/firefox/addon/userchrome-toggle-extended/)** from Firefox Add-ons
2. Go to `about:addons` → click the gear icon ⚙ → **"Manage Extension Shortcuts"**
3. Assign shortcuts for the ShyFox toggles:

| Toggle | Recommended Shortcut | Effect |
|--------|---------------------|--------|
| **Hide Navbar** | `Ctrl+Alt+S` | Toggle navbar hidden / shown (`‌`) |
| **Clean Mode** | `Ctrl+Alt+X` | Toggle clean mode (`‍`) |

> **Tip**: On macOS, `Ctrl+Alt` maps to `Control+Option`. You can use any key combo Firefox accepts.

Once set, press your shortcut to hide the navbar. Press again to show it. That's it.

> **Alternative methods**: If you prefer not to use an extension, see [`ALT_KEYBINDS.md`](ALT_KEYBINDS.md) for AutoConfig and external script approaches.

---

## Customization

Edit `Shyfox/shy-variables.css` to adjust:

```css
:root {
  --margin: 0.8rem;          /* Gap size around navbar when hidden */
  --trans-dur: 0.25s;        /* Animation speed */
  --navbar-wdt: 60vw;        /* Width of navbar when hidden/floating */
  --hide-bar-opct: 0.2;      /* Opacity of the indicator bar */
  --shy-accent-color: #3584E4; /* Accent color for progress bars */
}
```

### Disable Compact Menus

If you don't want the compact menu styles, remove this line from `userChrome.css`:

```css
@import url("ShyFox/shy-compact.css");
```

### Disable Compact Extensions Panel Only

Set `shyfox.disable.compact.unified.extensions` to `true` in `about:config`.

### Larger Context Menu

Set `shyfox.larger.context.menu` to `true` in `about:config`.

---

## Files Summary

```
chrome/
├── userChrome.css                 # Entry point — imports modules
└── Shyfox/
    ├── shy-variables.css          # Variables: sizes, colors, animation speed
    ├── shy-global.css             # Global layout, z-index, window drag, fixes
    ├── shy-navbar.css             # Navbar show/hide toggle & clean mode
    └── shy-compact.css            # Compact context menus, extension panel
```

Disabled (left as empty placeholders):

```
    ├── shy-sidebar.css            # Disabled — Firefox native sidebar
    ├── shy-toolbar.css            # Disabled — Firefox native tabs/toolbar
    └── shy-controls.css           # Disabled — Firefox native window controls
```

---

## Credits

This is a stripped-down version of [ShyFox](https://github.com/Naezr/ShyFox) by Naezr. The original ShyFox is a comprehensive theme that transforms Firefox's entire interface with auto-hiding panels, vertical tabs, and custom styling. This version preserves only the navbar auto-hide mechanism while removing all visual customizations, sidebar/toolbar panel management, and color overrides.

## License

Same as the original ShyFox project.
