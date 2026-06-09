# 🍼 Baby YouTube Automation Bot

A fully automated GitHub Actions pipeline that creates and uploads a baby-niche YouTube video every single day — from trending topic discovery through to publishing.

---

## How it works

```
6:00 AM → Find trending baby topics (Google Trends + Claude)
7:00 AM → Generate script + AI voiceover (Claude + ElevenLabs)
8:00 AM → Fetch footage + assemble video (Pexels + FFmpeg)
9:00 AM → Upload to YouTube with thumbnail (YouTube API)
```

---

## Setup guide (do this once)

### Step 1 — Fork or clone this repo
```bash
git clone https://github.com/YOUR_USERNAME/baby-youtube-bot.git
cd baby-youtube-bot
```

### Step 2 — Get your API keys

| Service | Where to sign up | Cost |
|---------|-----------------|------|
| Anthropic (Claude) | console.anthropic.com | ~$5/mo |
| ElevenLabs | elevenlabs.io | Free (10K chars) |
| Pexels | pexels.com/api | Free |
| YouTube Data API | console.cloud.google.com | Free |
| SerpAPI (optional) | serpapi.com | 100 free/mo |

### Step 3 — Add GitHub Secrets

Go to your repo → **Settings → Secrets and variables → Actions → New repository secret**

Add each of these:

```
ANTHROPIC_API_KEY        → from console.anthropic.com
ELEVENLABS_API_KEY       → from elevenlabs.io/app/settings
PEXELS_API_KEY           → from pexels.com/api
YOUTUBE_CLIENT_ID        → from Google Cloud Console
YOUTUBE_CLIENT_SECRET    → from Google Cloud Console
YOUTUBE_REFRESH_TOKEN    → see Step 4 below
```

### Step 4 — Get your YouTube refresh token

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project → Enable **YouTube Data API v3**
3. Create **OAuth 2.0 credentials** (Desktop app type)
4. Download the credentials JSON
5. Run this locally ONCE to authorise:

```bash
pip install google-auth-oauthlib
python -c "
from google_auth_oauthlib.flow import InstalledAppFlow
flow = InstalledAppFlow.from_client_secrets_file(
    'client_secrets.json',
    scopes=['https://www.googleapis.com/auth/youtube.upload']
)
creds = flow.run_local_server(port=0)
print('REFRESH TOKEN:', creds.refresh_token)
"
```

6. Copy the printed refresh token → add as `YOUTUBE_REFRESH_TOKEN` secret

### Step 5 — Add a background music file

Add a royalty-free lullaby MP3 to `templates/background_music.mp3`

Free options:
- [Free Music Archive](https://freemusicarchive.org) — search "lullaby"
- [Pixabay Music](https://pixabay.com/music) — search "baby soft"
- [YouTube Audio Library](https://studio.youtube.com) → Audio Library

### Step 6 — Enable the workflows

Go to your repo → **Actions** tab → Enable workflows if prompted.

The pipeline will run automatically every morning. You can also trigger any workflow manually from the Actions tab.

---

## Folder structure

```
.github/workflows/          ← GitHub Actions (runs automatically)
scripts/                    ← Python automation scripts
templates/                  ← Thumbnail base + background music
content_queue/              ← Today's topics and scripts (auto-updated)
output/                     ← Temp files (not committed to git)
```

---

## Monetisation checklist

- [ ] Reach 1,000 subscribers + 4,000 watch hours → Apply for AdSense
- [ ] Add Amazon affiliate links to every video description
- [ ] Pin affiliate comment to each video
- [ ] Apply to baby brand affiliate programs (BabyJogger, Graco, Hatch)
- [ ] Once at 10K subs → reach out to baby brands for sponsorships

---

## Troubleshooting

**Workflow fails at voiceover step:**
→ Check ElevenLabs monthly character limit (10,000 free). Upgrade to $5/mo Starter plan for 30,000 chars.

**YouTube upload fails:**
→ Refresh token may have expired. Re-run Step 4 to get a new one.

**No footage found:**
→ Pexels API key invalid, or daily quota exceeded. Check pexels.com/api/usage.

**FFmpeg error:**
→ Check `assemble_video.py` logs. Usually a corrupt downloaded clip. Re-run the video builder workflow.
