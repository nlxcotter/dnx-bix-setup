# Training Module: Auto-submit your DashNex site to Bing with IndexNow

> **Status:** living. **Audience:** anyone running a coded DashNex Business site who wants new/changed pages submitted to Bing automatically instead of pasting URLs by hand. **First built + verified live on TCS, 2026-06-29 (HTTP 202 accepted).**

## What IndexNow is (and isn't)
**IndexNow** is a free, official protocol that lets your site **ping search engines the instant a page is created or changed**, instead of waiting for a crawl or submitting URLs manually.
- **One ping reaches Bing, Yahoo, Yandex, and Seznam** at once (they share the IndexNow network).
- **Google does NOT use IndexNow.** Google runs its own crawler; keep using your sitemap + manual GSC "Request indexing" for Google. So IndexNow automates *Bing and friends*, not Google. Be honest about that with clients.
- It's **set-and-forget** once wired.

## The three parts
1. **A verification key file** hosted at your domain root, so the engines trust your pings.
2. **A submit step** that POSTs your URL list to the IndexNow API.
3. **A trigger** that fires the submit when content changes.

## Setup (coded DashNex app)

**1. Generate a key.** Any hex string, 8–128 chars:
```
openssl rand -hex 16        # e.g. a17dd5a89ea0ab06481bfcd0f9c45571
```

**2. Host the key file.** Drop a plain-text file in `public/` named `<KEY>.txt` containing exactly the key. DashNex serves `public/` at the site root, so it becomes `https://yourdomain.com/<KEY>.txt`. Verify after deploy:
```
curl https://yourdomain.com/<KEY>.txt   # must return the key, HTTP 200
```

**3. Build an owner-only submit endpoint** (`src/app/api/admin/indexnow/route.ts`). It builds the URL list the same way `sitemap.ts` does (static pages + SERVICES + published POSTS via `absoluteUrl`), gates on `getUser` + `hasRole('admin'|'owner')` (hard 403 to the public), then POSTs to IndexNow:
```
POST https://api.indexnow.org/indexnow
Content-Type: application/json; charset=utf-8
{ "host": "yourdomain.com", "key": "<KEY>",
  "keyLocation": "https://yourdomain.com/<KEY>.txt",
  "urlList": ["https://yourdomain.com/", ...] }
```
**A 200 or 202 response = accepted.** (TCS returned 202 on the first run.)

**4. Submit.** Visit the endpoint while logged in as owner, or POST the payload directly with curl (the key file just has to be live).

## The right trigger: ping-on-DEPLOY, not a daily cron
For a **code/deploy-driven site** (services + blog defined in code), content only changes when you deploy. So the correct automation is to **fire the submit right after each content deploy**, not a time-based cron — a daily cron would just re-submit unchanged URLs for nothing. Make "ping IndexNow" the last step of the deploy routine.

> **DashNex cron note:** DashNex *does* run Cloudflare cron triggers, but the dispatch lives in the **generated** `src/dashnex/worker.ts` (keyed per-module, and that file is **do-not-edit**), so adding an app-level scheduled job isn't trivial. Ping-on-deploy sidesteps that entirely and is the better fit here anyway.

## Gotchas for the Errors & Gotchas log
- **Google ≠ IndexNow.** Don't promise Google coverage from this. Sitemap + GSC handle Google.
- **Key file must be exact:** at the root, served 200, containing *only* the key. A 404 or wrong contents = pings silently rejected.
- **The key is NOT a secret** (it's publicly hosted by design) — fine to keep as a constant in code.
- **Right cadence = on change (deploy), not on a clock.** Re-submitting unchanged URLs daily wastes the engines' goodwill and does nothing.
- **202 is success**, not an error. IndexNow returns 200/202 on accept; it does not crawl instantly, it queues your URLs.

## Reusable rule of thumb
Host the key file in `public/`, build one owner-gated submit endpoint that mirrors your sitemap, and fire it on deploy. Bing (+ Yahoo/Yandex/Seznam) then learn about new pages within minutes, hands-off, for free.
