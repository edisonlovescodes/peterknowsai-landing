# Deploying peterknowsai.com on Cloudflare Pages

Since you own `peterknowsai.com` on Cloudflare, the deploy is 5 minutes.

---

## Step 1 — Push this folder to GitHub

```bash
cd /Users/e/Peter_Millions/landing
git init
git add .
git commit -m "Initial landing"
gh repo create peterknowsai-landing --public --source=. --push
```

(If you don't have `gh` CLI, just create a repo on github.com and push manually.)

---

## Step 2 — Create the Cloudflare Pages project

1. Open the [Cloudflare dashboard](https://dash.cloudflare.com).
2. Left sidebar → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
3. Pick the GitHub repo you just pushed.
4. Project setup:
   - **Framework preset**: None
   - **Build command**: *(leave blank — it's static HTML)*
   - **Build output directory**: `/`
   - **Root directory**: `/`
5. Click **Save and Deploy**. First build takes ~30 seconds.

You'll get a `.pages.dev` URL like `peterknowsai-landing.pages.dev`. The site is now live there.

---

## Step 3 — Connect peterknowsai.com

In your Pages project:

1. **Custom domains** tab → **Set up a custom domain** → enter `peterknowsai.com`.
2. Cloudflare auto-detects you own it and offers to set up the DNS automatically. Click **Activate domain**.
3. Repeat for `www.peterknowsai.com` (the `_redirects` file in this folder will 301 it to the apex).
4. Wait 30–60 seconds. Cloudflare auto-issues SSL.

That's it. `https://peterknowsai.com` now serves this landing page.

---

## Step 4 — Flip the three placeholders

Before you actually go live to buyers, search and replace these in `index.html`:

### A. Whop checkout
- Find: `plan_REPLACE_WITH_YOUR_PLAN_ID` (8 occurrences)
- Replace with: your Whop plan ID (looks like `plan_tQ10sSydvALHC`)
- Where to find it: Whop dashboard → your product → Pricing → copy plan ID

### B. Google Analytics 4 ID
- Find: `G-XXXXXXXXXX` (2 occurrences)
- Replace with: your GA4 Measurement ID
- Where to find it: analytics.google.com → Admin → Data Streams → web

### C. Real avatars in hero
- Find: `https://i.pravatar.cc/100?img=12` (and `33`, `47`, `68`)
- Replace with: real Twitter or Instagram profile image URLs of buyers / community members
- Or: drop 4 PNG files in this folder named `avatar-1.png` through `avatar-4.png` and reference them as `/avatar-1.png`

Push to GitHub. Cloudflare auto-rebuilds in ~30 seconds.

---

## Step 5 — Set up analytics

### Plausible (already wired in HTML)

1. plausible.io → Add Site → enter `peterknowsai.com`.
2. The script in `<head>` is already pointed at this domain. No code change.
3. Custom events you'll see immediately:
   - `Purchase Intent` (with `cta` prop = which CTA was clicked)
   - `Scroll 75` (read-through rate)
   - Auto-tracked outbound link clicks to whop.com

### Google Analytics 4

1. analytics.google.com → Admin → Create Property → Web data stream for `peterknowsai.com`.
2. Copy the Measurement ID (format: `G-XXXXXXXXXX`).
3. Replace in `index.html` (2 places).
4. **Mark `purchase_intent` as a conversion**: Admin → Events → flip the toggle on `purchase_intent`.
5. Conversion data shows up in Reports → Engagement → Conversions within 24 hours.

### Cloudflare's free Web Analytics (bonus)

Already free if you're on Cloudflare. Your dashboard → **Analytics & Logs** → **Web Analytics** → automatic for the domain.

---

## What you'll be able to measure

- **Total visits + sources**: Plausible dashboard, Cloudflare Web Analytics, GA4
- **Which CTA position drives the most clicks**: GA4 → events → `purchase_intent` → break down by `cta_label`. Or Plausible → events → `Purchase Intent` filtered by prop.
- **Read-through rate**: `scroll_75` event tells you who actually scrolls 75% before leaving
- **Whop conversion attribution**: UTM params on every Whop link tell Whop which CTA position drove each actual sale (Whop dashboard → Customers → filter by `utm_content`)

---

## Files in this folder

| File | Purpose |
|---|---|
| `index.html` | The landing page |
| `styles.css` | Design system (1:1 with frontrunning.ai structure) |
| `_headers` | Cloudflare Pages security + cache headers |
| `_redirects` | Redirect www → apex |
| `vercel.json` | Vercel config (if you ever switch off Cloudflare) |
| `CNAME` | GitHub Pages domain config (if you switch hosts) |
| `robots.txt` | Search engine rules |
| `sitemap.xml` | Search index map |
| `DEPLOY.md` | This file |
