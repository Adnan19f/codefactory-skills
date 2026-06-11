
---
name: mixbox-social-promo
description: Create professional social media promotional images for MixBox (mixbox.ba) — a Bosnian XXL fashion store. Use when asked to create a Facebook/Instagram post, promo image, carousel, product highlight, or any static visual content for MixBox social media. Supports: text-only post, image-only post, text + image post, and carousel (up to 6 images grid). Output: HTML artifact that renders as a downloadable PNG-ready canvas, or a PNG file.
---

# MixBox Social Media Promo Skill

Creates on-brand promotional images for MixBox social media channels (Facebook, Instagram).

---

## BRAND IDENTITY

### Colors
- **Background:** Deep dark gradient `#1a1a1a → #0d0d0d` (near black)
- **Primary Red:** `#E8272A` (MixBox red — used for logo, accents, highlights)
- **White:** `#FFFFFF` (headlines, logo text)
- **Light Grey:** `#CCCCCC` (body text, subtitles)
- **Accent Orange:** `#FF6B35` (optional warm accent for price callouts)

### Typography
- **Headline font:** Bold/Black weight sans-serif — use Google Fonts: `Barlow Condensed` (700/900) or `Oswald` (700)
- **Body font:** `Inter` or `Barlow` (400/600)
- All headings UPPERCASE
- Headline size: 60–90px on 1080px canvas
- Sub-headline: 28–36px
- Body: 20–24px

### Logo
Render the MixBox logo in the header as:
```
[M↓ icon box in red]  MixBox
```
SVG icon: a square with rounded corners, red fill, white "M" arrow-down symbol inside.
Text: "Mix" in white + "Box" in red, bold.

### Layout Structure (every post)
```
┌─────────────────────────────────┐
│  HEADER: Logo + page/category   │  ~15% height
├─────────────────────────────────┤
│                                 │
│  MIDDLE: Main content           │  ~70% height
│  (text, images, or both)        │
│                                 │
├─────────────────────────────────┤
│  FOOTER: Contact info           │  ~15% height
└─────────────────────────────────┘
```

### Footer (always present)
```
📞 +387 62 631 517   🌐 mixbox.ba   [OLX icon] mixbox.olx.ba
```
Dark semi-transparent bar, icons + text in white/grey.

---

## POST FORMATS

### Format 1: TEXT ONLY
- Full-bleed dark background with subtle texture or gradient
- Large headline (up to 3 lines) in white/red
- Supporting body text in light grey
- Optional: decorative red geometric accent line or shape
- Use case: announcements, promotions, quotes

### Format 2: IMAGE ONLY  
- One product image fills ~80% of canvas
- Subtle dark vignette overlay at top and bottom
- Header and footer overlay on top of image
- Product name or category as minimal text over image

### Format 3: TEXT + IMAGE (Primary format)
- Left or right: product image(s) ~50% width
- Opposite side: headline + body text + optional CTA badge
- Red accent divider line between text and image
- CTA badge: rounded pill shape, red fill, white text ("Naruči sad!", "Sazaj više", price)

### Format 4: CAROUSEL / GRID (up to 6 images)
- Single canvas showing 2×3 or 3×2 grid of product images
- Thin red lines as grid separators
- Optional category label overlay on each cell
- Suitable for "kolekcija" or "novi proizvodi" posts
- If fewer than 6 images provided: fill remaining cells with brand gradient tile

---

## CANVAS DIMENSIONS

| Platform | Size | Ratio |
|----------|------|-------|
| Instagram Post | 1080×1080 | 1:1 |
| Instagram Story / Reel cover | 1080×1920 | 9:16 |
| Facebook Post | 1200×628 | 1.91:1 |
| Default (universal) | 1080×1080 | 1:1 |

Always default to **1080×1080** unless user specifies otherwise.

---

## CONTENT DEFAULTS

When no specific text is provided, use these template strings:

**Headline options:**
- `XXL MODA` / `KAMOA FASHION` / `RADNA ODJEĆA`
- `OD 3XL DO 10XL` 
- `SVE ŠTO TREBAŠ NA JEDNOM MJESTU`

**Sub-headline options:**
- `Moderna odjeća za svaki okaz`
- `Premium kvaliteta, prihvatljive cijene`
- `Novo u ponudi`

**CTA options:**
- `Naruči online →`
- `Pogledaj kolekciju`
- `Dostupno na mixbox.ba`

---

## IMPLEMENTATION

### Step 1: Gather inputs
Ask the user (or infer from context):
1. **Format** — text-only / image-only / text+image / carousel
2. **Images** — attached product photos (if applicable)
3. **Headline text** — or use default
4. **Body text** — or use default
5. **Category** — XXL moda / Kamoa fashion / Radna odjeća / general
6. **Canvas size** — default 1080×1080

### Step 2: Build the HTML canvas

Create an HTML file using a `<canvas>` element (1080×1080) or a styled `<div>` at full size. Use JavaScript to draw all elements onto canvas, then expose a download button.

**Key rendering rules:**
- Use `document.fonts.ready` before drawing to ensure custom fonts loaded
- Load Google Fonts in `<head>`: Barlow Condensed + Inter
- For user-uploaded images: create `Image()` objects, `drawImage()` onto canvas
- Apply `ctx.filter = 'brightness(0.7)'` for vignette on image-behind-text areas
- Red divider lines: `ctx.strokeStyle = '#E8272A'; ctx.lineWidth = 4`
- Footer bar: `ctx.fillStyle = 'rgba(0,0,0,0.7)'`

**Download button pattern:**
```javascript
document.getElementById('download').addEventListener('click', () => {
  const link = document.createElement('a');
  link.download = 'mixbox-post.png';
  link.href = canvas.toDataURL('image/png');
  link.click();
});
```

### Step 3: Render MixBox Logo SVG

Embed this logo SVG in the header area:
```html
<svg viewBox="0 0 200 50" xmlns="http://www.w3.org/2000/svg">
  <!-- Icon box -->
  <rect x="0" y="5" width="40" height="40" rx="6" fill="#E8272A"/>
  <!-- Arrow-M symbol -->
  <polyline points="10,15 20,28 30,15" stroke="white" stroke-width="4" fill="none" stroke-linecap="round"/>
  <line x1="20" y1="28" x2="20" y2="38" stroke="white" stroke-width="4" stroke-linecap="round"/>
  <!-- Text -->
  <text x="48" y="34" font-family="Barlow Condensed, sans-serif" font-weight="700" font-size="28" fill="white">Mix</text>
  <text x="84" y="34" font-family="Barlow Condensed, sans-serif" font-weight="700" font-size="28" fill="#E8272A">Box</text>
</svg>
```

### Step 4: Quality checks before output
- [ ] Header logo visible and correctly proportioned
- [ ] All text within canvas boundaries (min 30px margin)
- [ ] Footer bar always at bottom
- [ ] No text overlapping with busy image areas without contrast background
- [ ] Red accents used consistently (not overused — max 2–3 red elements)
- [ ] Font loaded before canvas draw (use `fonts.ready` promise)

---

## OUTPUT

- Primary: React artifact (`.jsx`) with live canvas preview + PNG download button
- Alternative: Static `.html` file to `/mnt/user-data/outputs/mixbox-post.html`
- If images were uploaded: process them with `FileReader` → `Image()` → `canvas.drawImage()`

---

## EXAMPLE PROMPTS THAT TRIGGER THIS SKILL

- "Napravi Instagram post za MixBox XXL modu"
- "Promo slika za novu Kamoa kolekciju"
- "Karusel sa 4 proizvoda za Facebook"
- "Napravi post samo sa tekstom za akciju -20%"
- "MixBox promo image with product photos"
