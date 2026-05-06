# PlayerRenamer

![License](https://img.shields.io/github/license/jack-builds/PlayerRenamer-Gimkit)
![JavaScript](https://img.shields.io/badge/language-JavaScript-yellow)
![Gimloader](https://img.shields.io/badge/Gimloader-plugin-blue)
![Version](https://img.shields.io/badge/version-3.0.0-informational)
![Stars](https://img.shields.io/github/stars/jack-builds/PlayerRenamer-Gimkit?style=social)

> A Gimloader plugin for Gimkit that automatically assigns fun custom names to players on your screen — **host only, client-side only**.

---

## Purpose

PlayerRenamer is a lightweight plugin for [Gimkit](https://gimkit.com) that works through the [Gimloader](https://gimloader.github.io/) browser extension. When you host a game, it automatically replaces every player's displayed name with a custom name from your personal name pool — things like `"sigma"`, `"Rizzler"`, or `"ohio skibidi rizz"`. Names are assigned in order as players join and cycle back around if there are more players than names. You can fully customise the name list through a built-in settings menu.

> **Important:** Name changes are visible **only to you as the host**. Other players see their original names and are completely unaware of any renaming.

---

## How it Works

Here is what happens inside `main.js` step by step:

1. **Plugin loads** — When the Gimkit game network finishes loading (`api.net.onLoad`), the plugin checks whether you are the host. If you are not the host, it exits immediately and does nothing.

2. **Name pool is loaded** — The plugin reads your custom name list from Gimloader's storage (`api.storage.getValue`). If you have never configured it, it falls back to the built-in default names pool.

3. **Players are scanned** — The plugin accesses Gimloader's Phaser scene via `GL.stores.phaser.scene.characterManager.characters` to find all currently visible player characters in the game.

4. **Names are patched** — For each player character found, the plugin:
   - Assigns them the next name from the pool (cycling if needed), stored by their unique `sessionId`
   - Immediately calls `nametag.setName(customName)` and updates the tag text
   - Uses `api.patcher.instead` to intercept all future `setName` calls on that character's nametag, so Gimkit can never overwrite the custom name

5. **Late joiners are handled** — The plugin listens for new players joining mid-game using `state.players.onAdd`. When a new player joins, it re-runs the patch pass at 300ms, 800ms, and 1500ms delays to catch the character after it fully renders.

6. **Safety net** — A `setInterval` re-runs the patch every 3 seconds to catch any characters that may have been missed or re-rendered.

7. **Cleanup on stop** — When the plugin is stopped (`api.onStop`), the interval is cleared, all patches are removed, and the name assignments are reset.

---

## Getting Started

### Prerequisites

- A [Gimkit](https://gimkit.com) account
- A Chromium-based browser (Chrome, Edge, Brave, Opera) **or** Firefox
- The [Gimloader](https://gimloader.github.io/) browser extension

### Step 1 — Install Gimloader

1. Install the extension for your browser:
   - **Chrome / Edge / Brave / Opera:** [Chrome Web Store](https://chromewebstore.google.com/detail/gimloader/hmgbokdikhlcmopciindnbfbcjoocggj)
   - **Firefox:** [Mozilla Add-ons](https://addons.mozilla.org/en-US/firefox/addon/gimloader/)
2. Go to [gimkit.com/join](https://gimkit.com/join) — you should see a **wrench icon** appear next to the join button. This confirms Gimloader is active.

### Step 2 — Install PlayerRenamer

1. Open Gimloader by pressing **Alt + P** on any Gimkit page, or by clicking the wrench icon in your browser toolbar.
2. Go to the **Plugins** tab.
3. Click the **Create/Install** dropdown → select **Install from URL**.
4. Paste the following URL:
   ```
   https://raw.githubusercontent.com/jack-builds/PlayerRenamer-Gimkit/main/main.js
   ```
5. Click **Install**. PlayerRenamer will now appear in your plugin list.

> **Alternative:** Download `main.js` from this repo and drag and drop it directly onto the Plugins tab of the Gimloader menu.

### Step 3 — Configure Your Name Pool (Optional)

PlayerRenamer comes with a default set of names out of the box:

```
NPC_1, sigma, 67 king 67, Hugh, Rizzler, sigmatwizzler, ohio skibidi rizz, 67
```

To customise the list:

1. Open Gimloader (**Alt + P**) on any Gimkit page.
2. Find **PlayerRenamer** in your plugin list and click the **Settings** (gear) icon.
3. You will see a text area with one name per line — edit it however you like.
4. Click **Save** to apply your changes. Assignments reset automatically so the new names take effect immediately.
5. Click **Reset to defaults** at any time to restore the original name pool.

> Names are assigned in order as players join and cycle back to the start if there are more players than names.

### Step 4 — Use PlayerRenamer in a Game

1. **Host** a Gimkit game — PlayerRenamer only activates when you are the host.
2. Make sure the **PlayerRenamer** plugin is enabled in Gimloader before you start.
3. As players join your game, their names will automatically change on your screen.
4. You do not need to do anything else — renaming is fully automatic.

---

## Troubleshooting

**The wrench icon does not appear on Gimkit**
- Go to `chrome://extensions` (or `about:addons` on Firefox) and confirm Gimloader is enabled.
- Refresh the Gimkit page after enabling the extension.

**PlayerRenamer does not appear in the plugin list**
- Make sure you used the correct raw URL for `main.js` starting with `https://raw.githubusercontent.com/...`
- Try the drag-and-drop install method as an alternative.

**Names are not changing in-game**
- Confirm you are the **host** of the game — the plugin only runs for the host.
- Make sure the plugin is toggled **on** in the Plugins tab before joining the game.
- The plugin patches names on a 3-second cycle. Wait a few seconds after players join for names to update.

**A player's name reverted back to their original name**
- This can happen if Gimkit re-renders the character (e.g. after a round transition). The plugin re-patches every 3 seconds and will correct it automatically.

**Settings are not saving**
- Click the **Save** button inside the Settings menu and look for the green `✓ Saved X names!` confirmation message.
- If the issue persists, click **Reset to defaults** and try again.

**Alt + P does not open Gimloader**
- This hotkey only works on Gimkit pages. Make sure you are on `gimkit.com`.
- You can also click the wrench icon in your browser toolbar to open Gimloader.

**Plugin errors in the browser console**
- Open DevTools (F12) and look for messages starting with `[PlayerRenamer]`. These warning messages are built into the plugin to help diagnose issues.
- Ensure your Gimloader extension is up to date.
- If the issue persists, please [open an issue](https://github.com/jack-builds/PlayerRenamer-Gimkit/issues) with the full error message.

---

## License

This project is licensed under the [GPL-3.0 License](LICENSE).

---
Thank you for using PlayerRenamer!!
