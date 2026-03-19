<div align="center">

<img src="https://img.shields.io/badge/SkyluxMovies-v1.0.0-d4922a?style=for-the-badge&logo=film&logoColor=white" alt="SkyluxMovies v1.0.0" />

# 🎬 SkyluxMovies

### Stream and download free HD movies — no account, no watermark, no hassle.

[![Node.js](https://img.shields.io/badge/Node.js-18.x-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org)
[![Powered by YTS](https://img.shields.io/badge/Powered%20by-YTS-f0b84a?style=flat-square)](https://yts.bz)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=flat-square)](https://github.com/Advay254/SkyluxMovies/pulls)
[![Deploy on Render](https://img.shields.io/badge/Deploy%20on-Render-46E3B7?style=flat-square&logo=render&logoColor=white)](https://render.com)

<br/>

> Search a movie. Pick your quality. Download in seconds. No sign-up, ever.

<br/>

![SkyluxMovies Demo](https://skyluxmovies.onrender.com/og-image.png)

</div>

---

## ✨ Features

- 🎬 **720p · 1080p · 4K** — quality picker on every movie, sourced from YTS
- 🔥 **Rich catalogue homepage** — Trending, Most Watched, New Releases, Box Office, Top Rated, Most Downloaded, Most Anticipated + genre rows
- 🗂️ **Genre tabs** — tap any genre for a full-screen filtered view
- 🔍 **Live search** — instant results dropdown as you type
- 🎞️ **Movie detail page** — backdrop blur, cast circles, IMDb rating ring, trailer link, quality picker, similar movies grid
- ⚡ **Smart rate limiting** — 3-tier system: image proxy unrestricted, search 30/min, API 600/15min
- 📱 **Full PWA** — installable, Background Sync, Periodic Sync, Push Notifications handler, auto-updates on deploy
- 📲 **TWA / APK** — native Android app via Digital Asset Links verification (no URL bar)
- 📥 **In-browser install banner** — shows only in browser, hidden automatically in PWA/TWA
- 🗺️ **Production SEO** — JSON-LD knowledge graph, FAQPage schema, dynamic Open Graph, XML sitemap
- 📝 **SEO Blog** — articles targeting high-traffic movie download keywords
- 🚀 **Deploy anywhere** — Render, Railway, Fly.io, any Node.js host

---

## 🗂️ Project Structure

```
SkyluxMovies/
├── index.js          # Launcher — fetches and starts the core engine
├── package.json      # Launcher dependencies only
├── .env.example      # Environment variable reference
└── .gitignore
```

> The core application is loaded securely at runtime. This keeps the source lean and the deployment simple.

---

## 🚀 Quick Start

### 1. Fork or clone the repo

```bash
git clone https://github.com/Advay254/SkyluxMovies.git
cd SkyluxMovies
```

### 2. Install dependencies

```bash
npm install
```

### 3. Set up environment variables

```bash
cp .env.example .env
```

Edit `.env` with your values:

```env
SITE_URL=http://localhost:3000
TRAKT_CLIENT_ID=your_trakt_client_id_here
YTS_BASE=https://movies-api.accel.li/api/v2
YTS_FALLBACK=https://yts.bz/api/v2
```

### 4. Run

```bash
npm start
```

Visit `http://localhost:3000` — you're live. 🎉

---

## 🔑 Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `SITE_URL` | ✅ **Yes** | Full production domain — used for sitemap and canonical URLs. Example: `https://yoursite.onrender.com` |
| `TRAKT_CLIENT_ID` | Recommended | Powers trending rows via Trakt.tv API. Get one free at [trakt.tv/oauth/applications](https://trakt.tv/oauth/applications) |
| `API_KEY` | Optional | If set, external API requests must include this key via `?key=` or `x-api-key` header |
| `YTS_BASE` | Optional | Primary YTS API URL. Update here if YTS changes domains — no code changes needed |
| `YTS_FALLBACK` | Optional | Fallback YTS API URL |
| `PORT` | Auto | Set automatically by Render — do not set manually |

> ⚠️ `SITE_URL` is the most critical variable. If missing, the sitemap and Digital Asset Links will reference the wrong domain.

---

## 🌐 Deploying to Production

### Render (recommended)

1. Fork this repo to your GitHub account
2. Go to [render.com](https://render.com) and create a new **Web Service**
3. Connect your forked repo
4. Set **Build Command:** `npm install`
5. Set **Start Command:** `npm start`
6. Add your environment variables under **Environment**
7. Click **Deploy**

> Render free tier sleeps after 15 minutes of inactivity. Use [cron-job.org](https://cron-job.org) to ping `/api/health` every 10 minutes to keep it awake.

### Railway

```bash
npm install -g @railway/cli
railway login && railway init && railway up
```

Add your environment variables in the Railway dashboard under **Variables**.

### Any VPS (Ubuntu/Debian)

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
git clone https://github.com/Advay254/SkyluxMovies.git
cd SkyluxMovies && npm install
npm install -g pm2
pm2 start index.js --name skyluxmovies
pm2 save && pm2 startup
```

---

## 🛠️ How It Works

```
User deploys SkyluxMovies
        ↓
Launcher starts and fetches the core engine securely at runtime
Core engine extracts and installs its own dependencies
        ↓
App starts — homepage loads parallel fetches
(trending, most watched, new releases, box office)
Genre rows stream in with staggered requests
        ↓
User searches or taps a movie card
        ↓
GET /api/movie/:id
  → Server checks in-memory cache (6hr TTL)
  → Cache miss: fetches YTS movie_details with cast + images
  → Rewrites all YTS image URLs to /api/img?u= proxy
  → Returns proxied JSON to client
        ↓
Movie detail page renders
  → Backdrop blur, poster, cast circles, IMDb ring, genres, synopsis
  → Quality picker shows available torrents (720p / 1080p / 4K)
  → Tapping a quality triggers SPlayer magnet link intent
  → Trailer button opens YouTube directly
  → Similar movies in 3-column grid
        ↓
Browser opens SPlayer → torrent streams or downloads natively on device
```

> **Why image proxy?** YTS CDN applies hotlink protection. The `/api/img?u=` proxy handles this server-side so images always load reliably.

---

## ⚡ Cache TTL Reference

| Endpoint | TTL | Reason |
|----------|-----|--------|
| `/api/search` | 10 min | Search results change often |
| `/api/trending`, `/api/watched`, `/api/new`, `/api/boxoffice`, `/api/anticipated` | 2 hr | Updated daily by YTS |
| `/api/toprated`, `/api/popular` | 2 hr | Stable high-traffic lists |
| `/api/movie/:id` | 6 hr | Movie metadata rarely changes |
| `/api/suggestions/:id` | 4 hr | Suggestion list changes slowly |
| `/api/img` | 24 hr | Image bytes never change |

---

## 🔒 Security & Rate Limiting

Three-tier rate limiting — tuned so normal browsing never hits a limit:

| Tier | Endpoints | Limit |
|------|-----------|-------|
| Unrestricted | `/api/img` (image proxy) | No limit — 24hr cached, one real fetch per image |
| Search | `/api/search` | 30 req / min — scrape protection |
| API | All other `/api/` routes | 600 req / 15 min per IP |

Additional protections:
- **Server-side image proxy** — real YTS CDN URLs never exposed to the browser
- **Optional API key** — protect endpoints from external scrapers
- **Hardened HTTP headers** — via `helmet` (XSS, clickjacking, MIME sniffing protection)
- **Digital Asset Links** — TWA verified via `/.well-known/assetlinks.json` so the Android app runs without a URL bar

---

## 📲 Android App (TWA)

SkyluxMovies includes a native Android APK built as a Trusted Web Activity:

- No URL bar — runs exactly like a native app
- **In-browser install banner** — shown automatically to browser users, hidden in PWA/TWA mode
- PWA-ready with full offline support via service worker

---

## 🗺️ SEO

SkyluxMovies ships with production-grade SEO out of the box:

- **JSON-LD knowledge graph** — `Organization`, `WebSite`, `WebPage`, and `Movie` entities
- **FAQPage schema** — eligibility for Google AI Overviews and rich results
- **Movie schema** — title, year, genre, rating, runtime, language per movie page
- **Dynamic Open Graph** — title, description, and poster update after each movie loads
- **robots.txt** — explicit `Allow` for GPTBot, ClaudeBot, and PerplexityBot
- **XML sitemap** at `/sitemap.xml` — includes all pages and blog articles
- **Canonical URLs** on every page

---

## 🔄 Updates

Check `version.txt` for the latest release version. Sync your fork and redeploy on Render to get the latest update.

---

## 🤝 Contributing

1. Fork the repo
2. Create your branch: `git checkout -b feature/your-feature`
3. Commit: `git commit -m 'Add your feature'`
4. Push: `git push origin feature/your-feature`
5. Open a Pull Request

---

## ⚠️ Disclaimer

SkyluxMovies is an independent open-source project and is **not affiliated with, endorsed by, or connected to YTS, YIFY, or any movie studio** in any way.

This tool indexes publicly available torrent metadata. Users are responsible for complying with copyright laws in their country. Always respect the work of filmmakers and content creators.

---

## 📄 License

MIT © 2026 Advay — free to use, modify, and distribute.

---

<div align="center">

**If SkyluxMovies saved you time, drop a ⭐ — it helps others find the project.**

</div>
