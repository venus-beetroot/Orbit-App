# Orbit

**Assignments, tasks and grades in one place. Installs like a real app, works completely offline, and your data never leaves your device.**

**[Open Orbit](https://venus-beetroot.github.io/Orbit-App/)** then add it to your home screen (instructions below).

Orbit is a tracker built for high school students juggling assessments across multiple subjects. No accounts, no sign-up, no server. Open the link once and it works forever, even with zero internet.

## Features

**Assignments**
Track every assessment with its class, priority, weighting, assessment type (in-class or hand-in), date assigned and due date. Orbit automatically flags anything overdue and tells you by how many days.

**Tasks**
Two kinds, because not all work is equal:
- **Quick checklist** for things you just need to jot down and tick off
- **Essential tasks** for homework, study preparation and projects, with the same due date and priority tracking as assignments

**Grade calculator**
Create a table for each subject, pull in assessments you have already logged (weighting fills in automatically), and enter your marks. Orbit computes your weighted grade live and shows it on a ring gauge with a letter grade.

| Grade | Range |
|-------|-------|
| A | 85 and above |
| B | 70 to below 85 |
| C | 55 to below 70 |
| D | 50 to below 55 |
| Fail | below 50 |

**Also in the box**
- Light and dark themes
- Full offline support after the first visit
- Export and import your data as a JSON backup (database icon, top right)

## Install it

**iPhone and iPad**
1. Open [the link](https://venus-beetroot.github.io/Orbit-App/) in Safari
2. Tap the Share button
3. Tap **Add to Home Screen**

**Mac**
Open the link in Safari and choose File, then **Add to Dock**. In Chrome or Edge, click the install icon at the right end of the address bar.

**Windows and Android**
Open the link in Chrome or Edge and use the install prompt in the address bar or browser menu.

Installing matters: home screen apps get their own protected storage on iOS, so your data survives even if you do not open the app for weeks. If you only bookmark it in a browser tab, iOS may clear the data after about a week of inactivity. Either way, exporting a backup before holidays is a good habit.

## Privacy

Everything is stored locally on your own device using browser storage. Nothing is uploaded, tracked or shared. There is no analytics, no network requests after the first load, and no way for anyone (including the developer) to see your data. The flip side: data does not sync between devices. Use the export and import buttons to move it manually.

## Tech notes

Orbit is a single vanilla JavaScript file with no frameworks and no build step. Offline support comes from a service worker (`sw.js`) that caches the app shell, and the manifest makes it installable as a standalone app.

```
index.html            the entire app: UI, logic, themes
sw.js                 service worker for offline caching
manifest.webmanifest  install metadata
icon-*.png            app icons
```

To change the grade boundaries, edit the `letterFor` function in `index.html`. To run it locally, serve the folder over HTTP:

```
python3 -m http.server 8000
```

then open http://localhost:8000.

## Updating

After editing `index.html`, bump the cache version at the top of `sw.js` (for example `orbit-v1` to `orbit-v2`) and push. Everyone gets the new version the next time they open the app with an internet connection.

## License

See [LICENSE](LICENSE).