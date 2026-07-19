# Alternative Keyboard Shortcut Methods

If you prefer not to use the [Userchrome Toggle Extended](https://addons.mozilla.org/firefox/addon/userchrome-toggle-extended/) extension, here are two alternative ways to set up keyboard shortcuts for ShyFox.

---

## Method 1: AutoConfig + userChrome.js

This approach uses Firefox's AutoConfig mechanism. It's more involved to set up but works without any extensions.

### Step 1: Find Firefox's Installation Directory

- **macOS**: `/Applications/Firefox.app/Contents/Resources/`
- **Windows**: `C:\Program Files\Mozilla Firefox\`
- **Linux**: `/usr/lib/firefox/` or `/opt/firefox/`

### Step 2: Create `defaults/pref/autoconfig.js`

In the Firefox installation directory:

```
<firefox-install>/
└── defaults/
    └── pref/
        └── autoconfig.js
```

**`defaults/pref/autoconfig.js`**:
```js
pref("general.config.filename", "mozilla.cfg");
pref("general.config.obscure_value", 0);
```

### Step 3: Create `mozilla.cfg`

In the Firefox installation directory (same level as `defaults/`):

```js
// mozilla.cfg — must start with a comment line
Services.obs.addObserver(function () {
  loadTitlePrefacer();
}, "browser-delayed-startup-finished", false);

function loadTitlePrefacer() {
  let chromeDir = Services.dirsvc.get("UChrm", Ci.nsIFile);
  if (!chromeDir.exists()) chromeDir.create(Ci.nsIFile.DIRECTORY_TYPE, 0o755);
  let jsFile = chromeDir.clone();
  jsFile.append("userChrome.js");
  if (jsFile.exists()) {
    Services.scriptloader.loadSubScriptWithOptions(
      Services.io.newFileURI(jsFile).spec,
      { target: window, charset: "UTF-8", ignoreCache: true }
    );
  }
}
```

### Step 4: Create `chrome/userChrome.js`

In your Firefox profile's `chrome/` directory:

```js
// ShyFox — keyboard shortcuts for navbar control

const TITLE_PREFIX = {
  NAVBAR_HIDDEN: "‌",  // U+200C Zero Width Non-Joiner
  CLEAN_MODE:    "‍",  // U+200D Zero Width Joiner
};

function toggleTitlePrefix(char) {
  let current = document.documentElement.getAttribute("titlepreface") || "";
  if (current.includes(char)) {
    document.documentElement.setAttribute(
      "titlepreface",
      current.replace(new RegExp(char, "g"), "")
    );
  } else {
    document.documentElement.setAttribute("titlepreface", current + char);
  }
}

document.addEventListener("keydown", function (e) {
  const mod = e.metaKey || e.ctrlKey;
  if (mod && e.shiftKey && !e.altKey) {
    switch (e.key.toLowerCase()) {
      case "n":
        e.preventDefault();
        toggleTitlePrefix(TITLE_PREFIX.NAVBAR_HIDDEN);
        break;
      case "m":
        e.preventDefault();
        toggleTitlePrefix(TITLE_PREFIX.CLEAN_MODE);
        break;
    }
  }
});
```

Default shortcuts: `Ctrl+Shift+N` (toggle navbar), `Ctrl+Shift+M` (toggle clean mode). Edit the `switch` block to customize.

---

## Method 2: External Script

Use an external script that modifies Firefox's window title. Bind it to a hotkey using a system-level tool.

### macOS (AppleScript)

```bash
#!/bin/bash
# Toggle ShyFox navbar hidden/show
osascript -e '
tell application "Firefox"
  set w to front window
  set t to name of w
  if t contains "‌" then
    set name of w to my replaceText(t, "‌", "")
  else
    set name of w to t & "‌"
  end if
end tell

on replaceText(theText, search, replace)
  set AppleScript's text item delimiters to search
  set textItems to text items of theText
  set AppleScript's text item delimiters to replace
  set newText to textItems as text
  set AppleScript's text item delimiters to ""
  return newText
end replaceText'
```

Bind this script to a hotkey using **Hammerspoon**, **BetterTouchTool**, **Raycast**, or **macOS Shortcuts**.

### Linux (wmctrl)

```bash
#!/bin/bash
# Toggle ShyFox navbar hidden/show
TITLE=$(xdotool getactivewindow getwindowname)
if echo "$TITLE" | grep -q "‌"; then
  xdotool getactivewindow set_window --name "${TITLE//‌/}"
else
  xdotool getactivewindow set_window --name "${TITLE}‌"
fi
```

Bind to a hotkey via your window manager or desktop environment's shortcut settings.

### Windows (PowerShell + AutoHotkey)

With AutoHotkey, create a script that sends the titlepreface character. Or use the Userchrome Toggle Extended extension (recommended for Windows).
