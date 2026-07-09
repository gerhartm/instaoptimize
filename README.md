# InstaOptimize

A local-first Instagram account management tool with two modes: **Vanity** (clean your follower/following ratio) and **Love** (build a scored dating prospect pipeline from your existing social graph).

No backend. No API keys. No Instagram login. Just a CSV export of your following list + a single HTML file you open in Chrome.

![screenshot](screenshot.png)

## Why This Exists

If you're trying to use Instagram for dating (especially in the gay community), your profile IS your resume. Two things matter:

1. **Your ratio** — following 2,000 people when you have 1,500 followers signals low social value. Unfollowing brands, dead accounts, and 10K+ accounts you'll never interact with is the fastest fix.
2. **Your existing graph is an untapped dating pool** — you already follow people you find interesting. Nobody helps you systematically work through that list.

## How It Works

### Setup

1. Export your Instagram following list as a CSV. Tools like [IGExport](https://igexport.io), [Phantombuster](https://phantombuster.com), or similar scraping tools can generate this. The CSV should include these columns:

| Column | Required | Used For |
|--------|----------|----------|
| Username | ✅ | Core identifier |
| Fullname | Optional | Display on card |
| Category | Optional | Instagram's own category (DJ, Artist, etc.) — dramatically improves auto-categorization |
| Followers | ✅ | Ratio analysis, scoring |
| Following | Optional | Display |
| Posts | Optional | Dead account detection (≤2 posts = dead) |
| Bio | Optional | Keyword-based categorization |
| City | Optional | Location display, scoring |
| Is private | Optional | Display |
| Is verified | Optional | Display |
| Is business | Optional | Categorization signal |
| Avatar URL | Optional | Profile photos on cards |
| Profile URL | Optional | Not currently used |

2. Open `index.html` in Chrome
3. Drop your CSV
4. Pick a mode and start swiping

### Vanity Mode

Swipe through every account you follow. Each card shows:
- Profile photo (from Avatar URL in CSV)
- Username, full name, bio, city
- Instagram category tag
- Follower/following/post counts
- **AI suggestion** — the categorization engine pre-labels each account as keep or unfollow

**Category filter tabs** let you batch-process by type: blast through all Dead Accounts first (just spam ← ← ←), then Brands, then 10K+ accounts, then review the ambiguous ones last.

**Output:** a CSV of `username, decision, followers, category` — where decision is `keep` or `unfollow`. Feed the unfollow list to a browser automation tool (see [OpenClaw Integration](#openclaw-integration)).

### Love Mode

Swipe through accounts to build a dating prospect list. Right-swipe = interested, left = pass.

## Categorization Engine

The AI categorization uses a two-pass approach:

### Pass 1: Instagram's Category Field
If the CSV includes Instagram's own category metadata (e.g., "DJ", "Musician/Band", "Shopping & Retail"), this is the most reliable signal. The engine maps ~50 Instagram categories into buckets:

**Auto-Keep:** DJ, Musician/Band, Artist, Photographer, Dance & Night Club, Festival, Art Gallery, etc.

**Auto-Unfollow:** News & Media Website, Shopping & Retail, Product/Service, Fan Page, Magazine, etc.

### Pass 2: Bio + Username Keywords
For uncategorized accounts, the engine scans bios and usernames against keyword lists:

- **Music/DJ/Clubs:** dj, producer, techno, house music, vinyl, rave, rekordbox, boiler room, amapiano, berghain...
- **Art/Creatives:** artist, photographer, filmmaker, curator, gallery, graphic design, tattoo artist...
- **News/Media:** news, reporter, journalism, editorial, broadcast...
- **Meme/Fan Pages:** meme, shitpost, comedy, viral, parody... (only if >5K followers)
- **Brands:** official, shop now, free shipping, promo... (only if >100K followers or >500K)

### Pass 3: Heuristics
- **Dead Accounts:** ≤2 posts and not private
- **Friends & Family:** <2,000 followers and not a business account
- **10K+ Ratio Poison:** >10,000 followers and didn't match any keep category

## Persistence

All session data is saved to `localStorage`:
- Swipe progress auto-saves after every decision
- Close the tab, come back later, resume where you left off
- Multiple sessions supported (e.g., Vanity + Love running in parallel)
- Sessions show on the home screen with progress bars and stats

## OpenClaw Integration

The exported CSV is designed to be fed directly to [OpenClaw](https://openclaw.ai) or any browser automation tool. The workflow:

1. Export the unfollow list CSV from InstaOptimize
2. Write an OpenClaw script that:
   - Opens Instagram in a browser
   - For each username in the CSV where `decision = "unfollow"`:
     - Navigate to `instagram.com/{username}`
     - Click the "Following" button
     - Confirm unfollow
     - Wait 45-90 seconds (random delay to avoid rate limiting)
     - Move to next

**Rate limiting guidance:** Instagram monitors unfollow velocity. Stay under 20-30 unfollows/hour with random delays. Spread 500+ unfollows across 2-3 days.

## What's Built vs. What Could Be Built

### ✅ Built
- CSV import with PapaParse (handles multi-line bios, commas, unicode)
- Two-mode swipe UI with keyboard shortcuts (← → arrows, Cmd+Z undo)
- AI categorization engine (Instagram categories + bio keywords + heuristics)
- Category filter tabs for batch processing
- Love mode scoring and three-tier outreach plan
- Session persistence via localStorage
- CSV export at any point during or after swiping
- Profile photo display from Avatar URL
- "View" button opens Instagram profile in new tab

### 🔨 Could Be Built
- **Mobile swipe gestures** — touch/drag swiping for phone use
- **Photo grid preview** — embed recent posts via Toolzu or similar
- **Mutual follow detection** — if the CSV includes this data, factor it into scoring (+30 points) and tier assignment
- **OpenClaw integration** — direct "Execute Unfollows" button that triggers the automation
- **DM template generator** — suggest openers based on shared interests (bio keyword overlap)
- **Profile strength tracker** — before/after ratio metrics
- **Batch actions** — "unfollow all Dead Accounts" without swiping each one
- **Import from multiple sources** — combine data from different export tools
- **Collaborative mode** — share prospect lists with friends for second opinions
- **Re-export enriched CSV** — add the AI category and score to the original CSV for use in other tools

## Tech Stack

- **Zero build step** — single HTML file, vanilla JS, inline CSS
- **PapaParse** (CDN) — robust CSV parsing
- **Google Fonts** — Instrument Serif + DM Sans
- **localStorage** — session persistence
- No React, no Node, no backend, no API keys

## Running It

```bash
# That's it. Just open the file.
open index.html
```

Or double-click `index.html` in Finder/Explorer. It runs entirely in your browser.

## License

MIT — do whatever you want with it.
