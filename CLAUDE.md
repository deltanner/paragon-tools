# Paragon Tools — Project Memory

## Overview
Internal tools suite for **Paragon Pools** (St. George, Utah pool builder). Static HTML/CSS/JS site hosted on GitHub Pages with Firebase Realtime Database for cloud data sync.

## Live Site
- **URL**: https://deltanner.github.io/paragon-tools/
- **Repo**: https://github.com/deltanner/paragon-tools (owner: deltanner)
- **Hosting**: GitHub Pages (legacy mode, deploys from `main` branch)

## Firebase
- **Project**: toolsbyparagon (Spark/free plan)
- **Realtime DB**: https://toolsbyparagon-default-rtdb.firebaseio.com
- **App ID**: 1:204324649539:web:35c834ad63c90a5292b2f6
- **Used for**: Cloud sync of saved pool configurations in the head loss calculator
- **Auth**: Password gate (SHA-256 hashed, stored in sessionStorage per tab)

## Branding
- **Gold**: #CC9933 (primary accent)
- **Gold dark**: #a67a2a
- **Gold light**: #faf3e6
- **Black**: #1a1a1a (backgrounds, nav)
- **Charcoal**: #2d2d2d
- **Font**: Segoe UI / system-ui
- **Logo**: Full SVG logo with gold text + geometric mark, viewBox="0 0 413.07 220.36"
- **Nav mark**: Geometric gold polygons only, viewBox="120 0 160 135"
- **Favicon**: favicon.svg (gold geometric mark on dark background)

## Site Structure & Navigation

```
index.html (2 cards)
├── engineering-tools.html (menu page)
│   ├── pool-head-loss-calculator.html (★ main tool, has Firebase + password gate)
│   └── weir-flow-calculator.html
└── customer-tools.html (menu page, 2 cards)
    ├── intake-form.html (8-section customer consultation form)
    └── design-inspiration.html (15-category photo gallery)
        └── gallery/ (15 hero images, mostly from Paragon's own Inspo Pics)
```

## Key Design Patterns
- **Topnav**: Black bar with gold border-bottom, SVG geometric mark + breadcrumb path
- **Menu pages**: CSS grid cards with icon area (black bg) + body area (white bg), hover effects
- **Breadcrumbs**: Paragon Tools / [Section] / [Page] — gold for current page, gray links for parents
- **Cards**: border-radius: 12px, box-shadow, hover lift effect

## Head Loss Calculator Details
- Multi-circuit hydraulic calculator with pipe runs, fittings, equipment
- Plumbing cost estimator built in
- Filter sizing calculator built in
- Save/Load library synced to Firebase Realtime Database
- Password protected (password gate on page load)
- Cloud sync badge shows "☁ Cloud" / "Saving…" / "⚠ Offline" status

## Design & Inspiration Gallery
- 15 categories in a 5-column CSS grid (4:3 aspect tiles)
- Categories: pool-shapes, feature-walls, coping, decking, interior-finishes, hardscaping, waterfalls, water-features, tile, spas, lighting, furniture, slides-diving, vanishing-edge, entries
- 13 images sourced from Paragon's own marketing/inspo photos, 2 from Pexels stock
- Each category opens a lightbox with description + placeholder for future photo gallery

## Intake Form
- 8 sections: Contact Info, Project Overview, Pool Design, Spa Features, Water Features, Decking & Hardscaping, Equipment & Systems, Additional Info
- Client-facing consultation tool for initial meetings
- Currently saves form data to localStorage only (not Firebase — could be added)

## Files NOT in Git (via .gitignore)
- .~lock.* (LibreOffice lock files)
- *.pdf (PLUMBING PRICE LIST.pdf)
- pool-head-loss-worksheet.xlsx

## Source Files Location
- User's mounted folders:
  - /mnt/Tools — the website files (also the git repo)
  - /mnt/Paragon Pools — company files including Marketing/Inspo Pics, Marketing/Inspo, etc.

## Publishing Workflow
To push changes live:
```bash
cd /sessions/*/mnt/Tools
~/bin/gh auth setup-git   # if needed
git add [files]
git commit -m "description"
git push
```
Site updates within ~1 minute after push via GitHub Pages legacy build.

## gh CLI Notes
- Installed at ~/bin/gh (arm64 binary, not in default PATH)
- Auth token has repo, gist, read:org scopes (no workflow scope)
- Cannot push .github/workflows/ files — use legacy Pages deployment only
