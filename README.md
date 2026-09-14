# Semester Tracker

A one-page website that tracks every assignment in every class you're taking. Assignments are grouped by due date, and you can check them off, filter by class, and spot late work at a glance. Your checkmarks are saved online, so your phone and laptop stay in sync.

## Built to be set up with an AI assistant

This repo is a blank template. It's designed to be set up with help from an AI coding assistant, such as Claude Code, Cursor, or GitHub Copilot.

- **You** do the parts that need your own accounts: copying this repo on GitHub, creating a free Supabase database, gathering your class schedules, and turning on GitHub Pages. Step-by-step instructions are below.
- **Your assistant** does the rest: reading your schedules, building your class and assignment lists, filling in the config, and updating the list when your classes change. Everything it needs is in [Reference for AI assistants](#reference-for-ai-assistants) at the bottom of this file.

You don't need coding experience. You do need a GitHub account, a free Supabase account, and an AI assistant that can edit files in a folder on your computer.

## Setup

1. [Get your own copy on GitHub](#step-1-get-your-own-copy-on-github)
2. [Create your Supabase database](#step-2-create-your-supabase-database)
3. [Gather your class schedules](#step-3-gather-your-class-schedules)
4. [Have your assistant build your list](#step-4-have-your-assistant-build-your-list)
5. [Publish with GitHub Pages](#step-5-publish-with-github-pages)

### Step 1: Get your own copy on GitHub

1. Sign in to GitHub and open this repo.
2. Click **Fork** at the top right. Give your copy a name, such as `fall-2026-tracker`, and click **Create fork**.
3. Download (clone) your fork to your computer. On your fork's page, click the green **Code** button, copy the URL, and run `git clone <that URL>`. You can also use GitHub Desktop: **File → Clone repository**.
4. Open the downloaded folder in your AI assistant.

### Step 2: Create your Supabase database

Supabase stores your assignment list and checkmarks.

1. Go to [supabase.com](https://supabase.com), sign in (signing in with GitHub is easiest), and click **New project**.
2. Choose any project name and pick the region closest to you. Set a database password and save it somewhere; the tracker doesn't use it, but Supabase requires one. Click **Create new project** and wait a minute or two while it sets up.
3. In the left sidebar, open **SQL Editor**. Paste in the following and click **Run**:

   ```sql
   create table public.tracker (
     id text primary key,
     data jsonb not null default '[]'::jsonb,
     updated_at timestamptz default now()
   );
   insert into public.tracker (id) values ('main');

   alter table public.tracker enable row level security;
   grant select, update on public.tracker to anon;
   create policy "read tracker"   on public.tracker for select to anon using (true);
   create policy "update tracker" on public.tracker for update to anon using (true) with check (true);
   ```

   You should see "Success. No rows returned".
4. Check that it worked: open **Table Editor** in the sidebar. You should see a `tracker` table with one row whose `id` is `main`.
5. Copy these two values and keep them for Step 4:
   - **Project URL**, which looks like `https://abcdefghijkl.supabase.co`. It's under **Project Settings → Data API**, or click **Connect** at the top of the dashboard.
   - **Publishable key**, which starts with `sb_publishable_`. It's under **Project Settings → API Keys**. Older projects may show an **anon public** key instead, which also works.

> **Never use the secret key or the `service_role` key.** The key you use ends up in a public file, and those keys give anyone full control of your Supabase project.

Supabase sometimes rearranges its dashboard. If a menu name here doesn't match, use the dashboard's search or ask your assistant.

### Step 3: Gather your class schedules

Put your schedules in the `HW Schedules` folder, with one or more files per class. Your assistant reads everything in this folder.

- **Copy and paste into `.txt` files whenever you can.** For example, open a class's Grades or Assignments page in Canvas, select everything (Ctrl+A or Cmd+A), copy it, and paste it into `HW Schedules/BIO_100.txt`. Text is the most reliable format for your assistant.
- **Take screenshots whenever copying and pasting doesn't work.** Examples: pages that won't let you select text, schedules that are images, or pasted text that comes out scrambled. Save them as `BIO_100_1.png`, `BIO_100_2.png`, and so on, and make sure the due dates are readable.
- **Add syllabus or schedule PDFs and spreadsheets** as they are.
- **Add a `notes.txt` file** for anything the other files don't say: the first and last day of the semester, holidays, class times, or assignments announced in class.

Files you put in `HW Schedules` stay on your computer and aren't uploaded to GitHub. See [Privacy](#privacy) for why.

### Step 4: Have your assistant build your list

Copy this into your AI assistant and fill in the blanks:

```text
Read README.md in this repo, including "Reference for AI assistants". Then set
up my semester tracker from the files in "HW Schedules".
- Supabase Project URL: ____
- Supabase publishable key: ____
- Unlock password: ____
- My semester runs from ____ to ____.
Ask me about anything you can't read or that looks wrong. Once I approve the
list, commit and push.
```

Choose an unlock password you don't use anywhere else. Anyone who looks at your repo can see it (see [Privacy](#privacy)).

Your assistant fills in the config, adds your classes, and builds your assignment list. It then shows you a summary to check before it commits and pushes.

### Step 5: Publish with GitHub Pages

GitHub Pages hosts your tracker for free.

1. On your fork's GitHub page, click **Settings**, then **Pages** in the left sidebar.
2. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
3. Set **Branch** to `main` and the folder to `/ (root)`, then click **Save**.
4. Wait a minute or two, then refresh the Pages settings page. A banner at the top shows your site's address, which looks like `https://your-username.github.io/fall-2026-tracker/`.
5. Open that address and enter your unlock password. Your assignments should appear. Open the address on each device you use and bookmark it.

After each push, the site updates within a few minutes. If it doesn't, open the **Actions** tab on your fork. If GitHub asks you to enable workflows there, enable them.

## Using the tracker

- Check off assignments as you finish them. Changes save automatically.
- **+ Add task** adds something that isn't in your schedules.
- **Hide completed**, **Condensed view**, and **Hide daily items** change what you see on that device. Condensed view hides past days where everything is done. Daily items are small recurring things like attendance.
- **Reset to the original imported list** replaces your whole list with the imported assignments and clears every checkmark. You'll rarely want it.

**When a class adds or changes assignments,** put the updated schedule in `HW Schedules`, keeping the old file too. Then ask your assistant to update the live list without losing your checkmarks. When it's done, reload the tracker on every device before checking anything off.

## Privacy

Read this before putting anything personal in your tracker.

- **Your fork is public, and so is everything you push.** That includes the Supabase URL, key, and unlock password in `index.html`. The password keeps out casual visitors, but anyone who reads the file can find it.
- **Anyone who has your key can read or change your list.** That's fine for homework due dates. Don't store anything sensitive in the tracker.
- **Your schedule files stay private by default.** `.gitignore` keeps everything in `HW Schedules` except its README off GitHub, because pasted pages often include your name and grades. If your assistant only works from GitHub (for example, a cloud-based agent), it can't see those files. Either give it the files another way, or delete the `HW Schedules/*` lines from `.gitignore` so they get pushed. If you push them, first remove your name, grades, and anything else you don't want public.

## Troubleshooting

| What you see | Likely cause |
| --- | --- |
| "Add your Supabase URL and key to the config" | `SUPABASE_URL` or `SUPABASE_KEY` in `index.html` is still blank. |
| "Couldn't load — check config" | The URL or key is wrong, the SQL in Step 2 didn't run, or the `main` row is missing. |
| Checkmarks disappear when you reload | The `update tracker` policy or the `grant` line from Step 2 is missing. |
| Your imported assignments don't appear | The site only loads imported assignments while your saved list is empty. If you added a task first, ask your assistant to merge your assignments into the live list. |
| Your GitHub Pages address shows a 404 | Pages is still deploying, so wait a few minutes. Otherwise, check that Pages is set to `main` and `/ (root)`. |

---

## Reference for AI assistants

This section is for an AI coding assistant helping someone set up or maintain their tracker. Read all of it before changing anything.

### How it works

The whole app is `index.html`, containing HTML, CSS, and JavaScript. There's no build step, and the only dependency is `supabase-js`, loaded from a CDN. These parts of the file are meant to be edited:

1. **Config**: the `<script>` block near the top of `<body>`, with `SUPABASE_URL`, `SUPABASE_KEY`, and `UNLOCK_PASSWORD`. `SUPABASE_KEY` must be the publishable or anon key, never a secret or `service_role` key, because the file is public.
2. **`CLASSES`**: the courses.
3. **`SEED`**: the imported assignments.
4. Optionally, the `<title>` and the header subtitle (`Grouped by due date`), for example to add the semester name.

Don't change the rest of the app code unless the user asks.

The user's live list is stored in Supabase, not in `index.html`. It's in table `public.tracker`, row `id = 'main'`, column `data`, which holds a JSON array of items. This has consequences:

- On load, the page reads `data`. **If `data` is empty, the page writes `SEED` into it**, giving each item `id: "seed-<index>"` and `done: false`. Otherwise it uses `data` and ignores `SEED`.
- **Once `data` holds anything, editing `SEED` changes nothing the user sees.** The one exception is the Reset button, which replaces `data` with `SEED` and clears every checkmark.
- Every save replaces the entire `data` array, and the last write wins. A tab opened before you changed the database will overwrite your change the next time the user checks something off in it. After any direct database edit, tell the user to reload the tracker on every device before using it.

### `CLASSES`

```js
const CLASSES = {
  "BIO 100":{key:"bio100",accent:"var(--c-1)",label:"BIO 100"},
  "Other":{key:"other",accent:"var(--c-other)",label:"Other"},
};
```

- The property name is the class name. Items refer to it exactly in their `cls` field. Use the course's own code, such as `"BIO 100"`.
- `key` is a short lowercase slug. `label` is the text shown on filter chips and item tags, and is usually the same as the name.
- `accent` is a CSS color, normally one of `--c-1` through `--c-7` from `:root` at the top of the stylesheet. Give each class its own color. For more than seven classes, add `--c-8` and so on to `:root`.
- Order matters. It sets the order of the filter chips and of classes within each day, and the first class is the default when the user adds a task in the app. Keep `"Other"` last. It's the fallback for any `cls` that isn't in `CLASSES`.

### Items

`SEED` entries and the items stored in Supabase have the same shape:

| Field | Type | Notes |
| --- | --- | --- |
| `date` | `"YYYY-MM-DD"` | Due date. Required. A local date with no time zone. |
| `time` | string | For display only, such as `"11:59pm"`, `"3:05pm"`, or `"7:00am–10:00am"`. Use `""` when there's no time. |
| `cls` | string | Must exactly match a name in `CLASSES`. |
| `title` | string | Plain text. The app escapes it. |
| `busy` | boolean, optional | `true` for daily or low-stakes items, such as attendance, warm-ups, and in-class participation. "Hide daily items" hides these. |
| `tbd` | boolean, optional | With `time: ""`, shows "time TBD". |
| `id` | string | **Stored items only.** Must be unique. Seeded items get `seed-<index>`, and items added in the app get `u-<Date.now()>-<random>`. Use the `u-` pattern for items you add. |
| `done` | boolean | **Stored items only.** The user's checkmark. Never change it unless the user asks. |

Within a day, unfinished items come first, then items sort by class order, then by title. Time doesn't affect the order.

### Task: first-time setup

1. Read every file in `HW Schedules`: text, PDFs, spreadsheets, screenshots, and `notes.txt`. Work out which classes there are and which files belong to each.
2. Fill in the config with the values the user gives you.
3. Build `CLASSES`, with one entry per course.
4. Build `SEED`, sorted by date:
   - Resolve every date to `YYYY-MM-DD` in the right year. Sources often leave out the year, and spreadsheets can store dates oddly. Check that dates fall within the semester and match the class's weekday pattern and holidays.
   - Keep titles short but specific. For homework, include section and problem numbers when the source gives them, such as `"2.1 Linear Equations — 1, 13, 15"`.
   - Set `busy:true` on attendance, daily warm-ups, and other small recurring items.
   - For items with no due date, use the last day of classes with `time:""` and `tbd:true`.
   - When sources disagree, for example a syllabus and the LMS, prefer the LMS, and tell the user which you chose.
   - Add one entry per real assignment. Skip headings, grade totals, and weighting tables.
5. Before committing, show the user a summary: item counts per class, anything you couldn't read, and every date or detail you had to guess. Ask about anything uncertain, and never invent assignments.
6. Check your work: the `<script>` blocks parse, every `cls` is in `CLASSES`, and every `date` is a real date.
7. Commit and push once the user approves. The first load with an empty `data` row writes `SEED` to Supabase.

If `data` is already non-empty when setup finishes (for example, the user added a task before importing), `SEED` won't load. Use the next task to merge it in.

### Task: updating the live list

Use this whenever assignments change after the tracker is in use. The goal is to update the live list **without losing any checkmarks**.

1. Compare the new schedule with the previous file for that class, if there is one, and with the live `data`. Work out what's new, what's gone, and what changed (title, date, or time).
2. Save a backup of the live row to a local file outside the repo.
3. Build the new `data` array:
   - Keep every existing item's `id` and `done` exactly as they are.
   - Update renamed or rescheduled items in place. Don't delete and re-add them.
   - Add new items with fresh `u-` ids and `done: false`.
   - Remove an item that's no longer in the schedule only if it's unchecked. Ask before removing a checked item.
   - Leave items the user added by hand alone unless they ask. These have `u-` ids and don't match any source.
4. Re-read the row just before writing. If `updated_at` changed since your backup, the user edited the list meanwhile, so start over from step 2.
5. Write the new array, then read it back and confirm it matches, including the number of `done` items.
6. Make the same changes in `SEED` so a Reset matches the current schedule. Commit and push.
7. Tell the user what changed, and remind them to reload the tracker on every device.

Read and write the row with Supabase's REST API, using the URL and key from the config:

```sh
# Read
curl "$SUPABASE_URL/rest/v1/tracker?id=eq.main&select=data,updated_at" \
  -H "apikey: $SUPABASE_KEY" -H "Authorization: Bearer $SUPABASE_KEY"

# Write (replaces the whole array)
curl -X PATCH "$SUPABASE_URL/rest/v1/tracker?id=eq.main" \
  -H "apikey: $SUPABASE_KEY" -H "Authorization: Bearer $SUPABASE_KEY" \
  -H "Content-Type: application/json" -H "Prefer: return=minimal" \
  --data @new_data.json
```

`new_data.json` looks like `{"data": [ ...items ], "updated_at": "<ISO 8601 timestamp>"}`. If row level security blocks a write, the request can still report success without changing anything, so always read the row back afterwards.

### Task: adding a class mid-semester

Add the class to `CLASSES`, with a new color if needed. Then follow "Updating the live list" to add its assignments. Commit and push, and the new class appears once the site redeploys.

### Reading schedule sources

- **Canvas Grades page, pasted as text:** Assignments sit under date headings such as `Monday, September 14, 2026`. A heading may end with an uppercase note, such as `NO CLASS MIDTERM`, which is worth adding to that day's attendance title. Each assignment is a title line, an assignment group line, a due line such as `Sep 14 by 3:05pm` followed by submission details, and a score line. The group totals and weighting table at the end aren't assignments.
- **PDFs and spreadsheets:** Check whether listed dates are due dates or class meeting dates, and whether recurring items follow a pattern.
- **Screenshots:** Read them carefully, and list anything you can't make out for the user to check.
- **A newer file for a class that already has one:** Treat it as an update and use "Updating the live list".
- **Anything ambiguous:** Ask the user rather than guessing.
