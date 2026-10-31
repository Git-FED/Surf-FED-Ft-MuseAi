# Muse AI Sidebar — upgrade for Internet-Browser-With-Extensions (Surf FED)

Step 1 of the Surf Fed build: a toolbar button with a little robot icon.
Click it → a sidebar opens and Muse AI loads up. Whatever account you are
logged into, that chat appears on the side. As you move across tabs, Muse AI
sits right there on the side.

## Install

These 3 files are complete replacements for the same files in the repo root,
based on GitHub HEAD `ee6fee37` of `Git-FED/Internet-Browser-With-Extensions`:

1. Drop `index.html`, `renderer.js`, `styles.css` into the repo root,
   overwriting the existing files. (If your local copies differ from GitHub
   HEAD, merge instead — every addition is marked with a `MUSE SIDEBAR`
   comment so you can find it.)
2. `npm install` (first time only), then `npm start`.
3. Click the 🤖 button in the toolbar. Log into Muse once inside the
   sidebar — it stays logged in after that.

No changes to `main.js`, `preload.js`, or `package.json` are needed, and no
new image asset is needed (the button is an emoji, like the existing 🌙/🧩
buttons). No version bump was made — your call.

## What changed

- `index.html` — added the 🤖 toolbar button, wrapped `#webviewContainer`
  in a new `#mainRow` flex row, and added the `<aside id="museSidebar">`
  pane with its own `<webview>` (same `persist:surf-fed` partition as every
  tab, so the Muse login persists across restarts like any other site).
- `renderer.js` — added the sidebar toggle: first open loads
  `https://muse.ai` into the pane; the button highlights while open; the
  ✕ in the sidebar header closes it.
- `styles.css` — layout for `#mainRow` + `#museSidebar` (380px, max 45vw),
  plus dark-mode variants matching the existing extensions panel.

## Why a first-party pane, not a Chrome extension

Electron's extension loader does not implement `chrome.sidePanel`, so a
classic MV3 side-panel extension cannot open a sidebar in this browser. The
sidebar therefore lives in the browser UI itself — which is also how Surf Fed
does built-ins anyway (ad blocker, dark mode, etc. are all built in, not
downloaded). Same result: one button, one sidebar, Muse on the side.

## How the spec is met

- Button with a little robot icon → 🤖 in the toolbar.
- Click → sidebar opens, Muse AI loads → first open loads muse.ai.
- Whatever account you are logged into, that chat appears → the pane uses
  the shared `persist:surf-fed` session, so the login you already have (or
  do once) is the chat you see.
- Stays on the side as you move across tabs → the pane sits OUTSIDE
  `#webviewContainer`, and tab switching only ever touches tab webviews,
  so it never hides the sidebar.

## Not tested in a live window here

This VM has no display server, so the upgrade was syntax-checked and
reviewed against the repo's own patterns but not run in a live Electron
window. Verify with `npm start` and one click on 🤖. If the pane is blank
on first open, check the DevTools console for the webview error — the most
likely cause would be a partition/attribute typo, and the fix would be in
the `<webview>` tag in `index.html`.
