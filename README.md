# Orbit

**A planner and an error log in one app, built for HSC and A Level students. It installs like a real app, works offline, and can sync across your devices with an account.**

**[Open Orbit](https://venus-beetroot.github.io/Orbit-App/)** then add it to your home screen (instructions below).

Orbit opens on a home screen with two parts. The **Planner** keeps track of assignments, daily homework, tasks and grades. The **Error Log** is where you record the questions you got wrong and drill them until you get them right. No ads, no tracking, and unless you sign in, no network at all.

## Error Log

Most marks lost in exams are lost the same way twice. The Error Log is for breaking that cycle: log a mistake once, then keep drilling it until it sticks.

**Logging a mistake.** Every entry has five sections, each built from blocks you can mix and reorder:

| Section | What goes in it |
|---------|-----------------|
| Stimulus (optional) | A source extract, data table, diagram or scenario |
| Question | The question itself |
| Your answer | What you actually wrote |
| Correct answer | The right answer or worked solution |
| Why it went wrong (optional) | What tripped you up and what to remember |

A block can be text, an equation or an image. Text supports inline maths, and screenshots can be pasted straight in (Cmd+V or Ctrl+V), dragged onto a section, or added with the Image button, which opens the photo library on a phone. Large images are scaled to 1600 pixels automatically so they stay quick to load and sync.

Each mistake is also tagged with a subject, a topic, a source (like "2023 HSC Q14(b)"), its marks and an error type: careless slip, concept gap, misread the question, wrong method, did not know it, or ran out of time.

**Typing maths.** Orbit renders LaTeX with KaTeX, bundled with the app so it works offline.

| You type | You get |
|----------|---------|
| `$x^2 + 1$` | inline maths |
| `$$\int_0^1 x\,dx$$` | maths on its own line |
| `\(...\)` and `\[...\]` | the same, other style |
| `$\ce{N2 + 3H2 <=> 2NH3}$` | chemical equations (mhchem) |
| `\$5` | a plain dollar sign |

Prices are safe too. "It costs $500 and $200" stays plain text, because inline maths can't start or end next to a space.

**Sorting.** Filter by curriculum, subject and topic, by status (to drill, mastered, all), or search across every question, answer and note. Each subject has its own colour, and topics can take their own colour as well. Colours carry through to the Planner: an assignment for "Chemistry" gets the Chemistry colour.

**Drilling.** Pick a scope (everything, one subject, or whatever the Log tab is filtered to) and an order (oldest first, shuffled, or weakest first). For each question you can type an attempt, then reveal the answer and mark yourself honestly. Anything you get wrong comes back a few questions later in the same session, so a session only ends once you've got everything right. On a keyboard, Space reveals the answer and 1 or 2 marks it.

A mistake counts as **mastered** once you've got it right on two separate days. Getting it right twice in one sitting isn't proof you'll remember it next week. The rule is adjustable (1, 2 or 3 days) under Subjects, and any wrong answer resets the count.

**Default subjects.** These are a starting point. Everything can be renamed, recoloured, reordered, added or deleted under Subjects.

*HSC.* Maths and English follow the 2024 syllabuses, which are first examined in the 2027 HSC. Physics and Chemistry stay on the 2017 syllabuses until their new versions are first examined, in 2028 and 2029 respectively.

| Subject | Topics |
|---------|--------|
| Mathematics Advanced | Year 11 and 12 focus areas, from Working with functions to Financial mathematics |
| Mathematics Extension 1 | Further work with functions through to the binomial and sampling distributions |
| Mathematics Extension 2 | Nature of proof, vectors, complex numbers, further integration, mechanics |
| English Advanced | Texts and human experiences, Textual conversations, Critical study of literature, The craft of writing, plus the Year 11 focus areas |
| Physics | Modules 1 to 8 |
| Chemistry | Modules 1 to 8 |

*Pearson Edexcel International A Level.*

| Subject | Units |
|---------|-------|
| Mathematics | P1 to P4, M1, M2, S1, S2, D1 |
| Further Mathematics | FP1 to FP3, M2, M3, S2, S3 |
| Physics | Units 1 to 6 |
| Chemistry | Units 1 to 6 |

## Planner

**Assignments.** Track every assessment with its class, priority, weighting, assessment type (in-class or hand-in), date assigned, due date and optional due time. Each one shows a live countdown that gets more precise as the deadline approaches. Without a due time, the countdown runs to the end of the due day.

**Daily checklist.** A homework board that resets every midnight, with HSC and A Level defaults. Add standalone daily tasks, or add a subject and put specific sub tasks under it. Unfinished sub tasks carry over to the next day. Give any assignment a daily prep reminder and it appears here every day until the due date.

**Tasks.** A quick checklist for things to jot down, plus essential tasks with the same countdowns as assignments.

**Grades.** One table per subject. Pull in assessments you've already logged (their weighting fills in automatically), enter your marks, and Orbit works out your weighted grade: A 85+, B 70+, C 55+, D 50+, Fail below 50.

## Install it

**iPhone and iPad:** open [the link](https://venus-beetroot.github.io/Orbit-App/) in Safari, tap Share, then tap **Add to Home Screen**.

**Mac:** open the link in Safari and choose File, then **Add to Dock**. In Chrome or Edge, click the install icon in the address bar.

**Windows and Android:** open the link in Chrome or Edge and use the install prompt.

Installing matters. On iOS, home screen apps get protected storage, so your data survives long gaps between uses. A plain browser tab can have its storage cleared after about a week of not being opened.

## Accounts and sync (optional)

Out of the box, Orbit is fully local. To let students sign in and sync across devices, the app owner does a one-time setup with a free [Supabase](https://supabase.com) project.

**Step 1. Profiles (needed for any sync).** In the Supabase SQL Editor, run:

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

**Step 2. Images (needed for Error Log screenshots to sync).** Images are too big to keep in the profile, so they go into a private storage bucket, one folder per student. Run:

```sql
insert into storage.buckets (id, name, public, file_size_limit, allowed_mime_types)
values ('orbit-media', 'orbit-media', false, 5242880,
        array['image/jpeg','image/png','image/webp','image/gif'])
on conflict (id) do nothing;

create policy "Read own images" on storage.objects for select to authenticated
  using (bucket_id = 'orbit-media' and (storage.foldername(name))[1] = auth.uid()::text);

create policy "Upload own images" on storage.objects for insert to authenticated
  with check (bucket_id = 'orbit-media' and (storage.foldername(name))[1] = auth.uid()::text);

create policy "Replace own images" on storage.objects for update to authenticated
  using (bucket_id = 'orbit-media' and (storage.foldername(name))[1] = auth.uid()::text);

create policy "Delete own images" on storage.objects for delete to authenticated
  using (bucket_id = 'orbit-media' and (storage.foldername(name))[1] = auth.uid()::text);
```

Until Step 2 is done, everything except images still syncs. Images stay on the device that added them, and the account panel says so.

**Step 3. Connect the app.**

1. Recommended: in **Authentication > Sign In / Providers > Email**, turn off **Confirm email**, so new students aren't stuck waiting for a confirmation link.
2. In **Project Settings > API** (or **API Keys**), copy the **Project URL** and the public key (anon or publishable).
3. Open `index.html`, find `SYNC_CONFIG`, and paste both values in.

Students then create an account from the database icon at the top right. Edits sync a couple of seconds after they're made, and each device pulls the latest when it opens or comes back to the foreground.

**How sync handles conflicts.** The Planner and the Error Log are merged separately, each by its own timestamp. Editing your planner on your phone and your error log on your laptop doesn't overwrite either. Within one section, the most recent edit wins, so two people sharing one account will still clash. The first time you sign in on a device that already has data, Orbit asks whether to load your account or upload what's on the device.

**Free plan limits.** At the time of writing, Supabase's free plan includes 1 GB of file storage, 5 GB of monthly transfer and a 500 MB database, and pauses projects after a week of inactivity. A typical screenshot is 100 to 300 KB after compression, so a class of 30 can log a few thousand image-based mistakes before storage becomes a concern.

## Privacy

Without an account, everything stays on the device: data in browser storage, images in IndexedDB, no analytics. With an account, data and images are stored in the app owner's Supabase project, and row-level security means each account can only ever read and write its own data. Backups (the database icon, then Export) include your images, so a backup file is a complete copy.

## Tech notes

Orbit is a single vanilla JavaScript file with no framework and no build step. Sync talks to Supabase's REST, auth and storage endpoints directly with `fetch`. KaTeX and the mhchem extension are bundled in `vendor/katex` (MIT licence) so maths renders offline.

```
index.html            the whole app: UI, logic, themes, sync
sw.js                 service worker: offline cache, network-first for the page
manifest.webmanifest  install metadata
icon-*.png            app icons
vendor/katex/         KaTeX, mhchem and fonts
```

Useful places in `index.html`: `letterFor` holds the grade boundaries, `defaultDaily` the daily checklist defaults, `defaultErrlog` the default curricula and topics, and `ERROR_TYPES` the error categories. To run it locally, use `python3 -m http.server 8000` and open http://localhost:8000.

## Updating

Bump `CACHE` at the top of `sw.js` whenever you ship, then push. The page loads network-first, so anyone online picks up the new version the next time they open the app. Offline, it falls back to the cached copy.

## License

See [LICENSE](LICENSE). KaTeX is distributed under its own MIT licence in `vendor/katex/LICENSE`.
