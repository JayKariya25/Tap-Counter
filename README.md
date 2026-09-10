# Tap Counter

Count anything — reps, laps, stock, birds, cars — with as many counters running at once as you
like. The whole app is one HTML file: no build step, no dependencies, no account, no tracking
and nothing fetched from a CDN. Install it to your home screen and it works with no network
at all.

### 👉 [**Try it live — jaykariya25.github.io/Tap-Counter**](https://jaykariya25.github.io/Tap-Counter/)

**Install it:** on iPhone or iPad, *Share → Add to Home Screen*; on Android, Chrome offers
*Install app*. Either way you get a full-screen app icon, and it keeps working offline.

---

## Features

**Counting**
- **Unlimited counters at once** — each one counts completely separately.
- **A big tap pad** on every counter, with three layers of feedback per tap: the pad washes
  with that counter's own colour, a ripple spreads from where you touched, and `+1` floats up
  — plus optional vibration and sound.
- **`+` / `−` buttons** for corrections, and negative counts are allowed.
- **Step size** — count in 2s, 5s, 12s, anything.
- **Focus mode** (`⤢` on any card, or press `F`) — one counter fills the entire screen on a
  colour-tinted stage, so you can count one-handed without looking at the screen.

**Staying in control**
- **Reset** on each card, plus **Reset all** — both confirm first, both undoable.
- **Undo and redo**, 200 steps deep.
- **Target** — set a goal, get a progress bar and a celebration when you reach it.
- **Lock** a counter so stray taps can't change it.
- **Delete** right on the card (🗑, next to `⤢` and `⚙`) — confirms first, and undoable.

**Organising**
- **Rename** a counter by tapping its name.
- **Colour**, **duplicate** and **reorder** under `⚙` on each card.
- **Export / import** — JSON (round-trips perfectly) or CSV (Excel, Numbers, Sheets).

**Everywhere**
- **Installable and offline** — add it to your home screen and it launches full-screen and runs
  with no network, exactly as it does online.
- **Responsive** from a 320px phone to an ultrawide monitor: 1 column to 11, no horizontal
  scrolling at any size, notch- and home-indicator-safe.
- **Light / dark / auto** theme, following your system by default.
- **Auto-save** — counts survive closing the tab, and two open tabs stay in sync.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `N` | New counter |
| `1`–`9` | Tap that counter |
| `F` | Focus mode (`Esc` leaves it) |
| `⌘Z` / `Ctrl+Z` | Undo |
| `⇧⌘Z` / `Shift+Z` | Redo |
| `R` | Reset all |
| `T` | Change theme |

## Run it locally

Double-click `index.html`, or drag it into any browser. That's the whole setup.

To open it on your phone over your own Wi-Fi, serve the folder and visit your computer's IP:

```bash
cd tap-counter
python3 -m http.server 8000
# then on your phone: http://<your-computer-ip>:8000
```

On iPhone or iPad, **Share → Add to Home Screen** gives it a full-screen app icon with no
browser chrome.

## Deploy it free

This copy is hosted on **GitHub Pages**. The site is static, so there is no build command, no
publish directory and nothing to configure — `index.html` sits at the repo root and is served
as-is.

To publish it yourself:

1. Push the repo to GitHub. It must be **public** for Pages on a free account.
2. **Settings → Pages → Source:** `Deploy from a branch`.
3. **Branch:** `main`, folder `/ (root)` → **Save**.
4. It goes live at `https://<username>.github.io/<repo>/` in about a minute.

After that, every push to `main` redeploys the site automatically.

**Other free hosts**, all zero-config for a site like this: drag the folder onto
[Netlify Drop](https://app.netlify.com/drop), or connect the repo to Cloudflare Pages or
Vercel and leave the build settings empty.

## How it works

The app itself is entirely `index.html` (~60 KB) — markup, styles and script inline, no
framework and no external requests at all, so it can't break when a CDN does. The manifest,
service worker and icons alongside it add nothing to the app's behaviour; they only make it
installable and available offline.

- **State** is a small JSON blob in `localStorage`, written on a 250 ms debounce and flushed on
  `pagehide`, so nothing is lost when you close the tab.
- **Loading is defensive** — corrupt, hand-edited or hostile saved data is sanitised field by
  field rather than trusted, so a bad value can never leave you with a blank or `NaN` counter.
- **Taps count on `pointerdown`** for instant response, with the follow-up `click` swallowed so
  a tap never counts twice; keyboard activation still counts exactly once.
- **Rendering** keeps a handle on each card, so a tap updates only that number rather than
  redrawing the page — 200 counters stay responsive.
- **Accessibility** — fully keyboard operable, labelled controls, visible focus rings, and
  animations are disabled under `prefers-reduced-motion`.
- **Offline** comes from a service worker using a network-first strategy: online you always get
  the newest version, and the cache steps in only when the network doesn't answer. Pushing an
  update therefore reaches people on their next online visit — no stale-cache trap.
- **Opened straight off the disk** (`file://`) everything still works; service workers and
  manifests need a real origin, so those simply don't attach there.
- **Locked down by a Content-Security-Policy** of `default-src 'none'` with only same-origin
  scripts, styles, images and worker allowed, `connect-src 'none'`, and no `form-action` or
  `base-uri`. Nothing can be loaded from another site and nothing can be sent anywhere — the
  app makes no network requests of its own by design.

## Browser support

Any current browser: Chrome, Edge, Safari, Firefox, iOS Safari and Android Chrome.

Vibration in practice means Android phones — iOS browsers don't expose the Vibration API at all,
so the ⚡ toggle hides itself there; desktop browsers report support but have no hardware to
buzz. Sound uses the Web Audio API, works everywhere, and is off by default.

Installing works over HTTPS (as on GitHub Pages): Android Chrome shows an *Install app* prompt,
and iOS installs through *Share → Add to Home Screen*.

## Data and privacy

There is no server, no account, no analytics and no third-party code. Counts never leave the
device — they're stored in that browser's local storage, so each device (and each site you host
it on) keeps its own set. Use **Export / Import** to move counters between devices.

Imported files are treated as untrusted: every field is validated and coerced, and text only
ever reaches the page through `textContent` and form values, never as HTML.

## Project structure

```
index.html       the entire app — markup, styles and script
manifest.json    name, colours and icons for installing to a home screen
sw.js            service worker: caches the app so it runs offline
icons/           app icons (192, 512, maskable, and an iOS touch icon)
README.md        this file
```
