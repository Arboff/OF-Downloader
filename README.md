# OFDL — OnlyFans Downloader

A local, GUI-driven OnlyFans backup tool. Sign in once through the
in-app browser, then bulk-download your own posts, the creators
you're subscribed to, individual paid DMs, or your whole vault to
a clean, per-creator folder layout on disk.

Everything runs locally on your PC — your OnlyFans password and
downloaded media stay on your machine. On first launch OFDL asks
you to acknowledge **anonymous usage statistics** (see
[Privacy](#privacy--anonymous-usage-statistics) below).

---

## Screenshots

![OFDL main window](https://i.postimg.cc/rmPSs9T1/Screenshot-2026-05-25-205111.png)

<!--
Drop the second screenshot URL on the line below and remove this
comment block once it's in place.
-->

![OFDL second screenshot — pending URL]()

---

## Quick start

1. Double-click **`OFDL.exe`**.
   - First launch takes ~10–20 seconds while the bundled runtime
     unpacks to `%TEMP%`. Subsequent launches are faster.
   - If you don't have **Google Chrome** (or Microsoft Edge), the
     Sign-In page will tell you and offer to either download
     Chrome or let you point at an existing `chrome.exe`. See
     [Requirements](#requirements) below.
2. Click **Open Chrome and sign in**. A real Chrome window opens;
   log in to OnlyFans normally (email + password, 2FA, whatever
   your account uses).
3. Once you're back in the main window, pick a tab from the left
   sidebar and start backing up.

That's it. No config files to edit, no API keys to obtain. OFDL
creates a `config.json` next to the EXE on first sign-in and
keeps your session there for next time.

---

## What it does

OFDL backs up content you can already see in your OnlyFans web UI.
It does **not** bypass paywalls or steal anything — if you can't
view it in the browser, OFDL can't download it either.

| Tab               | What it backs up                                                                                  |
|-------------------|----------------------------------------------------------------------------------------------------|
| **Account**       | Your own account: profile pic, banner, vault, stories, your posts, your DMs.                       |
| **Backup**        | Same as Account, but with toggles for what to include (photos, videos, messages, vault, stories).  |
| **Subscriptions** | Bulk-download every subscribed creator. Tick the ones you want and OFDL walks each profile.        |
| **PPV**           | Paste a single paid-DM permalink to back up just that one message (incl. DRM video decryption).    |
| **Gallery**       | Browse already-downloaded photos and videos right inside the app — no need to dig through folders. |
| **Settings**      | Storage location, content-type toggles, DRM bypass tools, settings export/import.                  |
| **Help**          | In-app version of this README.                                                                     |

### File layout on disk

Everything lands in your configured backup folder (default:
`backup/` next to `OFDL.exe`; change it any time via
**Settings → Save folder**):

```
backup/
  <creator>/
    Profile Pic/
      avatar.jpg
    Banner/
      banner.jpg
    Metadata/
      <creator>.json          # creator info dump
    Photos/
      <media_id>.jpg
    Videos/
      <media_id>.mp4
    Messages/
      <media_id>.jpg
      PPV_<message_id>.mp4    # PPV-decrypted videos
      Paid_content.json       # purchase history catalogue
    Stories/                  # your-own-account only
    Vault/                    # your-own-account only
```

---

## DRM-protected paid videos

Paid DM videos are Widevine-DRM-encrypted by OnlyFans, so a normal
download only gives you the preview thumbnail. To decrypt them to
source-quality MP4 (with audio), OFDL needs three files plugged in
under **Settings → DRM bypass**:

1. **`client_id.bin`** — a Widevine L3 device blob.
2. **`private_key.pem`** — the matching RSA private key.
3. **`mp4decrypt.exe`** — the decrypt binary from the Bento4 SDK.
   - Free download: <https://www.bento4.com/downloads/>
   - Extract the Windows ZIP, then point OFDL at the `.exe` inside
     the `bin/` folder.

You have to source the Widevine L3 device yourself (public dumps
from older Android phones work fine; OFDL does not ship one). Once
all three paths are configured, OFDL's Settings page shows a green
"DRM bypass ready" banner.

### Backing up a specific PPV

1. Open the chat with the creator.
2. Under the creator's name at the top of the chat, click
   **"Purchases"**.
3. Find the PPV you want.
4. Click the **⋮** three-dot menu on that message and choose
   **"Copy link to message"**.
5. Paste the link into the PPV tab and click **"Add to queue"**
   or **"Download now"**.

The result lands at
`<your backup folder>/<creator>/Messages/PPV_<message_id>.mp4`.

> **Heads-up about the console flash.** While a PPV is decrypting
> you may briefly see a small black command-prompt window pop up
> on-screen. That's `mp4decrypt.exe` — Bento4's CLI tool — doing
> the actual decryption step. The window closes itself within a
> second and is completely normal; it is **not** malware or a
> crash. (OFDL 1.2+ tries to suppress the popup via
> `CREATE_NO_WINDOW`, but some antivirus products still un-hide
> child consoles, so don't be surprised if you see it.)

> **Note:** PPV decryption is only triggered from the PPV tab.
> A full per-creator backup will save the *preview* for DRM items
> and catalogue them in `Paid_content.json` so you can hand-pick
> which ones to decrypt later.

---

## Where do my downloads land?

The save folder is controlled by **Settings → Save folder**.

- Click **Browse…** to pick a new folder — the choice is saved
  *immediately* (no separate "Save settings" button needed). The
  "Active save folder" line right below the entry shows the
  absolute path currently in effect.
- You can also type a path directly. Hit **Enter** or click away
  from the field to commit.
- Relative paths (e.g. `backup`) are anchored to the folder that
  holds `OFDL.exe`, so the location is stable regardless of which
  shortcut, taskbar pin, or cwd you launched from.
- Default: `<OFDL.exe folder>/backup/`.

> **Earlier builds had a bug** where picking a folder via Browse
> did not auto-save until you scrolled down and hit "Save settings"
> at the bottom of the page. If you upgraded from an older build,
> open Settings once and re-pick your save folder so the value is
> committed under the new auto-save logic.

---

## Requirements

- **Windows 10 / 11 (x64).**
- **A Chrome-family browser** installed system-wide. OFDL drives a
  real browser for the sign-in window (and for DRM playback if
  you enable the bypass), so it needs one of these on disk:
  - **Google Chrome** — recommended. Download:
    <https://www.google.com/chrome/>.
  - **Microsoft Edge** — works as a fallback. Ships with
    Windows 10 / 11 out of the box, so most users already have it.
  - **Chrome Beta / Chrome Dev / Chrome Canary** — also accepted.

  OFDL searches the registry and the standard `Program Files`
  install paths automatically on first launch. If your Chrome
  lives somewhere unusual, the Sign-In screen pops up a **"Locate
  Chrome…"** card so you can point OFDL at the right `chrome.exe`
  (or `msedge.exe`); the path is then remembered in `config.json`
  next to the EXE. You can change it later via
  **Settings → Browser**.
- **An active OnlyFans account.**
- *(Optional, only for DRM video decrypt)* Bento4
  `mp4decrypt.exe` + a Widevine L3 device — see above.

No Python, no command-line work, no extra installers. The `.exe`
is fully self-contained — your settings live in a `config.json`
created next to it on first run.

---

## Tips & gotchas

- **First launch is slow** (~10–20s) while the one-file bundle
  unpacks. Subsequent launches are faster.
- **Antivirus false positive?** Some AV products flag PyInstaller
  one-file binaries because they self-extract on launch. The bundle
  is just Python + a few native dependencies — feel free to scan
  it on <https://www.virustotal.com> before trusting it.
- **"Sign out" doesn't delete anything on disk.** It just throws
  away the cached browser session so you can log in with another
  OnlyFans account next time.
- **Export your settings** before reinstalling or moving machines:
  Settings → "Export settings…". The exported JSON does **not**
  contain your OnlyFans password, but does contain the saved
  session cookies — treat it like a password file.
- **Gallery is read-from-disk.** Delete a file with Explorer and
  it disappears from the Gallery on next refresh.
- **Backups are resumable.** If the app crashes mid-run, just
  start it again — OFDL skips files that are already on disk.

---

## Troubleshooting

| Symptom                                                  | Likely cause / fix                                                                                                                          |
|----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| "OFDL couldn't find Google Chrome…" on the Sign-In page  | No Chrome / Edge installed in a standard location. Click **Download Chrome** or **Locate Chrome…** on the same screen to point OFDL at it.   |
| Sign-in window opens but blank / disconnects             | OnlyFans rate-limited the sign-in. Wait a minute and try again.                                                                              |
| PPV downloads as a tiny `.jpg`, not the video            | The DRM bypass isn't fully configured. Open Settings → DRM bypass — all three paths must be green.                                           |
| `mp4decrypt` not found                                   | You haven't downloaded Bento4 yet, or you pointed at the SDK root instead of `bin/mp4decrypt.exe`.                                           |
| Long PPV video fails with "string too long"              | Update — newer builds stream large segments via curl. Re-download `OFDL.exe`.                                                                |
| Sub backups go very slowly on huge accounts              | Normal — OFDL throttles requests to stay within OF's rate limits. Let it run; it'll resume on crash.                                         |
| Help tab shows nothing                                   | First time the layout engine warms up. Click another tab and come back.                                                                      |
| "Saved Chrome path doesn't exist"                        | You moved or uninstalled the Chrome you previously pointed at. Open Settings → Browser → **Clear** and let OFDL auto-detect, or pick a new path. |
| Files keep landing in `backup/` next to the EXE          | You picked a new folder via **Settings → Save folder → Browse…** but it didn't stick? Re-pick it once — the choice now auto-saves the moment you hit OK. The "Active save folder" line below the entry should immediately update to the new absolute path. |
| Small black console window flashes during PPV decrypt    | Expected. That's Bento4's `mp4decrypt.exe` doing the actual decrypt — see [DRM-protected paid videos](#drm-protected-paid-videos).            |

---

## Privacy & anonymous usage statistics

On first launch, OFDL shows a notice about **anonymous usage
statistics**. If you continue, the app may send non-identifying
signals to the project operator to help fix bugs and prioritize
features. Examples include:

- App opened / signed out
- Backup finished (counts only — e.g. photos, videos, size — **not**
  your files)
- Pro license activated (anonymous machine ID + basic hardware info:
  CPU, GPU, RAM)
- Unexpected crashes (error message and stack trace)
- Support messages you submit in **Help → Support** (includes the
  email address you provide so we can reply)

**What is never sent:** your OnlyFans password, private messages,
or your full backup library.

Each report is linked to an **anonymous machine identifier**, not
your name or OF username.

For privacy, legal, or support questions, contact:

**moneytalks2@rambler.ru**

---

## Disclaimer

OFDL is for **personal archival** of content you have legal access
to (your own posts). You are responsible for complying with
OnlyFans' Terms of Service and the laws of your jurisdiction. Do
not redistribute downloaded content.

The author and project owner accept **no responsibility for
misuse** of this software. By using OFDL you agree that any
content you download, store, or share is done at your own risk
and under your own legal accountability — including compliance
with applicable copyright law, platform terms of service, and the
laws of your local jurisdiction.

**Contact (legal & support):** moneytalks2@rambler.ru

---

## License

Released under the **MIT License**.

```
MIT License

Copyright (c) 2026 OFDL contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
