# QA Report: Paola Ramirez

**Date:** 2026-02-11
**URL:** https://cofoundy.github.io/portfolio-paola-ramirez/
**Status:** FAIL

## Data Validation
- [x] Name matches source (Sheet: "Paola Ramirez", Page: "Paola Ramirez")
- [x] Email matches source (Sheet: "paolarago2@gmail.com", Page: "paolarago2@gmail.com")
- [x] Job title derived from CV/Research (not invented)
- [x] All 8 companies match CV/Research exactly
- [x] All 4 education entries match CV/Research
- [x] All dates match source data
- [x] No hallucinated data detected

## Clean Deploy
- [x] No "Powered by" / "Made with" / "Built with" visible text
- [x] No "Lorem ipsum" / "Your name here" / "[placeholder]" text
- [x] No template watermarks visible to users
- [x] No "undefined" or "null" visible in rendered content
- [x] No broken links showing "#" or "javascript:void(0)" as text
- [ ] **FAIL: Footer renders Twitter and GitHub icons without href** (ghost links with no destination)

## Technical
- [x] CSS loads (HTTP 200) — `_astro/index.CiffQ9-D.css`
- [x] Favicon loads (HTTP 200) — `favicon.svg`
- [x] No profile image referenced in hero (text-only design, no broken images)
- [ ] **FAIL: HTML `<strong>` tags escaped in Education degree fields** — rendered as literal text

## Mobile / Responsiveness
- [x] Template uses responsive Tailwind breakpoints throughout (sm/md/lg/xl)
- [x] Hero uses responsive text sizing (text-4xl through lg:text-8xl)
- [ ] No mobile hamburger menu (header hidden on mobile, scroll-only navigation)
  - Acceptable for minimal-mono single-page template design

## Console Errors
- N/A (Chrome MCP unavailable during this session)

## Issues Found

### Issue 1: ESCAPED HTML IN EDUCATION DEGREES (SEVERITY: HIGH)
**Location:** Education section — all 4 degree entries
**Files:** `src/config.ts` lines 122-128, `src/components/Education.astro` line 28

**Problem:** The `config.ts` education degree fields contain `<strong>` HTML tags:
```
"<strong>Maestria</strong> en Gerencia de Tecnologias de la Informacion"
"<strong>MBA</strong> - Maestria en Administracion Estrategica de Empresas"
"Programa Avanzado en <strong>Big Data & Analytics</strong>"
```
The `Education.astro` component renders these with `{edu.degree}` (Astro's default text interpolation), which HTML-escapes the tags. Users see literal `<strong>` and `</strong>` text on screen.

**Fix (option A — component):** In `Education.astro` line 28, change `{edu.degree}` to:
```astro
<Fragment set:html={edu.degree} />
```

**Fix (option B — config):** Remove `<strong>` tags from the degree strings in config.ts since the h3 heading already provides visual emphasis.

---

### Issue 2: GHOST SOCIAL LINKS IN FOOTER (SEVERITY: MEDIUM)
**Location:** Footer — Twitter (X) and GitHub icons
**File:** `src/components/Footer.astro` lines 73-120

**Problem:** Footer.astro unconditionally renders all 4 social icons (email, linkedin, twitter, github). Paola's config only defines email and linkedin. The Twitter and GitHub `<a>` tags render with `href={undefined}`, creating clickable icons that lead nowhere.

**Fix:** Wrap Twitter and GitHub links with conditional checks:
```astro
{siteConfig.social?.twitter && (
  <a href={siteConfig.social.twitter} ...>Twitter SVG</a>
)}
{siteConfig.social?.github && (
  <a href={siteConfig.social.github} ...>GitHub SVG</a>
)}
```

---

### Issue 3: MISSING ACCENTS IN SPANISH UI LABELS (SEVERITY: LOW)
**Location:** Header, About section heading, Education section heading, Footer nav
**Files:** `Header.astro`, `About.astro`, `Education.astro`, `Footer.astro`

**Problem:** Two section labels are missing Spanish accent marks:
- "Educacion" should be "Educacion" (with accent on the o)
- "Sobre Mi" should be "Sobre Mi" (with accent on the i)

These appear in the header nav, section headings, and footer nav (6 occurrences total).

**Fix:** Replace "Educacion" with the properly accented form and "Sobre Mi" with the properly accented form in all component files.

---

## Evidence
- Full page HTML captured and analyzed via curl
- HTTP status codes: CSS (200), Favicon (200)
- Source files inspected: Education.astro, Footer.astro, Header.astro, About.astro, config.ts
- Screenshots not captured (Chrome MCP server unavailable)
