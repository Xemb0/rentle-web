# Rentle — Marketing Site

Standalone landing site for **Rentle**, the property &amp; rent management app
(owners + tenants). Intended for the subdomain **`rentle.imautotech.in`**.
Mirrors the `wxgamepad-web` / `watchparty-web` setup: static, Tailwind, Netlify,
marketing-only.

## Stack
- Plain static HTML + [Tailwind CDN](https://cdn.tailwindcss.com) — **no build step**.
- Screenshots are **bundled locally** in `images/screenshots/` (the App Store images),
  rendered with a lightbox — no external dependency.
- The iOS beta form posts to Rentle's own Supabase project (`cvhxmrwrilcxbumzkpen`).

## Files
```
index.html             Landing (hero, owner+tenant features, how-it-works, beta form, screenshots, download)
privacy-policy.html    Privacy Policy  → use as the store privacy URL
terms-of-service.html  Terms (incl. UPI-payment disclaimer + auto-renewable subscription terms)
contact.html           Contact page
images/rentle-logo.png App icon (logo / favicon / OG)
images/screenshots/    10 App Store screenshots
netlify.toml           Static publish config
```

## Local preview
```bash
cd rentle-web
python3 -m http.server 8080   # http://localhost:8080
```

## Deploy (Netlify) + subdomain
1. New Netlify site from this repo, publish dir `.`.
2. Add custom domain **`rentle.imautotech.in`**; at DNS add a **CNAME** `rentle` → `<site>.netlify.app`.
3. Set the store **Privacy URL** to `https://rentle.imautotech.in/privacy-policy.html`.

## ⚠️ Before go-live
- **Play Store** — confirm `com.rentle.app` listing is live (used in hero + download).
- **App Store** — the iOS chip points at the beta form; swap to the App Store URL once live.
- **iOS beta table** — the form needs a `testflight_signups` table in Rentle's Supabase
  project. Run this once in **Supabase → Rentle → SQL Editor**:
  ```sql
  create table if not exists public.testflight_signups (
    id uuid primary key default gen_random_uuid(),
    apple_id text not null unique,
    platform text not null default 'ios',
    source text default 'website',
    created_at timestamptz not null default now()
  );
  alter table public.testflight_signups enable row level security;
  create policy "anon can insert signups" on public.testflight_signups
    for insert to anon, authenticated with check (true);
  ```
- **imautotech card** — point the Rentle product card's `custom_page_path` at the subdomain
  once it's live (same as wx-gamepad).
