# Orbit

**Assignments, tasks, daily checklists and grades in one place. Installs like a real app, works offline, and optionally syncs across your devices with an account.**

**[Open Orbit](https://venus-beetroot.github.io/Orbit-App/)** then add it to your home screen (instructions below).

Orbit is a tracker built for high school students juggling assessments across multiple subjects and curriculums. No ads, no tracking, and unless you turn on sync, no network at all.

## Features

**Daily checklist**
A homework board that resets itself every morning. It ships with default subjects for two curriculums, switchable with one tap:

| Subject | HSC | A Levels |
|---------|-----|----------|
| Chemistry, Physics, Math | yes | yes |
| English, SOR, VA | yes | no |

Add your own subjects (to either curriculum or both), and add specific sub tasks under each subject, like "Ex 7C q1 to 10". Ticking every sub task automatically ticks the subject, and ticking a subject ticks all its sub tasks. Overnight, all ticks clear: finished sub tasks disappear and unfinished ones carry over to today. There is also a Projects board with the same behaviour, empty by default.

**Assessment prep reminders**
Give any assignment a daily prep reminder like "one past paper section per day". It appears at the top of the Daily tab every single day, up to and including the due date, and resets each morning until then.

**Assignments**
Track every assessment with its class, priority, weighting, assessment type (in-class or hand-in), date assigned and due date. Orbit automatically flags anything overdue and tells you by how many days.

**Tasks**
A quick checklist for things you just need to jot down, plus essential tasks (homework, study preparation, projects and more) with full due date and priority tracking.

**Grade calculator**
Create a table for each subject, pull in assessments you have already logged (weighting fills in automatically), and enter your marks. Orbit computes your weighted grade live with a letter grade: A 85+, B 70+, C 55+, D 50+, Fail below 50.

**Also in the box**
Light and dark themes, full offline support, and JSON export/import backups.

## Install it

**iPhone and iPad:** open [the link](https://venus-beetroot.github.io/Orbit-App/) in Safari, tap Share, tap **Add to Home Screen**.

**Mac:** open the link in Safari and choose File, then **Add to Dock**. In Chrome or Edge, click the install icon in the address bar.

**Windows and Android:** open the link in Chrome or Edge and use the install prompt.

Installing matters: home screen apps get protected storage on iOS, so your data survives long gaps between uses. A plain browser tab can have its storage cleared after about a week of inactivity.

## Accounts and sync (optional)

Out of the box, Orbit is fully local and makes zero network requests. To let people sign in and sync their data across devices, the app owner does a one-time setup with a free [Supabase](https://supabase.com) project:

1. Create a Supabase account and a new project (free tier is fine).
2. In the project, open the **SQL Editor** and run:

```sql
create table if not exists orbit_profiles (
  user_id uuid primary key references auth.users(id) on delete cascade,
  data jsonb not null,
  updated_at timestamptz not null default now()
);

alter table orbit_profiles enable row level security;

create policy "Users manage own profile" on orbit_profiles
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

3. Recommended: in **Authentication > Sign In / Providers > Email**, turn off **Confirm email**. Otherwise every new user has to click a confirmation link before their first sign in.
4. In **Settings > API**, copy the **Project URL** and the **anon public** key.
5. Open `index.html`, find `SYNC_CONFIG` near the top of the script, and paste both values in.
6. Bump the version in `sw.js` (`orbit-v2` to `orbit-v3`) and push.

Users then create an account from the database icon in the header (top right) with just an email and password. Once signed in, changes sync automatically a couple of seconds after every edit, and each device pulls the latest data on launch.

How sync resolves conflicts: whole profile, last write wins. If you edit on your phone offline and on your laptop online, whichever device syncs later becomes the truth. For a single person moving between devices this is fine; it is not built for two people editing one account simultaneously.

Row level security means every account can only ever read and write its own row, even though all accounts share one database.

## Privacy

Without an account: everything is stored locally on your device, nothing is uploaded anywhere, and there are no analytics of any kind. With an account: your data is stored in the app owner's Supabase project, protected per-user by row level security, and still contains no tracking. Export and import buttons are always available for manual backups.

## Tech notes

Orbit is a single vanilla JavaScript file with no frameworks, no build step and no dependencies. Sync uses Supabase's REST and auth endpoints directly via `fetch`, so there is no SDK to load. Offline support comes from a service worker (`sw.js`) and the manifest makes it installable.

```
index.html            the entire app: UI, logic, themes, sync
sw.js                 service worker for offline caching
manifest.webmanifest  install metadata
icon-*.png            app icons
```

Grade boundaries live in the `letterFor` function. The default daily subjects live in `defaultDaily`. To run locally: `python3 -m http.server 8000` then open http://localhost:8000.

## Updating

After editing `index.html`, bump the cache version at the top of `sw.js` and push. Everyone gets the new version the next time they open the app online.

## License

See [LICENSE](LICENSE).
