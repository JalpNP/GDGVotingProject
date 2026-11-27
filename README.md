# MLH Presenter Vote

A small voting kiosk for GDG Sudbury events. A tablet sits at the entrance of the hall, and attendees tap the MLH presenter they liked most on their way back in. The vote is saved, a thank-you screen shows for a few seconds, and the kiosk resets for the next person.

Organizers sign in to a separate results page that shows a live leaderboard, ready to put up on the projector for the final reveal.

Built with React, Vite and Supabase.

## Pages

- `/` is the kiosk. It's public and needs no login.
- `/results` is the live leaderboard. Organizers only; it redirects to `/login` if you're not signed in.
- `/login` is the organizer sign-in page.

## Setup

You'll need Node 20.19 or newer and a free Supabase account.

### 1. Create the Supabase project

Create a new project at [supabase.com](https://supabase.com). Any region close to Sudbury is fine.

### 2. Create the tables

Open the **SQL Editor** in the Supabase dashboard and run these two files, in this order:

1. `supabase/schema.sql` creates the tables, the `vote_counts` view, the security rules, and turns on realtime.
2. `supabase/seed.sql` adds four placeholder presenters.

To use your real presenters, edit them in the **Table Editor** under `presenters`:

- `display_order` controls the order of the cards on the kiosk.
- `photo_url` can be any public image URL. A public file in Supabase Storage works well. Leave it empty and the card shows the presenter's initials instead.

### 3. Lock down sign-ups and create an organizer account

This step matters. Anyone with a login can see the votes and open or close voting, so only organizers should have accounts.

1. Go to **Authentication > Sign In / Providers** and turn off **Allow new users to sign up**.
2. Go to **Authentication > Users > Add user > Create new user**. Enter the organizer's email and a password, and tick **Auto Confirm User**.

Repeat the second part for each organizer.

### 4. Add your keys

```
VITE_SUPABASE_URL=https://your-project-ref.supabase.co
VITE_SUPABASE_ANON_KEY=sb_publishable_...
```

- The project URL is under the **Connect** button at the top of the dashboard.
- For the key, use the **Publishable key** from **Project Settings > API Keys**. It's Supabase's newer name for the anon key. On older projects, the legacy `anon` key works too.
- Never use the **Secret key** here. It bypasses all the security rules, and anything in a `VITE_` variable ends up in the browser.

The publishable key is meant to be public. It ships in the browser, and the database security rules decide what it can do: read presenters and settings, and add votes. It can't read the votes back.

### 5. Run it

```
npm install
npm run dev
```

## Project layout

```
supabase/          SQL to run in the Supabase SQL Editor
src/lib/           Every Supabase call lives here
src/hooks/         Data loading, realtime subscriptions and kiosk behaviour
src/components/    Small UI pieces, each with its own CSS module
src/pages/         The kiosk, login and results screens
src/styles/        Theme colours and global styles
public/            Static files, including the GDG Sudbury logo
```

To use your own logo, replace `public/gdg-logo.svg` with a file of the same name.
