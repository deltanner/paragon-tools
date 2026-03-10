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

## Quote Builder (quote-builder.html)

### Architecture
- Tabbed interface: New Quote / Unit Costs / Saved Quotes
- Firebase paths: `quoteBuilder/settings` (unit costs), `quoteBuilder/quotes` (saved quotes)
- VIP3D takeoff PDF import via pdf.js (client-side text extraction)
- Side panel shows real-time cost breakdown with two columns: My Cost and Bill Rate

### Pricing Model
- Margin-based pricing: `price = cost / (1 - margin)` — NOT markup
- Default global margin: 30%, adjustable via slider
- Category-level margin overrides: equipment/products (via `equipMargin`), tile/stone (via `tileMargin`)
- Line items can specify `marginCat: 'tile'` or `marginCat: 'equip'` to use override margins

### Spa Configuration
- **None**: No spa
- **Internal**: Spa carved into pool shell with dam wall
  - Inputs: Spa Perimeter (lf), Dam Wall Length (lf), Dam Wall Height (ft)
  - Dam wall face gets "Other Tile" (sqft = length × height)
  - Dam wall top gets waterline tile (added to waterline LF)
  - Spa perimeter used for rebar and interior finish (via `allSpaPerim`)
  - Does NOT add to coping perimeter (internal spas have no exposed coping edge)
- **External On Grade**: Separate vessel at deck level
  - Inputs: Spa Perimeter (lf), Volume (gal), Spillover config
  - Spa perimeter adds to coping, rebar, and interior finish
- **External Raised**: Separate vessel with exposed veneer
  - Same inputs as On Grade, plus Raised Height (ft) and Shared Side (lf)
  - Auto-calculated veneer sqft = (spa perimeter − shared side) × raised height
  - Spa perimeter adds to coping, rebar, and interior finish

### Spillover Logic (external spas only)
- **Open**: Removes coping along spillover edge, adds tile underneath (spillover sqft = length × 1ft)
- **Covered**: Coping stays over spillover, but tile is still added underneath
- Spillover tile goes to "Other Tile" category ($/SF)
- Fixed 1ft height (12" wall thickness)

### Key Variables in recalc()
- `spaPerim`: External spa perimeter only (used for coping)
- `allSpaPerim`: Spa perimeter for ALL spa types including internal (used for rebar, interior)
- `perimeter`: Pool perimeter from Pool Dimensions
- `damWallLen` / `damWallHt`: Internal spa dam wall (used for waterline tile LF and other tile sqft)

### Cost Calculation Categories

**1. Site Prep**: Geotech, excavation, forming, permits (city-specific), general conditions

**2. Shell**
- Steel/Rebar: auto-calculated as `(perimeter + allSpaPerim) × $/lf` — default $41/lf
- Shotcrete: combined labor + material, `shotcreteCY × $/cy` — single setting

**3. Plumbing**: Plumbing labor, gas line (if spa or heater), conduit/wiring (auto-calc from light count)

**4. Equipment**: Heritage Plus package (flat cost, equip margin), salt system, automation, heater, booster pump, feature pump, blower
- Business rules: salt chlorinator → requires automation; spa → requires heater + valves + gas line; water features → requires feature pump

**5. Tile/Coping**
- **Waterline Tile** ($/LF): auto-calculated LF = pool perimeter + internal spa dam wall + cover vault dam wall
  - Material and Labor as separate line items
- **Other Tile** ($/SF): dam wall face sqft + spillover sqft
  - Material and Labor as separate line items
- **Veneer** ($/SF): raised spa veneer + planter veneer — all consolidated
  - Material and Installation as separate line items
- **Coping**: Two types (mutually exclusive):
  - Pour-in-place: single $/LF cost
  - Stone/paver: material $/LF + install $/LF (material can be overridden per-quote via `copingStoneLF` input, falls back to setting)
  - Coping perimeter = pool perimeter + external spa perimeter + vault coping − spillover deduction (open only)
  - Vault coping = vault length + 2 × vault width (3 exposed sides)
- All tile/coping material uses `marginCat: 'tile'` for tile margin override

**6. Decking**: Flat cost based on decking type (concrete, pavers, travertine)

**7. Auto Cover** (optional)
- Cover Care flat cost (equip margin)
- Creates a vault that adds:
  - Dam wall → waterline tile LF (vault length added)
  - 3-sided coping perimeter (length + 2 × width)
  - Vault lid costs: Pour-in-Place ($/LF) or Stone (material $/LF + install $/LF)
- Vault inputs: Length, Width, Lid Type (pour-in-place or stone)

**8. Interior Finish**
- Based on total perimeter LF: `(pool perimeter + allSpaPerim) × $/lf` — default $45/lf
- Includes spa perimeter for ALL spa types (internal, external grade, external raised)
- Depth surcharge: $600 per foot of depth over 6' (e.g., 8' deep = $1,200 surcharge)

**9. Additional Options**: Planter, raised wall, sheer descents (count × unit cost)

### Cover Vault Cascading Effects
When Auto Cover is selected and vault dimensions are entered:
1. Vault length adds to waterline tile LF
2. Vault creates 3-sided coping perimeter (length + 2 × width)
3. Vault lid costs added based on lid type selection
4. All vault-related tile/coping uses appropriate margin categories

### Save/Load
- Quotes saved to Firebase at `quoteBuilder/quotes/{id}`
- Stores: customer info, dimensions, spa config + dims, options, checkboxes, select values
- Settings saved at `quoteBuilder/settings` with all unit cost values

### Takeoff PDF Import
- Parses VIP3D takeoff PDFs using pdf.js client-side
- Extracts: perimeter, area, volume, depths, shotcrete CY, spa data
- Auto-detects spa type and populates appropriate fields
- Rebar and interior finish are auto-derived (no manual import needed)

## TODO / Future Work
- **Takeoff PDF import refinement**: Del will create a consistent VIP3D takeoff export format. Once that's defined, tighten the parser to match the standardized section layout and field order. Currently the parser handles column-layout text mixing but could be much more reliable with a known format.
- **Quote Summary output view**: Print-ready format for presenting to customers
- **JobTread API integration**: Push quotes to JobTread (mentioned but not started)
- **Heritage Plus pricing**: If Heritage ever offers an API, integrate real-time equipment pricing
