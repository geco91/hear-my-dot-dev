![Hear My Dot — Give ChatGPT audio room](assets/readme-header.png)

# Hear My Dot

**A little room to listen.**

When ChatGPT speaks, Hear My Dot gives it room: it pauses supported YouTube and Podverse media and mutes other browser tabs. After ChatGPT audio stops, it waits through a short quiet period before restoring its own changes.

**Your playback stays yours.** Media that was already paused stays paused, tabs that were already muted stay muted, and manual changes take priority. ChatGPT tabs are always exempt.

## Install in Chrome

You need Chrome 120 or later. No build step, Node.js, or package installation is needed to use the extension.

1. On this repository's GitHub page, choose **Code → Download ZIP**, then extract the ZIP to a folder you will keep. If you already cloned the repository, use that checkout instead.
2. In the Chrome profile where you use ChatGPT and your media tabs, open `chrome://extensions` and turn on **Developer mode**.
3. Click **Load unpacked** and select the **`extension/` folder inside the extracted repository**. This is the folder containing `manifest.json`; do not select the repository root or `diagnostics/headset-probe/`.
4. Refresh any YouTube and Podverse tabs that were already open so their media controls can load.
5. Pin **Hear My Dot** from Chrome's extensions menu. Click its icon to open settings.

Keep the extracted folder in place: Chrome loads the extension from it. This is a local unpacked prototype, not a Chrome Web Store installation. It controls tabs in the same Chrome profile; incognito mode is not supported.

## Make room for ChatGPT audio

In settings, turn on **Prioritize ChatGPT audio**, choose a **Quiet period**, and click **Save settings**. Audio priority is on by default, with a 10-second quiet period. You can choose 10–20 seconds; saved values are kept when updating.

When an unmuted `chatgpt.com` tab produces audio:

- Supported **YouTube videos and Podverse audio/video pause**.
- **Other tabs are muted**. Muting is also the fallback when a supported page's media script is unavailable or pausing fails. Podverse also uses this fallback when no accessible player is found.
- After ChatGPT audio stops, the **quiet period** gives short pauses room before playback returns. New ChatGPT audio restarts the wait.
- Hear My Dot **resumes only media it paused** and **undoes only its own mute changes**.

The trigger is Chrome's audio activity indicator, not speech recognition. Any audio from ChatGPT can trigger focus; an open but silent call or microphone permission alone does not. Chrome's audio indicator can linger, and browser or device sleep can delay restoration, so the quiet period is a timing target.

## See what is happening

The toolbar icon opens settings; it does not toggle audio priority directly. Its shape, badge, and tooltip show the current state.

| Badge | State | Meaning |
| --- | --- | --- |
| None | Ready | Waiting for ChatGPT audio. |
| `ON` | Active | ChatGPT audio has triggered audio priority. |
| `WAIT` | Waiting | The quiet period is running, or the extension is retrying its own mute restoration. |
| `OFF` | Disabled | Audio priority is switched off in settings. |

`WAIT` can remain visible while restoration is pending, even after you turn off audio priority. The tooltip explains the state; the icon does not guarantee audible speaker output.

## Your playback stays yours

Automatic resume is deliberately cautious. It applies only to the same still-paused media element, source, and page that Hear My Dot paused. Playback, seeking, volume or speed changes, navigation, and player replacement can cancel automatic resume. A direct click, touch, or keypress on a supported media page also cancels its automatic pause/resume control for the current focus period.

If media stays paused after an interaction, resume it yourself. Chrome may also block automatic playback; Hear My Dot makes one resume attempt and does not retry it. Reloading the media page or extension clears ownership of paused media, so it will not automatically resume those old pauses.

Pause/resume supports top-level pages on `youtube.com`, `www.youtube.com`, `m.youtube.com`, and `podverse.fm` over HTTPS. YouTube Music and YouTube embeds on other sites use tab muting instead. Embedded players, inaccessible players, and site changes can limit media control. Manual overrides do not trigger fallback muting of the playback you chose.

## Update

The unpacked extension does not update automatically.

1. Open Hear My Dot settings, turn off **Prioritize ChatGPT audio**, click **Save settings**, and wait for its changes to be restored.
2. Update the files:
   - **Git checkout:** in your repository folder, run `git pull --ff-only` on the branch that tracks your intended upstream.
   - **ZIP download:** download and extract a fresh ZIP. Replace the contents of the `extension/` folder you previously loaded in Chrome with the contents of the new ZIP’s `extension/` folder. Copy into that existing `extension/` folder, not the repository root.
3. Open `chrome://extensions` and click **Reload** on Hear My Dot. Review any permission prompt Chrome shows.
4. Refresh your existing YouTube and Podverse tabs, then re-enable audio priority in settings and save.

Before removing the extension or disabling it in Chrome, follow step 1 as well. Once removed or disabled in Chrome, it cannot finish restoring its changes.

## Privacy and permissions

Hear My Dot does not record audio or use your microphone. Settings are stored locally, and it does not persist browsing history or tab/media URLs. Media URLs are held in memory only while the extension owns a pause. The extension makes no network requests of its own.

It uses `storage` for settings and focus state, `alarms` for restoration, and access to ChatGPT, YouTube, and Podverse hosts to detect eligible tabs and run the supported media controls. It does not request the broad `tabs` permission. There is no native helper or control of other Chrome profiles.

## Development and test status

This is an independently written Manifest V3 prototype, currently version **0.3.1**. The extension is already in use, and the repository includes automated tests. A complete, reproducible browser end-to-end acceptance test has not been established here. YouTube ads, live streams, Shorts, the deployed Podverse player, manual-control races, and sleep/wake behavior still need real-browser testing.

The loadable extension lives in [`extension/`](extension/); automated tests live in [`tests/`](tests/). Developers with Node.js can run them from the repository root without installing packages:

```sh
node --test tests/*.test.mjs
```

An optional, separate [headset command diagnostic](diagnostics/headset-probe/README.txt) is available for inspecting shortcut bindings and received commands. It is not required for Hear My Dot and does not add a background-toggle mode to the main extension.

## Origin and license

Original, independently written code under the [MIT License](LICENSE). Functional inspiration comes from [FrancisGregori/audio-tab-finder](https://github.com/FrancisGregori/audio-tab-finder). This is not a fork; no code or assets were copied from that project. No Podverse implementation code or assets are bundled. The header and toolbar icons are original artwork.
