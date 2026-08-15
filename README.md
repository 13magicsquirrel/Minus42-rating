# Fruit Squad — Minus42 stall app

A four-game iPad app for 8–10 year olds, built on the Minus42 brand language.
Kids play, then rate the flavours they tasted at the stall, then land on the
"Stay in Touch" page with the Instagram QR.

```
fruit-squad/
├── index.html          ← the app. This is the whole thing.
├── stay-in-touch.html  ← optional standalone poster screen (see note below)
├── .nojekyll           ← tells GitHub Pages to serve the files as-is
└── README.md
```

**Upload all of them to GitHub, including `.nojekyll`.** It's a hidden file — on
a Mac, press `Cmd + Shift + .` in Finder to see it.

`index.html` is completely self-contained: all 8 mascots, the logo, the
Instagram QR and the home-screen icon are embedded in the file. The only thing
it loads from the internet is Google Fonts, so it works fine with no wifi.

> **About `stay-in-touch.html`:** the app no longer links to it. The Instagram
> QR and "follow us" now appear *inside* the app on the thank-you screen, so the
> kiosk never navigates away and never gets stranded on a dead-end page. The
> file is kept in case you want to run it as a separate poster screen on a
> second display — delete it if you don't, the app doesn't need it.

---

## The flow

1. **Home** — big PLAY button, the chilling crew, order link, and a
   **"Skip the game — just leave a taste review"** option for visitors who only
   want to rate the samples.
2. **One game, picked at random** — a different one every play, never the same
   twice in a row, always on a countdown clock:

   | Game | What it is | Clock |
   |---|---|---|
   | Match the Crew | memory pairs with the fruit mascots | 75s |
   | Crunch Words | 10×10 word hunt — nutrition vocabulary | 100s |
   | Freeze It or Skip It | sort real fruit & veg from sugary stuff | 80s |
   | Snack Smarts | 6 nutrition questions from a pool of 12 | 90s |

   Up to 3 ✦. Stars come from getting it right **and** beating the clock. Nobody
   ever scores zero — the tone stays encouraging. If the clock runs out the game
   ends gracefully and moves on. One game per visitor keeps the stall queue moving.

3. **Taste test** — rate the six flavours: mango, apple, pineapple, strawberry,
   jackfruit, jamun. Flavours left blank = "didn't try it". Tap a star again to
   undo. There's an **optional phone number** field (+91, validated as a real
   Indian mobile: 10 digits starting 6–9; `+91` and a leading `0` are accepted).
4. **Thank you** — the Instagram QR (`@minus42world`) and order link show right
   here, **inside the app**. It never navigates away to another page, so the
   kiosk is never stranded. After 20 seconds it resets itself to the home
   screen, ready for the next visitor — or staff tap **"Done — back to start"**.

---

## Where the ratings go

Every taste test is appended to `localStorage` on that iPad under the key
`fruitSquad.v1`. Visitors never overwrite each other — each one is a new row.

### Does a refresh wipe it? No.

Ratings are written to the iPad's `localStorage`, which survives page refreshes,
closing the tab, and restarting the iPad. Nothing in the app deletes data on
load — the only delete path is the "Clear all data" button, which sits behind
the PIN **and** a confirmation dialog.

Two things *do* wipe it, so export regularly:

- Safari → Settings → **Clear History and Website Data**
- Roughly **7 days** with the site unused (Safari's storage eviction), or
  private browsing

One gotcha: storage is tied to the exact address. Ratings collected on a
`file://` copy won't appear on the GitHub Pages version, and vice versa. Pick
one address and stick to it.

### Getting the data out

**Tap the small "Results 🔒" button in the bottom-left of the home screen**, then
enter the stall PIN. (Tapping the minus42 logo 5 times does the same thing.)

**The default PIN is `1309`.** Change it at the top of the `<script>` block:

```js
STALL_PIN: "1309",
```

It's a soft lock — enough to stop a curious 9-year-old, not real security, since
anyone who views the page source can read it. Don't reuse a password you care
about.

The dashboard shows the running average and rating count per flavour, and gives
you three buttons:

- **Save today's results (CSV)** — writes `minus42-taste-tests-YYYY-MM-DD.csv`
  to the iPad's Files app. One row per visitor: when, their phone number if they
  gave one, then one column per flavour (blank where they didn't try it).

  ```
  when,phone,mango,apple,pineapple,strawberry,jackfruit,jamun
  2026-08-15T20:37:23.254Z,9876543210,5,,,4,,
  ```

  The button saves **exactly one file per tap** and then shows "✓ Saved N
  reviews" for a few seconds. iPad downloads are silent, so without that
  confirmation it's easy to tap three times and end up with three copies —
  the button now locks itself to prevent that.
- **Copy results instead** — same data to the clipboard, to paste into an email
  or a spreadsheet.
- **Clear all data** — wipes the stored tests. Asks you to confirm first.

> **Export at the end of every stall day.** The data lives only on that iPad.

---

## Putting it on GitHub Pages

Hosting it gives you a real `https://` address, which makes the stored ratings
much more durable than a `file://` page, and lets you add it to the iPad home
screen as a fullscreen app.

No command line needed — drag and drop works fine.

1. Create a new repository on github.com — name it `fruit-squad`, set it
   **Public**, and leave "Add a README" unticked (you already have one).
2. On the empty repo page, click **uploading an existing file**.
3. Open the `fruit-squad` folder in Finder, press `Cmd + Shift + .` to reveal
   hidden files, select **all four** (`index.html`, `stay-in-touch.html`,
   `README.md`, `.nojekyll`) and drag them into the browser.
4. Click **Commit changes**.
5. Go to **Settings → Pages**. Under "Build and deployment", set Source to
   **Deploy from a branch**, branch **main**, folder **/ (root)**. Save.
6. Wait ~1 minute, then refresh that page — it shows your live link:
   `https://<your-username>.github.io/fruit-squad/`

To update the app later, upload a new `index.html` over the old one — the iPad
picks it up on next load, no need to touch the device.

<details>
<summary>Prefer the command line?</summary>

```bash
cd ~/Desktop/fruit-squad
git init -b main
git add -A
git commit -m "Fruit Squad — Minus42 stall app"
git remote add origin https://github.com/<your-username>/fruit-squad.git
git push -u origin main
```
Then do step 5 above.
</details>

### On the iPad

1. Open the URL in Safari.
2. Share button → **Add to Home Screen**.
3. Launch it from the home screen icon — it opens fullscreen with no Safari
   chrome, which is what you want at a stall.
4. Optional, for an unattended stall: Settings → Accessibility → **Guided
   Access**, then triple-click the side button to lock the iPad into the app so
   kids can't wander off into Safari.

---

## Things you may want to change

All at the top of the `<script>` block in `index.html`:

```js
const CONFIG = {
  ORDER_URL:  "https://minus42world.com/",
  AUTO_HOME_SECONDS: 20,                // kiosk resets itself; 0 turns it off
  STORAGE_KEY: "fruitSquad.v1",
  STALL_PIN: "1309",                    // unlocks the Results button
  TIME: { memory: 75, words: 100, sort: 80, quiz: 90 }   // seconds per game
};
```

- **Flavours** — the `FLAVOURS` array just below `CONFIG`, with brand product
  colour-coding. Add or remove a flavour and the taste test and dashboard both
  follow automatically.
- **Quiz questions** — the `QUESTIONS` array. There are 12; each visitor gets a
  random 6.
- **Word list** — `GAMES.words.WORDS` and the matching `CLUES`.
- **Sugary items in the sorting game** — the `JUNK` array (line-art SVG icons,
  drawn in the brand's outline style).

---

## Tested on

iPad Air / iPad 9th–10th gen, landscape and portrait, both in Safari (with
browser chrome) and fullscreen from the home screen. Layout is driven by
viewport units throughout, so it also holds up on iPad Pro and iPad mini.
Sound is generated in-browser and can be muted from the home screen.
