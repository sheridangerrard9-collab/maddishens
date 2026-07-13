[README.md](https://github.com/user-attachments/files/29953408/README.md)
# Maddi's Hens 🥂

A luxury, editorial-style event website built for a private Hens Day — event
information plus a beautiful RSVP form, in the spirit of a high-end bridal
stationery suite rather than a novelty party site.

**Stack:** Next.js 15 (App Router) · TypeScript · Tailwind CSS · Framer Motion · Radix UI

---

## 1. Project structure

```
maddis-hens/
├── app/
│   ├── api/rsvp/route.ts    # RSVP submission endpoint (Sheets/Supabase/Airtable)
│   ├── globals.css          # design tokens, base styles, accessibility defaults
│   ├── layout.tsx           # fonts, SEO metadata, skip link
│   └── page.tsx             # assembles all sections
├── components/
│   ├── Nav.tsx               About the day, event details, itinerary,
│   ├── Hero.tsx               what to bring, FAQ, RSVP form + section,
│   ├── AboutDay.tsx           mood board, footer, countdown, save-to-
│   ├── EventDetails.tsx       calendar, scroll-progress "signature" element,
│   ├── Itinerary.tsx          and the hand-drawn line-art SVG library.
│   ├── WhatToBring.tsx
│   ├── FAQ.tsx
│   ├── RSVPForm.tsx
│   ├── RSVPSection.tsx
│   ├── MoodBoard.tsx
│   ├── Footer.tsx
│   ├── Countdown.tsx
│   ├── SaveToCalendar.tsx
│   ├── ChampagneProgress.tsx
│   ├── FadeIn.tsx
│   └── LineArt.tsx
├── lib/
│   ├── event.ts              # single source of truth for date/time/location
│   └── utils.ts
├── middleware.ts             # optional password gate for the whole site
├── tailwind.config.ts        # colour palette, type scale, animations
├── vercel.json
└── .env.example
```

## 2. Getting started locally

```bash
npm install
cp .env.example .env.local   # then fill in the fields you need (see below)
npm run dev
```

Open http://localhost:3000.

With no environment variables set, RSVP submissions are simply logged to
the server console — handy for testing the form before wiring up storage.

## 3. Editing event content

Almost everything guest-facing lives directly in the relevant component
under `components/`, written in plain English so it's easy to edit even
without a dev background:

- **Date / time / location / dress code** → `lib/event.ts`
- **Hero headline & subtitle** → `components/Hero.tsx`
- **Itinerary times** → `components/Itinerary.tsx`
- **FAQ questions** → `components/FAQ.tsx`
- **Mood board images** → `components/MoodBoard.tsx` (swap the `src` URLs)
- **Colours / fonts** → `tailwind.config.ts`

## 4. Connecting an RSVP storage backend

Set `RSVP_PROVIDER` in your environment to `sheets`, `supabase`, `airtable`,
or leave it as `console` for local testing. The API route at
`app/api/rsvp/route.ts` reads this variable and routes submissions
accordingly — the RSVP form itself never needs to change.

### Option A — Google Sheets (simplest for a non-technical host)

1. Create a new Google Sheet with a header row matching the RSVP fields
   (firstName, lastName, email, mobile, attending, dietary, allergies,
   songRequest, emergencyContact, notes, submittedAt).
2. Open **Extensions → Apps Script** and paste a small script that reads
   `e.postData.contents` as JSON and appends a row with `sheet.appendRow(...)`.
3. Deploy it as a **Web App** (Execute as: Me, Who has access: Anyone).
4. Copy the deployment URL into `GOOGLE_SHEETS_WEBHOOK_URL`.
5. Set `RSVP_PROVIDER=sheets`.

### Option B — Supabase

1. Create a project at [supabase.com](https://supabase.com).
2. Create a table called `rsvps` with columns matching the RSVP fields
   (text columns are fine for all of them, plus a `submittedAt` timestamp).
3. Copy your project URL and **service role key** (Settings → API) into
   `NEXT_PUBLIC_SUPABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY`.
4. Set `RSVP_PROVIDER=supabase`.

### Option C — Airtable

1. Create a base with a table called `RSVPs`, with a column per RSVP field.
2. Generate a personal access token with `data.records:write` scope on
   that base, and copy your Base ID (from the base's API docs page).
3. Fill in `AIRTABLE_API_KEY`, `AIRTABLE_BASE_ID`, and optionally
   `AIRTABLE_TABLE_NAME` (defaults to `RSVPs`).
4. Set `RSVP_PROVIDER=airtable`.

## 5. Optional password gate

Set `SITE_PASSWORD` to require a password (via browser basic-auth) before
guests can view the site. Leave it unset to keep the site public — anyone
with the link can view it, which is fine for a private, unlisted URL shared
directly with guests. The site also sets `robots: noindex` so it won't
appear in search results either way.

## 6. Deploying to GitHub + Vercel

```bash
git init
git add .
git commit -m "Initial commit: Maddi's Hens website"
git branch -M main
git remote add origin https://github.com/<your-username>/maddis-hens.git
git push -u origin main
```

Then on [vercel.com](https://vercel.com):

1. **New Project → Import** your GitHub repo.
2. Vercel auto-detects Next.js — no build settings need changing.
3. Add your environment variables from `.env.local` under
   **Settings → Environment Variables**.
4. Deploy. Your site will be live at `<project-name>.vercel.app` (add a
   custom domain under **Settings → Domains** if you'd like one).

## 7. Accessibility & performance notes

- Semantic landmarks (`<nav>`, `<main>`, `<footer>`), a skip-to-content
  link, and visible focus rings throughout.
- All form fields have associated `<label>`s, `aria-invalid`, and
  `aria-describedby` error messaging; the attendance choice is a proper
  `fieldset`/`radiogroup`.
- Decorative illustrations are marked `aria-hidden`; the countdown timer
  exposes a single accessible label rather than four noisy live regions.
- Respects `prefers-reduced-motion` — all animation is disabled for users
  who have that preference set.
- Colour palette was chosen to keep burgundy-on-cream body text comfortably
  above WCAG AA contrast for normal text.

## 8. Replacing placeholder imagery

The "About the Day" and "What We're Wearing" sections use royalty-free
Unsplash placeholder photography so the site looks complete out of the box.
Swap the `src` attributes in `components/AboutDay.tsx` and
`components/MoodBoard.tsx` for real photos whenever you're ready — any
publicly reachable image URL works, or upload to Vercel Blob storage and
point to that URL instead.
