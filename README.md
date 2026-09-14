# Semester Tracker (template)

A single-page assignment tracker: every assignment grouped by due date, with checkboxes, class filters, late flags, and a condensed view. The list is stored in one Supabase row, so every device that unlocks the page sees the same checkmarks.

This is a blank copy. It has no classes, no assignments, and no credentials.

## Setup

### 1. Create the Supabase table

Make a free Supabase project, open the SQL editor, and run:

```sql
create table tracker (
  id text primary key,
  data jsonb not null default '[]'::jsonb,
  updated_at timestamptz default now()
);
insert into tracker (id) values ('main');

alter table tracker enable row level security;
create policy "read tracker"   on tracker for select to anon using (true);
create policy "update tracker" on tracker for update to anon using (true) with check (true);
```

The page only ever reads and updates the row with `id = 'main'`. It never inserts one, so the `insert` line is required.

### 2. Fill in the config

At the top of the `<body>` in `index.html`, set:

- `SUPABASE_URL`: Project Settings → API → Project URL
- `SUPABASE_KEY`: the publishable (anon) key from the same page
- `UNLOCK_PASSWORD`: whatever you want to type on each new device

### 3. Add your classes

Add one entry per course to `CLASSES`, giving each an accent from the `--c-1` … `--c-7` palette at the top of the stylesheet:

```js
"BIO 100":{key:"bio100",accent:"var(--c-1)",label:"BIO 100"},
```

### 4. Add assignments

Add one entry per assignment to `SEED`. `cls` must match a name in `CLASSES`.

```js
{date:"2026-09-02",time:"11:59pm",cls:"BIO 100",title:"Homework 1"},
{date:"2026-09-04",time:"5:00pm",cls:"BIO 100",title:"In-class quiz",busy:true},
{date:"2026-12-11",time:"",tbd:true,cls:"BIO 100",title:"Final project"},
```

- `busy:true` marks a daily or low-stakes item. "Hide daily items" hides these.
- `tbd:true` with an empty `time` shows "time TBD".

`SEED` is written to Supabase only when the row is empty (first load), and again when you click "Reset to the original imported list". **After the first load, editing `SEED` does not change the live list.** Add later assignments with the "+ Add task" button, or edit the Supabase row directly.

Schedule exports (Canvas grade pages, syllabi, PDFs) can go in a `HW Schedules/` folder, which is gitignored.

### 5. Host it

`index.html` is a static file. Put it on any static host (GitHub Pages, Netlify, and so on), or open it locally.

## A note on privacy

The password check runs in the browser, and the Supabase key sits in the page source. Anyone who opens the source can read and change your list. That's fine for a personal to-do list, but don't put anything sensitive in it.
