# AI Future Lab — Cloud YouTube Shorts Automation

A starter automation system for **@TheAIFutureLabHQ**. It creates one English AI-themed vertical Short per scheduled run, generates a simple branded slide video with voiceover, and uploads it to YouTube.

## What it does

1. Gemini creates a topic, 90–115 word script, title, description, hashtags, tags and six scene captions.
2. Google Translate TTS generates an English voice track.
3. Sharp creates six branded 9:16 slides; FFmpeg combines them with the voice track into an MP4.
4. YouTube Data API uploads the Short.
5. GitHub Actions runs it daily at **7:00 PM India time** and stores a 7-day output artifact.

**Important limitations**
- This is an automated slide-style Short, not AI-generated cinematic footage. The visuals are branded text/graphic scenes.
- Topic ideation is AI-generated; this starter does not verify breaking news or live trends. Review factual claims.
- Google Translate TTS is an unofficial endpoint and may change or rate-limit. For commercial reliability, replace it with a licensed TTS provider.
- Free Gemini quotas and GitHub limits can change. A public GitHub repository's standard hosted Actions usage is free under GitHub's current policy.
- Keep the repository private if possible. Never expose API keys or OAuth tokens in code.
- Start with `unlisted`. Review the first videos before setting `YOUTUBE_PRIVACY` to `public`.
- Do not upload repetitive, low-value, misleading or mass-produced videos. Add original commentary and review each output to protect channel quality and monetization eligibility.

## A. Create the required keys

### 1. Gemini API key
- Open https://aistudio.google.com/app/apikey
- Create an API key.
- Store it as a GitHub Actions secret named `GEMINI_API_KEY`.

Gemini free tier has model and rate limits; check https://ai.google.dev/gemini-api/docs/rate-limits and pricing.

### 2. YouTube API OAuth credentials
- Open https://console.cloud.google.com/
- Create a Google Cloud project.
- Enable **YouTube Data API v3**.
- Configure OAuth consent screen (External; add your Google account as a test user if the app is in Testing).
- Create OAuth Client ID → **Web application**. Add this exact Authorized redirect URI: `http://localhost:3000/oauth2callback`.
- Copy Client ID and Client Secret.
- Important: OAuth consent/testing configuration can require reauthorization periodically. For stable use, configure the consent screen appropriately and follow Google's verification requirements.

### 3. Generate a refresh token on your computer
Install Node.js 20+ and FFmpeg. Then:

```bash
npm install
cp .env.example .env
```

Put `YOUTUBE_CLIENT_ID` and `YOUTUBE_CLIENT_SECRET` into `.env`. Run:

```bash
npm run auth
```

Run `npm run auth`; open the URL printed in the terminal, sign in with the Google account that owns the channel, and approve upload permission. The browser returns to a local confirmation page; copy the refresh token printed in your terminal.

On Windows, if `cp` is unavailable, copy `.env.example` and rename the copy to `.env`.

## B. Add GitHub secrets

1. Create a GitHub repository and upload all project files.
2. Open repository → Settings → Secrets and variables → Actions → New repository secret.
3. Add these four secrets:

| Secret name | Value |
|---|---|
| `GEMINI_API_KEY` | Gemini API key |
| `YOUTUBE_CLIENT_ID` | OAuth client ID |
| `YOUTUBE_CLIENT_SECRET` | OAuth client secret |
| `YOUTUBE_REFRESH_TOKEN` | Refresh token from `npm run auth` |

4. Optional: Settings → Secrets and variables → Actions → Variables → New repository variable:
   - Name: `YOUTUBE_PRIVACY`
   - Value: `unlisted` (recommended for first tests)

If the variable is absent, the workflow uses `unlisted`.

## C. Turn on daily cloud automation

- Push the repository to GitHub.
- Open **Actions** tab.
- Enable workflows if GitHub asks.
- Select **AI Future Lab Daily Shorts** → **Run workflow** to test.
- It will also run every day at 7 PM India time (13:30 UTC).

After a successful run, open the run → Artifacts → download `ai-future-lab-output` to inspect the MP4 and JSON metadata.

## D. Local dashboard (optional)

```bash
npm start
```

Open http://localhost:3000 and press **Run automation now**.

Do not expose this dashboard to the public internet. It has no login/authentication.

## E. Change upload mode

Set GitHub repository variable `YOUTUBE_PRIVACY` to:
- `unlisted` — link-only review (recommended initially)
- `private` — only you
- `public` — publish immediately

If your Google API project is not eligible for public uploads, YouTube may restrict uploads to private. Follow the API console / YouTube API compliance requirements.

## Troubleshooting

- `quotaExceeded` / `rateLimitExceeded`: wait for quota reset or check API quota.
- `invalid_grant`: rerun `npm run auth` and replace the refresh token secret.
- `GEMINI_API_KEY` missing: check exact GitHub secret spelling.
- `ffmpeg not found`: install FFmpeg locally; GitHub workflow installs it automatically.
- TTS errors: Google Translate TTS can rate-limit; retry later or integrate a supported TTS service.
- Workflow not starting on schedule: GitHub scheduled workflows can be delayed; manual `workflow_dispatch` is available.

## Safety checklist

- Never commit `.env`, API keys, OAuth client secrets or refresh tokens.
- Use `unlisted` until you have checked voice pronunciation, spelling, factual accuracy, and video quality.
- Use only content, images, audio and music you have rights to use.
- Add meaningful original editing/commentary; uploading fully automated repetitive videos does not guarantee monetization.
