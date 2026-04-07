# Aagman Social Dashboard - Daily Workflow

## Overview

This workflow fetches your social media posts from X/Twitter and Instagram, generates a static HTML dashboard, and deploys it to Vercel automatically.

**Dashboard URL:** https://aagman-social.vercel.app  
**GitHub Repo:** https://github.com/iamaryansinha/aagman-social-dashboard

---

## What Gets Fetched

| Platform | Source | Posts Fetched | Data Included |
|----------|--------|---------------|---------------|
| **X/Twitter** | Agent Reach CLI | All available (~63) | Date, Type, Caption, Impressions, URL |
| **Instagram** | Apify API | Last 50 | Date, Type, Caption, Likes, Comments, URL |
| **LinkedIn** | — | — | Coming soon (requires credentials) |

### Post Types Detected (X)
- **Post** — Regular tweets
- **Quote** — Quote tweets (retweet with comment)
- **Retweet** — Pure retweets
- **Reply** — Reply tweets (if fetched)
- **Article** — Long-form articles

---

## Prerequisites

### 1. Environment Variables (Already Configured)

Located in: `~/.openclaw/workspace/.env`

```bash
APIFY_TOKEN=apify_api_xxxxxxxxxxxxxxxxxxxx  # Your Apify token
```

### 2. X/Twitter Auth (Already Configured)

Located in: `~/.bashrc` and `~/.agent-reach/config.yaml`

```bash
TWITTER_AUTH_TOKEN=your_auth_token_here
TWITTER_CT0=your_ct0_token_here
```

### 3. GitHub Access (Already Configured)

Remote: `https://iamaryansinha:TOKEN@github.com/iamaryansinha/aagman-social-dashboard.git`  
(Replace TOKEN with your GitHub personal access token)

---

## File Locations

```
~/.openclaw/workspace/
├── .env                                    # API keys
├── social_posts/
│   ├── instagram/
│   │   └── tradewithaagman_YYYYMMDD_HHMMSS.json  # Apify output
│   ├── twitter/
│   │   └── tradewithaagman_posts.json      # Agent Reach output
│   ├── csv/                                # CSV exports (optional)
│   └── dashboard.html                      # Generated dashboard
├── skills/
│   └── apify-ultimate-scraper/             # Apify skill
├── scripts/
│   ├── fetch_instagram_daily.sh            # Instagram fetch script
│   ├── generate_dashboard.py               # Dashboard generator
│   └── deploy_dashboard.sh                 # Full deploy script
└── aagman-social-dashboard/                # Git repo
    ├── index.html                          # Deployed dashboard
    ├── vercel.json                         # Vercel config
    └── README.md                           # Repo docs
```

---

## Daily Workflow Script

**Main Script:** `~/.openclaw/workspace/scripts/deploy_dashboard.sh`

### What It Does (In Order):

1. **Generate Dashboard** (`generate_dashboard.py`)
   - Load Instagram posts from JSON files
   - Load X posts from JSON file
   - Parse post types and metadata
   - Generate HTML with tables sorted by date (newest first)
   - Save to `social_posts/dashboard.html`

2. **Copy to Repo**
   - Copy `dashboard.html` to `aagman-social-dashboard/index.html`

3. **Git Commit**
   - Stage changes
   - Commit with timestamp: "Update dashboard - YYYY-MM-DD HH:MM"

4. **Push to GitHub**
   - Push to `main` branch
   - Vercel auto-deploys in ~30 seconds

---

## Manual Run (One-Time Test)

```bash
# Run the full workflow
bash ~/.openclaw/workspace/scripts/deploy_dashboard.sh

# Expected output:
# 🔄 Aagman Social Dashboard Deploy Script
# ========================================
# [1/4] Generating fresh dashboard...
# ✅ Dashboard generated: ...
#    Posts: 15 Instagram, 63 X, 0 LinkedIn
# [2/4] Copying to repo...
# [3/4] Committing changes...
# [main xxxxxxx] Update dashboard - YYYY-MM-DD HH:MM
# [4/4] Pushing to GitHub...
# ✅ Dashboard deployed!
```

---

## Individual Scripts (For Debugging)

### Fetch Instagram Only
```bash
bash ~/.openclaw/workspace/scripts/fetch_instagram_daily.sh
```

**What it does:**
- Runs Apify actor `apify/instagram-post-scraper`
- Fetches last 50 posts from @tradewithaagman
- Saves to `social_posts/instagram/tradewithaagman_YYYYMMDD_HHMMSS.json`
- Also fetches profile stats

### Fetch X Only
```bash
export TWITTER_AUTH_TOKEN="your_auth_token"
export TWITTER_CT0="your_ct0_token"

~/.agent-reach-venv/bin/twitter user-posts tradewithaagman --max 300 --json \
  -o ~/.openclaw/workspace/social_posts/twitter/tradewithaagman_posts.json
```

### Generate Dashboard Only
```bash
cd ~/.openclaw/workspace
python3 scripts/generate_dashboard.py
```

### Deploy Only (Skip Regeneration)
```bash
cd ~/.openclaw/workspace/aagman-social-dashboard
git add index.html
git commit -m "Update dashboard - $(date '+%Y-%m-%d %H:%M')"
git push origin main
```

---

## Cron Schedule

**Runs Daily:** 23:00 IST (11:00 PM India Time)  
**Server Time:** 17:30 UTC / 18:30 GMT+8

### Cron Entry:
```cron
# Aagman Social Dashboard - Daily update at 23:00 IST
0 17 * * * /bin/bash -c 'export HOME=/root; export PATH="$HOME/.agent-reach-venv/bin:$PATH"; bash /root/.openclaw/workspace/scripts/deploy_dashboard.sh >> /var/log/aagman-social-cron.log 2>&1'
```

### Check Cron Status:
```bash
crontab -l | grep aagman
```

### View Logs:
```bash
tail -f /var/log/aagman-social-cron.log
```

---

## Troubleshooting

### Issue: Dashboard not updating
**Check:**
1. GitHub repo has new commits?
2. Vercel build status at https://vercel.com/dashboard
3. Run manual deploy and check error messages

### Issue: X posts not fetching
**Check:**
1. Auth tokens expired? (Check `~/.bashrc`)
2. Run X fetch manually to see errors
3. Rate limit? (Wait 15 minutes)

### Issue: Instagram not fetching
**Check:**
1. Apify token valid? (Check `~/.openclaw/workspace/.env`)
2. Apify credits remaining? (Check https://console.apify.com)

### Issue: Wrong dates in dashboard
**Fix:**
```bash
# Regenerate from scratch
cd ~/.openclaw/workspace
python3 scripts/generate_dashboard.py
bash scripts/deploy_dashboard.sh
```

---

## Future Enhancements

### LinkedIn Integration
- Requires: LinkedIn email + password
- Script: `scripts/fetch_linkedin.py` (ready but needs credentials)
- Run: Set `LINKEDIN_USERNAME` and `LINKEDIN_PASSWORD` env vars

### More X Posts
- Current: ~63 posts (API limitation)
- To get all: Use Twitter Archive import or Twitter API v2 ($100/mo)

### Notifications
- Add Slack/Telegram webhook on deploy success/failure
- Modify `deploy_dashboard.sh` to send notifications

---

## Support

**Agent:** Run `bash ~/.openclaw/workspace/scripts/deploy_dashboard.sh`  
**Manual:** Check logs at `/var/log/aagman-social-cron.log`  
**GitHub:** https://github.com/iamaryansinha/aagman-social-dashboard  
**Live URL:** https://aagman-social.vercel.app

---

*Last Updated: 2026-04-08*  
*Workflow Version: 1.0*
