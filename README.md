# Orbit

Assignments, tasks and grades. An offline-first tracker your students install straight from a link, with no App Store, no accounts and no server. All data stays on the student's own device.

## What's in this folder

- `index.html` - the entire app (UI, logic, grade formulas, themes)
- `sw.js` - service worker that caches everything so it runs fully offline
- `manifest.webmanifest` - makes it installable as a standalone app
- `icon-*.png` - app icons

## Host it (once, takes ~5 minutes)

The app needs to be served over HTTPS for offline install to work. GitHub Pages is free and you have used it before:

1. Create a new GitHub repository (e.g. `orbit`).
2. Upload all files in this folder to the repository root.
3. Settings > Pages > Source: `main` branch, root folder. Save.
4. Your app is live at `https://YOURNAME.github.io/orbit/`. Share that link with students.

Netlify Drop (drag the folder onto netlify.com/drop) works too.

## Students install it

**iPhone / iPad (Safari):** open the link, tap Share, tap "Add to Home Screen". It gets its own icon, opens full screen and works offline from then on.

**Mac (Safari 17+):** open the link, File > Add to Dock. It appears in the Dock and Launchpad like a normal app.

**Mac (Chrome/Edge):** open the link and click the install icon at the right end of the address bar.

After the first visit, the service worker caches everything, so it opens and works with no internet at all.

## Grade boundaries

Hard-coded in `index.html` in the `letterFor` function:

- A: 85 and above
- B: 70 to below 85
- C: 55 to below 70
- D: 50 to below 55 (this band was undefined in the original spec, so it is labelled D)
- Fail: below 50

Edit that one function to change bands.

## Data and backups

Everything is stored in the browser's local storage on the device. Nothing leaves the device. The database icon in the header lets anyone export a `.json` backup and import it on another device.

One caveat worth telling students: if they use the app in a normal Safari tab (not installed to the home screen) and don't open it for a long stretch, iOS can clear website storage. Installing it to the home screen avoids this, and exporting a backup before holidays is a good habit anyway.

## Updating the app

Edit `index.html`, then bump the version string at the top of `sw.js` (`orbit-v1` to `orbit-v2`). Push to GitHub. Students get the new version the next time they open the app with internet.
