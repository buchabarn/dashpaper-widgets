# DashPaper Widgets

Community widgets for **DashPaper**.

DashPaper is a free macOS desktop widget manager. Widgets are built with plain
HTML, CSS, and JavaScript, rendered by WebKit, and can optionally use native
DashPaper APIs for notifications, clipboard access, storage, background tasks,
external-browser authentication, and window sizing.

This repository is where developers can submit widgets they have built or learn
how to install their own widgets locally.

> Not every productivity app fits everyone, but you can create productive
> widgets that fit you.

## Quick Start

A DashPaper widget is a folder containing a strict `widget.json` manifest and
the HTML file named by that manifest:

```text
my-widget/
  widget.json
  index.html
  styles.css
  script.js
  preview.png
  README.md
```

The smallest working widget only needs:

```text
my-widget/
  widget.json
  index.html
```

For a community submission, also include `preview.png` and `README.md`.

## Create Your First Widget

Create a lowercase folder:

```sh
mkdir my-widget
cd my-widget
touch widget.json index.html styles.css script.js README.md
```

Add this manifest to `widget.json`:

```json
{
  "name": "My Widget",
  "description": "A short explanation of what the widget does.",
  "icon": "M",
  "iconClass": "my-widget",
  "entry": "index.html",
  "publishedAt": "2026-06-07",
  "author": "Your Name",
  "version": "1.0.0",
  "category": "Productivity"
}
```

Add this page to `index.html`:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="styles.css">
  <title>My Widget</title>
</head>
<body>
  <main class="widget" data-dashpaper-widget>
    <p class="label">My Widget</p>
    <p id="value" class="value">Hello desktop</p>
    <button
      class="close"
      data-dashpaper-no-drag
      aria-label="Close widget"
      onclick="window.dashpaper?.close()"
    >
      &times;
    </button>
  </main>
  <script src="script.js"></script>
</body>
</html>
```

Add this styling to `styles.css`:

```css
@font-face {
  font-family: "DM Sans";
  src: local("DM Sans");
}

* {
  box-sizing: border-box;
}

html,
body {
  margin: 0;
  width: fit-content;
  min-width: fit-content;
  min-height: fit-content;
  overflow: hidden;
  background: transparent;
  color: rgba(255, 255, 255, 0.94);
  font-family: "DM Sans", -apple-system, BlinkMacSystemFont, sans-serif;
  user-select: none;
}

.widget {
  position: relative;
  width: 240px;
  min-height: 120px;
  padding: 14px;
  border: 1px solid rgba(255, 255, 255, 0.14);
  border-radius: 18px;
  background: rgba(15, 24, 32, 0.76);
  backdrop-filter: blur(24px) saturate(1.2);
}

.label {
  margin: 0;
  color: rgba(255, 255, 255, 0.58);
  font-size: 12px;
  font-weight: 600;
}

.value {
  margin: 10px 0 0;
  font-size: 24px;
  font-weight: 650;
  letter-spacing: -0.03em;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 27px;
  height: 27px;
  border: 0;
  border-radius: 999px;
  background: transparent;
  color: rgba(255, 255, 255, 0.5);
  font-size: 20px;
}

.close:hover {
  background: rgba(255, 255, 255, 0.08);
  color: white;
}
```

`script.js` can be empty for this example.

## Install a Widget Locally

DashPaper loads local widgets from:

```text
~/.dashpaper/widgets/
```

The easiest installation flow is:

1. Open DashPaper from the menu bar.
2. Open the marketplace.
3. Click **Add widget**.
4. Finder opens the local widgets directory.
5. Drag the complete widget folder into that directory.
6. Return to the marketplace and enable the widget.

The resulting path should be:

```text
~/.dashpaper/widgets/my-widget/widget.json
~/.dashpaper/widgets/my-widget/index.html
```

Do not copy only the HTML file. DashPaper ignores folders without a valid
`widget.json`.

DashPaper watches widget files while it is running. Save your HTML, CSS, or
JavaScript changes and the installed widget will reload automatically.

You can also install from Terminal:

```sh
mkdir -p ~/.dashpaper/widgets
cp -R my-widget ~/.dashpaper/widgets/
```

## Manifest Reference

DashPaper currently requires these fields:

| Field | Required | Description |
| --- | --- | --- |
| `name` | Yes | Human-readable marketplace name. |
| `description` | Yes | Short, useful marketplace summary. |
| `icon` | Yes | One or two characters displayed in the marketplace icon. |
| `iconClass` | Yes | Lowercase CSS-safe class used for icon styling. |
| `entry` | Yes | HTML entry file inside the widget folder. |
| `publishedAt` | No | ISO date in `YYYY-MM-DD` format. |
| `width` | No | Initial fallback width before WebKit measures the widget. |
| `height` | No | Initial fallback height before WebKit measures the widget. |
| `minWidth` | No | Minimum auto-sized width. |
| `minHeight` | No | Minimum auto-sized height. |
| `maxWidth` | No | Maximum auto-sized width. |
| `maxHeight` | No | Maximum auto-sized height. |
| `author` | No | Community attribution. |
| `version` | No | Semantic widget version. |
| `category` | No | Community category. |

Suggested categories:

```text
Productivity
Utility
Developer
System
Finance
Calendar
Weather
Personal
Fun
Experimental
```

DashPaper generates and tracks widget ids automatically. Developers do not
submit an id. A pull request cannot use a folder name already present in the
repository.

If a folder name collides, choose a more specific slug such as
`acme-github-pr-queue` instead of `github`.

### The `New` badge

The marketplace displays `New` for 30 days from `publishedAt`.

- Use a real release date in `YYYY-MM-DD` format.
- Missing or invalid dates do not receive the badge.
- Dates older than 30 days lose the badge automatically.
- Maintainers may update `publishedAt` to the merge or release date.

## Natural Widget Sizing

DashPaper measures rendered HTML after WebKit loads the page. Put
`data-dashpaper-widget` on the element that defines the widget's visual bounds:

```html
<main class="widget" data-dashpaper-widget>
  ...
</main>
```

Prefer natural content sizing:

```css
html,
body {
  width: fit-content;
  min-width: fit-content;
  min-height: fit-content;
}

.widget {
  width: 280px;
  min-height: 140px;
}
```

When content expands or collapses, ask the native host to remeasure it:

```js
await window.dashpaper?.resizeToContent({
  animated: true,
  persist: false
});
```

Use `persist: false` for temporary panels so closing the panel returns to the
widget's normal size.

## Dragging and Interactive Controls

DashPaper handles native dragging. Avoid implementing your own window dragging.

Buttons, links, inputs, textareas, selects, and content-editable elements do not
start a drag. Add `data-dashpaper-no-drag` to any other custom interactive
surface:

```html
<div class="slider" data-dashpaper-no-drag></div>
```

For reliable text input:

```js
input.addEventListener("pointerdown", () => {
  window.dashpaper?.focusInput();
  input.focus();
});
```

## Native APIs

DashPaper exposes native functionality on `window.dashpaper`.

### App and window

```js
dashpaper.platform; // "macos"

const info = await dashpaper.app.info();
// { name, version, build, bundleId, platform }

dashpaper.close();
dashpaper.focusInput();

await dashpaper.resize(320, 180, {
  animated: true,
  persist: true
});

await dashpaper.resizeToContent({
  animated: true,
  persist: false
});

await dashpaper.openURL("https://example.com");
```

### Notifications

```js
await dashpaper.notify(
  "Pomodoro complete",
  "Your focus session has finished."
);
```

### Clipboard

```js
const text = await dashpaper.clipboard.readText();
await dashpaper.clipboard.writeText("Copied from DashPaper");
```

Clipboard access should be initiated by an obvious user action. Never collect or
transmit clipboard content without clear disclosure.

### Persistent native storage

```js
await dashpaper.storage.set("settings", {
  city: "Nairobi",
  units: "metric"
});

const settings = await dashpaper.storage.get("settings");
await dashpaper.storage.remove("settings");
```

Native storage is scoped to the widget. Browser `localStorage` is also available
for ordinary widget state.

### Background tasks

```js
dashpaper.tasks.register(
  "refresh-data",
  {
    interval: "10m",
    runOnStartup: true
  },
  async () => {
    await refreshData();
  }
);

dashpaper.tasks.unregister("refresh-data");
dashpaper.tasks.list();
```

Intervals accept milliseconds or strings such as `"30s"`, `"10m"`, and `"1h"`.
Tasks are discarded when the widget reloads, is uninstalled, or DashPaper exits.

### External-browser authentication

DashPaper intentionally does not embed Google, Apple, GitHub, Microsoft, or
other SSO login pages. Open authentication in the user's default browser:

```js
const state = crypto.randomUUID();
const url = new URL("https://example.com/oauth/authorize");

url.searchParams.set("client_id", "your-client-id");
url.searchParams.set("response_type", "code");
url.searchParams.set("redirect_uri", dashpaper.auth.redirectURL);
url.searchParams.set("state", state);

const callback = new URL(
  await dashpaper.auth.start(url.href, { state })
);

const code = callback.searchParams.get("code");
```

Use Authorization Code with PKCE. Secure values can be stored per widget:

```js
await dashpaper.auth.setToken("access_token", token);
const token = await dashpaper.auth.getToken("access_token");
await dashpaper.auth.removeToken("access_token");
```

Do not include client secrets or private API keys in widget source code.

## Network Requests

Standard browser `fetch()` is available:

```js
async function loadWeather() {
  const response = await fetch(
    "https://api.open-meteo.com/v1/forecast?latitude=-1.2864&longitude=36.8172&current=temperature_2m"
  );

  if (!response.ok) {
    throw new Error(`Weather request failed: ${response.status}`);
  }

  return response.json();
}
```

Network widgets must provide loading, empty, cached, and error states. Do not
silently send personal data to third parties.

## Community Submission Layout

Each pull request should add **one widget folder at the root of this
repository**.

Good:

```text
dashpaper-widgets/
  clipboard-stack/
  daily-quote/
  pomodoro/
  README.md
```

Avoid:

```text
dashpaper-widgets/
  widgets/
    clipboard-stack/
```

Use a clean lowercase folder name:

```text
clipboard-stack
daily-quote
github-pr-queue
stripe-revenue-card
```

Avoid spaces, punctuation, or temporary names:

```text
Clipboard Stack
my widget!!!
new-widget-final-v2
```

## Required Submission Files

A community submission should contain:

```text
clipboard-stack/
  widget.json
  index.html
  styles.css
  script.js
  preview.png
  README.md
```

Additional local assets are allowed:

```text
clipboard-stack/
  assets/
    icon.svg
```

Do not load basic UI assets from a CDN when they can be included in the widget
folder. Widgets should remain usable when those cosmetic services are offline.

## Widget README

Every submitted widget should explain its purpose and setup:

```md
# Clipboard Stack

A small clipboard memory widget for DashPaper.

## What it does

Clipboard Stack lets you save, search, and reuse copied text.

## Setup

No setup required.

## Permissions and network access

Reads the clipboard only when the user clicks "Read Clipboard".
Does not send clipboard content over the network.

## Author

Built by @yourhandle.
```

Disclose:

- Network domains contacted by the widget
- Clipboard usage
- Notification usage
- Authentication requirements
- Data stored locally
- Any required user configuration

## Submit a Widget

1. Fork this repository.
2. Create a new lowercase widget folder at the repository root.
3. Add the widget files and strict `widget.json`.
4. Add a representative `preview.png`.
5. Install and test the complete folder in DashPaper.
6. Confirm the widget works after restarting DashPaper.
7. Open a pull request.

Pull request title:

```text
Add <Widget Name>
```

Example:

```text
Add Clipboard Stack
```

## Pull Request Rules

Each pull request must:

- Add one widget folder only
- Put the folder at the repository root
- Use a unique lowercase folder name
- Include a valid `widget.json`
- Include the entry file named by `widget.json`
- Include `preview.png`
- Include a widget `README.md`
- Work as a self-contained widget
- Avoid secrets, private tokens, and client secrets
- Avoid minified, obfuscated, generated, or malicious JavaScript
- Disclose network, clipboard, authentication, and data-storage behavior
- Be useful as a desktop widget

Maintainers may reject widgets that are broken, duplicative without meaningful
improvement, unsafe, excessively resource-intensive, or inconsistent with the
repository guidelines.

## Design Guidelines

DashPaper widgets should be:

- Small
- Useful
- Calm
- Legible
- Easy to inspect
- Easy to customize
- Focused on one clear job

Additional guidance:

- Use transparent `html` and `body` backgrounds.
- Keep padding compact and intentional.
- Use consistent nested radii: inner radius equals outer radius minus padding.
- Avoid large external shadows; transparent native windows expose shadow halos.
- Keep controls large enough to click without dominating the widget.
- Use semantic HTML and accessible labels.
- Provide visible focus states for keyboard users.
- Avoid fixed page-sized canvases and unnecessary continuous animation.

## Security

Do not submit widgets that:

- Steal or silently transmit user data
- Track users without clear disclosure
- Exfiltrate clipboard contents
- Mine cryptocurrency
- Run hidden or unrelated network requests
- Mimic macOS security or authentication dialogs
- Attempt to bypass DashPaper protections
- Include malicious or obfuscated code
- Bundle secrets, private keys, or private tokens

Widgets must be transparent and reasonably easy to audit.

## Testing Checklist

Before submitting:

- The complete folder appears in the DashPaper marketplace.
- Enabling and disabling the widget preserves its expected state.
- Inputs, buttons, scrolling, and keyboard focus work.
- The widget can be dragged without custom drag code.
- Expanded UI returns to its original size when closed.
- Content is not clipped at minimum or expanded sizes.
- The widget remains within normal CPU and memory usage while idle.
- Network failure does not crash or permanently break the widget.
- No secrets are present in source or commit history.
- The preview accurately represents the current widget.
- The widget README documents setup and sensitive capabilities.

## Ideas

Small, focused widgets are usually the best:

- Clipboard Stack
- Quick Note
- Pomodoro
- Countdown
- Daily Quote
- Weather
- World Clock
- System Monitor
- Battery Monitor
- Network Speed
- GitHub Pull Request Queue
- Stripe Revenue Card
- Website Uptime Monitor
- JSON or API Card
- RSS Feed
- Linear Issues
- Product Launch Countdown
- Exchange Rate Widget

## License

This repository does not currently include a top-level license. Before accepting
public contributions, maintainers should add one.

Until then, widget authors should include a license in their widget folder and
state it in the widget README. By opening a pull request, contributors must have
the right to submit and distribute all included code and assets.

## More Documentation

The extended development guide is available at
[`docs/widget_development.md`](docs/widget_development.md).
