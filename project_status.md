# Champs Global Solutions — Local Website Project Status

## Project Overview
A single-file HTML website for Champs Global Solutions business funding.
- **File**: `index.html` (renamed from `champsglobalservices.html` for GitHub Pages compatibility)
- **Assets**: `New Logo.png`, `Banner.png`
- **Local dev server**: `python3 -m http.server 8090` (port 8090 — port 8080 was already in use)
- **GitHub repo**: `LD-botzillabot-cmd/Champs-Global` (Public)
- **Hosting**: GitHub Pages (free static hosting)
- **Live reference site**: https://champsglobalservices.com

---

## Completed Decisions & Rationale

### 1. Logo Replacement
- **Decision**: Replaced text/placeholder logo with `champs-logo-DnrdYJcs.png`
- **Final size**: 72px height (started at 48px → bumped 20% to 58px → bumped 25% more to 72px)
- **Rationale**: Match brand identity; user iterated on size to find the right visual weight in the nav bar

### 1a. Logo Transparent Background (champs-logo-DnrdYJcs.png)
- **Decision**: Removed white background from `champs-logo-DnrdYJcs.png` using Python Pillow (in-place edit, same filename)
- **Method**: First pass used a pixel-by-pixel threshold (`r>235, g>235, b>235 → alpha=0`); second pass used edge flood-fill to catch warm/cream near-white pixels missed by the first pass
- **Cache busting**: Added `?v=2` query string to the `<img src>` in the HTML so browsers discard the cached white-background version and fetch the updated file
- **Rationale**: Original PNG had a white/cream rectangular background that was visible on the nav bar and clashed with the hero banner below

### 1b. Logo Swapped to New Logo.png
- **Decision**: Replaced `champs-logo-DnrdYJcs.png` with `New Logo.png` in the nav bar
- **Background removal**: `New Logo.png` had a light gray background (RGB ~211-213) — removed via edge flood-fill using brightness + color variation thresholds before wiring it up
- **Src in HTML**: `New%20Logo.png?v=1` (space URL-encoded; `?v=1` cache-busts the fresh file)
- **Size**: Kept at 72px height (same as previous logo)
- **Rationale**: Brand updated to new logo asset provided by user

### 2. Logo Clickable — Links to Home
- **Decision**: Wrapped logo `<img>` in an `<a>` that calls `showPage('home')` and scrolls to top
- **Rationale**: Standard UX convention — clicking the logo should always return the user to the home page regardless of which page/section they're on

### 3. Nav Menu Scroll Links
- **Decision**: All nav links (Services, How It Works, Eligibility, FAQ) use `showPage('home')` + `setTimeout(() => el.scrollIntoView({behavior:'smooth'}), 50)` with `return false` to suppress default anchor jump
- **Rationale**: The site is a single-page app with multiple "pages" (home, resources, apply). Nav links need to switch to the home page first, then scroll. The 50ms delay ensures the page switch renders before scroll fires. `return false` prevents the instant anchor jump from overriding the smooth scroll.

### 4. Banner Style — Reverted to Original
- **Decision**: Kept original banner CSS: `h1 { font-size: 48px; font-weight: 800; line-height: 1.15 }` and `p { font-size: 17px; line-height: 1.65 }`
- **Rationale**: Two responsive font approaches (CSS clamp, media queries) were tried and rejected by the user. User explicitly requested full revert to initial version. No further changes to banner typography unless re-requested.

### 5. Apply Now / Start Application Buttons
- **Decision**: All CTA buttons call `scrollToApply()` which calls `showPage('home')` then scrolls to `#apply`
- **Rationale**: Multiple buttons across the page needed a consistent way to reach the Apply for Funding section. Centralized in one function to avoid duplication.

### 6. Service Card Modals (Learn More)
- **Decision**: Each of the 5 service cards has a "Learn More" button that opens a modal overlay (`openServiceModal('type')`)
- **Services covered**: Business Loan Matching, Personal Credit Lines, Real Estate Loans, Equipment Financing, Credit Score Improvement
- **Content source**: Fetched from compiled JS bundle at `https://champsglobalservices.com/assets/index-C4VyHRrV.js` via curl/grep
- **Rationale**: Live site uses React with a compiled bundle — WebFetch couldn't extract component data directly, so raw bundle parsing was used to get accurate copy

### 7. Multi-Step Application Form (4 Steps)
- **Decision**: Apply for Funding section uses a 4-step stepper: Loan Type → Application Details → File Upload → Review & Submit
- **Step panels**: `#step1-content`, `#app-form-wrap`, `#step3-wrap`, `#step4-wrap`, `#final-success`
- **Stepper states**: `.stepper-num.inactive` / `.stepper-num.active` / `.stepper-num.done`
- **On submit**: All 4 stepper circles turn green (`.done` class applied to all)
- **Rationale**: Matches the flow on the live site; breaking into steps reduces cognitive load for applicants

### 8. Three Distinct Loan Type Forms
- **Business Loan**: Shows business info section + previous business funding history; hides RE-specific fields
- **Personal Loan**: Shows personal info section + previous personal funding history; hides business/RE fields
- **Real Estate Loan**: Shows RE-specific fields (property address, loan program dropdown, conditional Refi/Fix & Flip fields); hides personal + business sections
- **Decision**: `continueToApplication()` reads `selectedLoanType` and shows/hides sections accordingly
- **Rationale**: Each loan type has fundamentally different data requirements; a single form would be cluttered and confusing

### 9. Real Estate Conditional Fields
- **Decision**: RE loan program dropdown (`#f-loanProgram`) triggers `updateReFields()` which shows Refi fields or Fix & Flip fields based on selection
- **Rationale**: Not all RE programs require the same data; showing only relevant fields reduces friction

### 10. Input Auto-Formatting
- **Decision**: SSN formats as `XXX-XX-XXXX`, Phone as `(XXX) XXX-XXXX`, EIN as `XX-XXXXXXX` on input event
- **Rationale**: Reduces user errors and matches expected format for downstream processing

### 11. File Upload (Step 3)
- **Decision**: Drag-and-drop file upload zone with file list management; supports click-to-browse fallback
- **Rationale**: Modern UX for document submission; required documents list shown alongside upload area

### 12. Footer Legal Modals
- **Decision**: Privacy Policy, Terms of Service, and State Disclosures open in modal overlays (`openLegal('privacy')`, etc.)
- **Content source**: Fetched from live site JS bundle
- **Rationale**: Legal content needs to be accessible without navigating away; modal keeps user in context

### 13. Footer Copyright Year
- **Decision**: Updated from © 2024 to © 2026
- **Rationale**: User requested update to reflect current year

### 14. Footer Navigation Links (FAQ / How It Works / Eligibility)
- **Decision**: Footer links use the same `showPage('home')` + `scrollIntoView` pattern as nav bar links
- **Rationale**: Footer links previously had bare `href="#"` and went nowhere. Users on the Resources or Apply pages need footer links to correctly switch to home and scroll to the section.

### 15. Resources Page — Credit Bureau Links
- **Decision**: "Get Your Report" buttons link to specific credit report pages (not homepages), all with `target="_blank" rel="noopener noreferrer"`
  - TransUnion: `https://www.transunion.com/credit-report`
  - Experian: `https://www.experian.com/lp/credit-score-unbr/`
  - Equifax: `https://www.equifax.com/personal/products/credit/`
- **Rationale**: Generic homepage links weren't useful; users need to land directly on the page to request their report

---

## Architecture Notes

- **Single-file**: All HTML, CSS, and JS live in `champsglobalservices.html` — no build tools, no frameworks
- **Page switching**: `showPage(pageName)` shows/hides top-level page divs (`#home`, `#resources`, `#apply-page`)
- **Scroll pattern**: Always call `showPage()` first, then use `setTimeout(..., 50)` before `scrollIntoView` so the DOM paint completes before scrolling
- **Modal pattern**: Single overlay div reused for service modals, legal modals, and any future popups
- **CSS variables**: `--orange`, `--navy`, `--navy-dark`, `--text-dark`, `--text-mid`, `--text-light`, `--bg-light`, `--border`
- **Content sourcing**: Live site is React + Vite + Tailwind with compiled JS. Content was extracted by curling the JS bundle and grepping for relevant strings.

---

## Pending / Not Yet Done

- Contact link in footer (currently `href="#"` — no contact page or modal built yet)
- Services footer column links (Business Loan Matching, Credit Line Facilitation, etc.) — currently `href="#"`, could link to service cards or open modals
- Form submission backend — currently front-end only; no data is sent anywhere on submit
- Equipment Financing and Credit Score Improvement modals — content was attempted to be extracted from JS bundle but grep complexity limit was hit; may need manual entry

---

## Brand Name Change
- **Decision**: Replaced all instances of "Champs Global Services" with "Champs Global Solutions" across the entire HTML file (18 occurrences)
- **Scope**: Page title, logo alt text, section headings, FAQ answers, footer text, copyright line, Privacy Policy, Terms of Service, State Disclosures modals
- **Rationale**: Business rebranding requested by user

---

## Deployment — GitHub Pages
- **Repo**: `LD-botzillabot-cmd/Champs-Global` (Public)
- **File renamed**: `champsglobalservices.html` → `index.html` (GitHub Pages requires this as the entry point)
- **Steps**: Upload `index.html`, `New Logo.png`, `Banner.png` → Settings → Pages → Branch: main / root → Save → Add custom domain → Update DNS at registrar
- **DNS records needed** (at domain registrar):
  - A records: `185.199.108.153`, `.109.`, `.110.`, `.111.153` pointing to `@`
  - CNAME: `www` → `LD-botzillabot-cmd.github.io`
- **HTTPS**: Enable "Enforce HTTPS" in GitHub Pages settings after DNS propagates (up to 48hrs, usually under 1hr)

---

## Key Files
| File | Purpose |
|------|---------|
| `index.html` | Entire website — HTML + CSS + JS (renamed from `champsglobalservices.html`) |
| `New Logo.png` | Current active logo (gray background removed, used in nav bar) |
| `champs-logo-DnrdYJcs.png` | Previous logo (retired — no longer in use) |
| `Banner.png` | Reference screenshot of live site banner (used during font discussion) |
| `project_status.md` | This file — decision log and project tracker |
