# Deploying Morsel (off the tunnel → stable)

The app is a single static file (`index.html`) plus header config. It needs
**cross-origin isolation headers** (`COOP: same-origin`, `COEP: require-corp`)
so the in-browser cutout can multi-thread on iOS. Both host configs below are
already in this folder: `_headers` (Netlify) and `vercel.json` (Vercel).

Data is local-first (IndexedDB), so a **stable URL = your history persists**.
The OpenRouter/Claude key lives in the browser (localStorage) until we add the
serverless proxy (phase 2).

---

## Option A — Netlify Drop (fastest, no accounts, ~60s)
1. Go to https://app.netlify.com/drop
2. Drag this `food-scrapbook` folder onto the page.
3. Get a permanent `https://<name>.netlify.app` URL. Open on phone → Add to Home Screen.
   `_headers` makes the fast cutout work automatically.

## Option B — GitHub → Vercel (the real stack, ~10 min)
```sh
cd "C:/Claude Code/food-scrapbook"
git init && git add index.html vercel.json _headers DEPLOY.md && git commit -m "Morsel app"
# create an empty repo on github.com, then:
git remote add origin https://github.com/<you>/morsel.git
git branch -M main && git push -u origin main
```
Then on vercel.com → New Project → import the repo → Deploy. `vercel.json`
applies the isolation headers. Every `git push` auto-deploys.

## Phase 2 (later) — hide the key + sync + social
- **Vercel serverless function** (`/api/analyze`) holds the OpenRouter key server-side;
  the browser calls our own endpoint instead of OpenRouter directly.
- **Supabase** (free Postgres + auth + storage) for cross-device sync of morsels
  and the social food-trails layer (friends' maps).

---

`server.cjs` is only for the local Cloudflare-tunnel preview — not needed on
Netlify/Vercel (they apply the headers via the config files).
