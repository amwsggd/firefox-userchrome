# ShyFox (Simplified)

A streamlined Firefox userChrome theme based on [ShyFox](https://github.com/Naezr/ShyFox), focused on **manually hiding or showing the navbar** (address bar + navigation buttons) via keyboard shortcuts. The shown state keeps Firefox's native appearance. The toggle has no animation or hover behavior, which avoids relying on unstable address-bar internals.

## What This Does

| Feature | Behavior |
|---------|----------|
| **Navbar Toggle** | Hide or show the URL bar + navigation buttons via shortcut |
| **Clean Mode** | Compatibility alias that also hides or shows the navbar |
| **Compact Menus** | Smaller context menus, grid layout for extensions panel |

The navbar's shown state uses Firefox's native styling. Compact menu rules remain a separate, optional module.

## How It Works

The theme toggles panel visibility by adding or removing **invisible Unicode characters** to the Firefox window title. CSS rules detect these characters via `[titlepreface*="..."]` selectors and apply show/hide styles accordingly.

| Character | Unicode | Titlepreface | Effect |
|-----------|---------|-------------|--------|
| `‌` | U+200C | Zero Width Non-Joiner | **Navbar hidden** — use the same shortcut again to show it |
| `‍` | U+200D | Zero Width Joiner | **Compatibility mode** — also hides the navbar |

When **no** special character is in the title, the navbar behaves like Firefox's normal navbar.

### Detail: Navbar Hidden (`‌`)

- The navbar is moved off-screen immediately, without an animation
- There is no hover-to-reveal behavior or indicator bar
- Press the configured Userchrome Toggle Extended shortcut again to show it
- `Cmd+L` / `Ctrl+L` does not force the navbar to appear while the toggle is hidden
- The URL bar retains a layout box internally for compatibility with current Firefox

### Detail: Clean Mode (`‍`)

The historical Clean Mode marker is retained so existing extension settings keep working. In this simplified build it has the same behavior as Hide Navbar.

## Compatibility Strategy

- The critical navbar rules use flat selectors and avoid CSS nesting, `:is()`, and `:has()` so older Firefox parsers can read them.
- The toggle depends only on the extension's established U+200C/U+200D `titlepreface` markers and the long-lived `#nav-bar`, `#urlbar-container`, and `#urlbar` elements.
- Popup detection no longer uses `chromehidden`; only real DOM fullscreen hides the entire toolbox.
- Unknown future DOM changes fail open: if Firefox renames a target, the navbar stays visible instead of leaving the browser without an address bar.
- Compact menu styling is isolated in its own module and cannot disable the navbar toggle if those optional selectors change.

Firefox's chrome DOM is not a stable public API, so no `userChrome.css` theme can guarantee compatibility with every future release. The rules above minimize that risk and keep failures recoverable.

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

After restarting, run this short smoke test:

1. Confirm the navbar is visible before using a toggle.
2. Press the configured shortcut and confirm the navbar is hidden.
3. Press it again and confirm the navbar returns and accepts typing.
4. Open a popup window and confirm its native controls are unchanged.
5. Enter and exit a website's fullscreen mode and confirm the normal navbar returns afterward.

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

The manual navbar toggle intentionally has no animation, hover target, indicator, width option, or color override. This keeps it independent of Firefox's changing address-bar animation internals.

### Disable Compact Menus

If you don't want the compact menu styles, remove this line from `userChrome.css`:

```css
@import url("Shyfox/shy-compact.css");
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
    ├── shy-variables.css          # Minimal shared layout variables
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

This is a stripped-down version of [ShyFox](https://github.com/Naezr/ShyFox) by Naezr. The original ShyFox is a comprehensive theme that transforms Firefox's entire interface with auto-hiding panels, vertical tabs, and custom styling. This version preserves a manual navbar toggle while removing its hover/animation behavior, sidebar/toolbar panel management, and color overrides.

## License

Same as the original ShyFox project.
