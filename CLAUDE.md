# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal KL (Kuala Lumpur) trip planner for April 2-5, 2026. Two self-contained single-file HTML apps with embedded CSS/JS — no build system, no frameworks, no package manager.

## Development

Serve locally for testing (Wikimedia/Leaflet APIs require HTTP, not file://):
```bash
python3 -m http.server 8765
```

No build, lint, or test commands — these are standalone HTML files.

## Architecture

### Main Deliverables
- **`kl-explorer.html`** (~985 lines) — Interactive place explorer. 48 places across 9 categories (landmark, cultural, neighborhood, garden, museum, food, cafe, nightlife). Features: Leaflet map, category/crowd-level filtering, search, detail modals with image carousel (Wikimedia Commons API), distance-from-hotel on every card, scroll-reveal animations.
- **`kl-itinerary.html`** (~749 lines) — Day-by-day itinerary view. 4-column grid (arrival + 3 days), timeline cards with time slots, Leaflet route maps per day, collapsible food/transport tips.

### Shared Patterns (both HTML files)
- Vanilla HTML/CSS/JS, no dependencies except Leaflet.js and Google Fonts CDN
- CSS custom properties defined in `:root` for theming (same palette in both files)
- Place data stored as inline JS arrays of objects
- Hotel coordinates hardcoded: `lat: 3.1555, lng: 101.714` (Novotel KL City Centre)
- Images: Wikimedia Commons API (`https://commons.wikimedia.org/w/api.php`) with CORS proxy fallbacks
- Responsive breakpoints: 375px, 768px, 1024px

### Design System
- **Fonts:** Cormorant Garamond (headings) + Outfit (body) — used in the actual HTML files
- **Palette:** `--bg:#FAF7F2`, `--accent:#C17F59`, `--gold:#D4A853`, `--text:#1A1714`
- Category colors: each category has its own CSS variable (e.g., `--cat-landmark:#4A6FA5`)
- Grain texture overlay on both pages
- Note: `design-system/kl-trip/MASTER.md` was auto-generated with different fonts/colors (Newsreader/Roboto, pink accent) — the HTML files do NOT follow it. The `:root` variables in the HTML files are the source of truth.

### Supporting Files
- `KL_Hidden_Gems_Guide.md` / `KL_Food_Guide.md` — Research source material
- `images/` — Nano Banana 2-generated hero/day banner images (PNG + JPEG thumbnails)
- `stitch-mockups/` — Google Stitch mockups (3 variants) used during itinerary design
- `travel_persona.md` — User's travel preferences and constraints

## Design Workflow Protocol

When asked to build any UI, page, or visual component, follow this 5-phase workflow IN ORDER. Do not skip to coding.

### Phase 1 — VISION (Google Stitch)
1. Create a Stitch project: `mcp__stitch__create_project`
2. Generate 2-3 screen mockups from a text description: `mcp__stitch__generate_screen_from_text`
3. Present mockups to the user
4. User picks a direction BEFORE any code is written
5. Generate variants if needed: `mcp__stitch__generate_variants`

### Phase 2 — SYSTEM (UI UX Pro Max)
1. Run design system generation with persist:
   ```bash
   python3 .claude/skills/ui-ux-pro-max/scripts/search.py "<product keywords>" --design-system --persist -p "KL Trip"
   ```
2. For specific pages, add page override:
   ```bash
   python3 .claude/skills/ui-ux-pro-max/scripts/search.py "<page keywords>" --design-system --persist -p "KL Trip" --page "<pagename>"
   ```
3. Check `design-system/MASTER.md` for global rules
4. Check `design-system/pages/<page>.md` for page-specific overrides
5. Supplement with domain searches as needed (--domain style/color/typography/ux/landing)

### Phase 3 — COMPONENTS (21st.dev Magic)
1. Search for relevant component patterns: `mcp___21st-dev_magic__21st_magic_component_inspiration`
2. For React/Next.js projects: use `mcp___21st-dev_magic__21st_magic_component_builder` to generate components directly
3. For vanilla HTML projects: study the component patterns and reimplement in vanilla CSS+JS
4. For logos/brand assets: use `mcp___21st-dev_magic__logo_search`

### Phase 4 — ASSETS (Nano Banana 2)
1. Generate custom images matching the design system from Phase 2
2. Style prompts must reference the exact palette and aesthetic
3. Use consistent style keywords across all images for cohesion
4. Output to project `images/` directory
5. Prefer `nb2` model tier for speed, `pro` for maximum quality

### Phase 5 — BUILD (Claude Code)
1. Code the page/app to match the Stitch mockup (Phase 1)
2. Follow UI UX Pro Max rules strictly (Phase 2)
3. Use 21st.dev component patterns (Phase 3)
4. Embed Nano Banana assets (Phase 4)
5. Run the pre-delivery checklist:
   - [ ] No emojis as icons (use SVG)
   - [ ] cursor-pointer on all clickable elements
   - [ ] Hover transitions 150-300ms
   - [ ] Text contrast 4.5:1 minimum
   - [ ] Focus states visible
   - [ ] prefers-reduced-motion respected
   - [ ] Responsive: 375px, 768px, 1024px

### When to Skip Phases
- Skip Phase 1 (Stitch) for: small tweaks, bug fixes, text changes
- Skip Phase 3 (21st.dev) for: vanilla HTML projects where no React components are usable
- Never skip Phase 2 (design system) or Phase 5 (build checklist)
