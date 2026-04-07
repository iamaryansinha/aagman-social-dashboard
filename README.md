# Aagman Social Dashboard

Static HTML dashboard for tracking social media performance across X/Twitter, Instagram, and LinkedIn.

**Live URL:** https://aagman-social.vercel.app

## Setup Instructions

### 1. Create GitHub Repo

Go to https://github.com/new and create:
- **Repo name:** `aagman-social-dashboard`
- **Visibility:** Public (for free Vercel hosting)
- **Initialize:** Don't add README (we have one)

### 2. Push This Code

```bash
cd ~/.openclaw/workspace/aagman-social-dashboard

# Add remote (replace with your username)
git remote add origin https://github.com/YOUR_USERNAME/aagman-social-dashboard.git

# Rename branch to main
git branch -m main

# Commit and push
git add .
git commit -m "Initial dashboard"
git push -u origin main
```

### 3. Deploy to Vercel

**Option A: Vercel CLI**
```bash
# Install Vercel CLI if not already
npm i -g vercel

# Login and deploy
vercel login
vercel --prod
```

**Option B: GitHub Integration (Recommended)**
1. Go to https://vercel.com/new
2. Import your GitHub repo
3. Framework preset: **Other** (static HTML)
4. Deploy

**Option C: OpenClaw Agent**
Just tell me: "Deploy the dashboard" and I'll push it to your GitHub and trigger Vercel deploy.

### 4. Update Data Daily

The dashboard auto-generates from your social data. To update:

```bash
# Run the full pipeline
bash ~/.openclaw/workspace/scripts/fetch_instagram_daily.sh
python3 ~/.openclaw/workspace/scripts/generate_dashboard.py

# Copy new dashboard to repo
cp ~/.openclaw/workspace/social_posts/dashboard.html \
   ~/.openclaw/workspace/aagman-social-dashboard/index.html

# Push to GitHub (auto-deploys to Vercel)
cd ~/.openclaw/workspace/aagman-social-dashboard
git add index.html
git commit -m "Update dashboard - $(date +%Y-%m-%d)"
git push
```

Or use the helper script:
```bash
bash ~/.openclaw/workspace/scripts/deploy_dashboard.sh
```

## Data Sources

| Platform | Source | Update Frequency |
|----------|--------|------------------|
| Instagram | Apify API | Daily |
| X/Twitter | Agent Reach CLI | Daily |
| LinkedIn | linkedin-api | Daily |

## Local Development

```bash
cd ~/.openclaw/workspace/aagman-social-dashboard
python3 -m http.server 8080
# Open http://localhost:8080
```

## Features

- **Dark theme** — Trading/finance aesthetic
- **3 Platform Sections** — X, Instagram, LinkedIn
- **Summary Stats** — Total posts, engagement, averages
- **Interactive Charts** — Content type, engagement trends
- **Recent Posts Table** — Last 10 posts per platform
- **Auto-refresh** — Regenerate daily via cron

## Cron Setup (Auto-Update)

Add to crontab for daily 9 AM IST updates:
```bash
0 4 * * * /root/.openclaw/workspace/scripts/deploy_dashboard.sh >> /tmp/dashboard_cron.log 2>&1
```

## File Structure

```
aagman-social-dashboard/
├── index.html          # Main dashboard (generated)
├── vercel.json         # Vercel config
└── README.md           # This file
```

## Custom Domain (Optional)

1. Buy domain (e.g., `social.aagman.in`)
2. Add to Vercel project settings
3. Update DNS records

---

**Need help?** Just ask the agent to "deploy dashboard" or "update dashboard".
