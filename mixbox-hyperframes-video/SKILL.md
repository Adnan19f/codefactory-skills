
---
name: mixbox-hyperframes-video
description: Create short animated promotional videos for MixBox (mixbox.ba) using HeyGen HyperFrames — an HTML-to-MP4 rendering framework. Use when asked to create a short video, animated post, Reels/Stories video, or any motion content for MixBox social media. Output: an index.html composition file ready for `npx hyperframes render`. Requires Node 22+ and FFmpeg installed locally.
---

# MixBox HyperFrames Video Skill

Creates short animated promotional videos (15–30 seconds) for MixBox social media using HeyGen HyperFrames.

---

## WHAT IS HYPERFRAMES

HyperFrames turns plain HTML/CSS/JS into deterministic MP4 videos.

**How it works:**
1. You write an `index.html` composition file
2. Headless Chrome seeks through each frame
3. FFmpeg encodes it to MP4

**Key HTML conventions:**
- `data-start="N"` — element appears at frame N
- `data-duration="N"` — element is visible for N frames
- Default: **30 fps**
- So `data-start="30"` = appears at 1 second; `data-duration="90"` = lasts 3 seconds
- The `<body>` itself sets the total composition length via CSS `--duration` variable or through the last element's end point

**No React needed.** Plain HTML + CSS animations (or GSAP, Anime.js, Lottie).

**CLI commands (user runs these):**
```bash
# Install
npx hyperframes init my-mixbox-video
cd my-mixbox-video

# Preview in browser (real-time)
npx hyperframes preview

# Render to MP4
npx hyperframes render --output mixbox-promo.mp4

# Validate structure
npx hyperframes lint
npx hyperframes validate
```

---

## BRAND IDENTITY (same as social promo skill)

- **Background:** `#1a1a1a` dark
- **Primary Red:** `#E8272A`
- **White:** `#FFFFFF`
- **Font:** Barlow Condensed (700/900) for headlines, Inter for body
- **Logo:** M-icon box (red) + "MixBox" text
- **Footer contact:** +387 62 631 517 | mixbox.ba | mixbox.olx.ba

---

## VIDEO FORMATS

### Format A: Product Showcase Slideshow
Sequence of product cards sliding in/out.
- Duration: 15–20 seconds (450–600 frames at 30fps)
- Each card: 3–4 seconds
- Transition: slide-left or fade
- Content per card: product image + name + price

### Format B: Brand Intro + Category Reveal  
Cinematic brand opener followed by category reveals.
- Duration: 20–30 seconds
- Frame 1 (0–90): MixBox logo animation (scale-in + glow)
- Frame 91–180: Category title (e.g., "XXL MODA") slides in
- Frame 181–360: Product grid fades in
- Frame 361–end: CTA + contact footer

### Format C: Promo Announcement
Urgent promotional announcement.
- Duration: 10–15 seconds
- Bold text animation: headline drops in
- Price/discount badge bounces in
- Countdown or urgency element (optional)

### Format D: Story/Reel Vertical (9:16)
Vertical format for Instagram Stories or Reels.
- Canvas: 1080×1920
- Swipe-up style text reveal from bottom
- Full-bleed product image background

---

## COMPOSITION STRUCTURE

### Canvas size
```css
body {
  width: 1080px;
  height: 1080px;   /* or 1920px for Story/Reel */
  margin: 0;
  overflow: hidden;
  background: #1a1a1a;
  font-family: 'Barlow Condensed', sans-serif;
}
```

### Three-zone layout
```html
<!-- HEADER: Logo + category label -->
<div class="header" data-start="0" data-duration="600">...</div>

<!-- MAIN: Animated content area -->
<div class="main" data-start="15" data-duration="570">...</div>

<!-- FOOTER: Contact bar -->
<div class="footer" data-start="30" data-duration="560">...</div>
```

---

## ANIMATION TECHNIQUES

### CSS Keyframe slide-in (no external deps)
```css
@keyframes slideInLeft {
  from { transform: translateX(-120%); opacity: 0; }
  to   { transform: translateX(0);     opacity: 1; }
}

@keyframes fadeInScale {
  from { transform: scale(0.8); opacity: 0; }
  to   { transform: scale(1);   opacity: 1; }
}

@keyframes slideUp {
  from { transform: translateY(60px); opacity: 0; }
  to   { transform: translateY(0);    opacity: 1; }
}
```

### HyperFrames seekable timing with CSS
```css
/* Use animation-delay tied to frame timing */
.headline {
  animation: slideInLeft 0.4s ease forwards;
  animation-delay: 0.5s;    /* = frame 15 at 30fps */
  opacity: 0;
}
```

### GSAP (optional, more control)
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
<script>
  // HyperFrames exposes window.__currentFrame for seekable animations
  gsap.registerPlugin();
  
  const tl = gsap.timeline();
  tl.from('.logo', { scale: 0, opacity: 0, duration: 0.5, ease: 'back.out' })
    .from('.headline', { x: -100, opacity: 0, duration: 0.4 }, '+=0.2')
    .from('.product-grid', { y: 50, opacity: 0, stagger: 0.1 }, '+=0.3')
    .from('.footer', { y: 30, opacity: 0, duration: 0.3 }, '+=0.5');
</script>
```

### Seekable animation (required for deterministic render)
HyperFrames seeks frames non-linearly. Make all animations seekable:

```javascript
// BAD: Wall-clock setTimeout
setTimeout(() => show(element), 1000);

// GOOD: requestAnimationFrame or GSAP timeline (both work with HyperFrames' beginFrame API)
// GSAP timelines are automatically seekable
// CSS animations with animation-delay are seekable

// If using custom JS animation, use GSAP or the Web Animations API:
element.animate([
  { opacity: 0, transform: 'translateY(40px)' },
  { opacity: 1, transform: 'translateY(0)' }
], { duration: 400, delay: 1000, fill: 'both' });
```

---

## FULL EXAMPLE: Product Showcase (Format A)

```html
<!DOCTYPE html>
<html lang="bs">
<head>
  <meta charset="UTF-8">
  <meta name="hyperframes-fps" content="30">
  <meta name="hyperframes-duration" content="600"><!-- 20 seconds -->
  
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@700;900&family=Inter:wght@400;600&display=swap" rel="stylesheet">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
  
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    
    body {
      width: 1080px;
      height: 1080px;
      overflow: hidden;
      background: linear-gradient(160deg, #1a1a1a 0%, #0d0d0d 100%);
      font-family: 'Barlow Condensed', sans-serif;
      color: #fff;
    }

    /* ── HEADER ── */
    .header {
      position: absolute;
      top: 0; left: 0; right: 0;
      height: 140px;
      background: rgba(0,0,0,0.6);
      display: flex;
      align-items: center;
      padding: 0 48px;
      gap: 16px;
      border-bottom: 3px solid #E8272A;
    }

    .logo-icon {
      width: 52px; height: 52px;
    }

    .logo-text {
      font-size: 42px;
      font-weight: 900;
      letter-spacing: -1px;
    }
    .logo-text span { color: #E8272A; }

    .category-pill {
      margin-left: auto;
      background: #E8272A;
      color: white;
      padding: 8px 24px;
      border-radius: 999px;
      font-size: 22px;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 1px;
    }

    /* ── PRODUCT CARDS ── */
    .card-track {
      position: absolute;
      top: 140px;
      left: 0; right: 0;
      height: 820px;
      display: flex;
    }

    .card {
      position: absolute;
      top: 0; left: 0;
      width: 1080px; height: 820px;
      display: flex;
      align-items: center;
      padding: 60px;
      gap: 60px;
      opacity: 0;
    }

    .card-image {
      width: 460px;
      height: 600px;
      object-fit: cover;
      border-radius: 16px;
      border: 3px solid rgba(232,39,42,0.4);
      flex-shrink: 0;
    }

    .card-image-placeholder {
      width: 460px;
      height: 600px;
      background: linear-gradient(135deg, #2a2a2a, #1a1a1a);
      border-radius: 16px;
      border: 3px solid rgba(232,39,42,0.4);
      display: flex;
      align-items: center;
      justify-content: center;
      color: #444;
      font-size: 18px;
      flex-shrink: 0;
    }

    .card-content {
      flex: 1;
    }

    .card-tag {
      display: inline-block;
      background: rgba(232,39,42,0.15);
      border: 1px solid #E8272A;
      color: #E8272A;
      font-size: 18px;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 2px;
      padding: 6px 18px;
      border-radius: 4px;
      margin-bottom: 20px;
    }

    .card-title {
      font-size: 72px;
      font-weight: 900;
      line-height: 1;
      text-transform: uppercase;
      margin-bottom: 16px;
    }

    .card-sizes {
      font-size: 26px;
      color: #aaa;
      font-weight: 600;
      margin-bottom: 28px;
    }

    .card-price {
      font-size: 52px;
      font-weight: 900;
      color: #E8272A;
      margin-bottom: 36px;
    }

    .card-cta {
      display: inline-block;
      background: #E8272A;
      color: white;
      font-size: 26px;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 1px;
      padding: 18px 48px;
      border-radius: 8px;
    }

    /* ── FOOTER ── */
    .footer {
      position: absolute;
      bottom: 0; left: 0; right: 0;
      height: 100px;
      background: rgba(0,0,0,0.75);
      border-top: 2px solid #333;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 60px;
    }

    .footer-item {
      display: flex;
      align-items: center;
      gap: 10px;
      font-family: 'Inter', sans-serif;
      font-size: 22px;
      color: #ccc;
    }

    .footer-dot {
      width: 6px; height: 6px;
      background: #E8272A;
      border-radius: 50%;
    }

    /* ── PROGRESS BAR ── */
    .progress {
      position: absolute;
      bottom: 100px;
      left: 0;
      height: 4px;
      background: #E8272A;
      transform-origin: left;
    }
  </style>
</head>
<body>

  <!-- HEADER -->
  <div class="header" id="header">
    <svg class="logo-icon" viewBox="0 0 52 52" xmlns="http://www.w3.org/2000/svg">
      <rect width="52" height="52" rx="8" fill="#E8272A"/>
      <polyline points="12,16 26,30 40,16" stroke="white" stroke-width="5" fill="none" stroke-linecap="round" stroke-linejoin="round"/>
      <line x1="26" y1="30" x2="26" y2="44" stroke="white" stroke-width="5" stroke-linecap="round"/>
    </svg>
    <div class="logo-text">Mix<span>Box</span></div>
    <div class="category-pill">XXL Moda</div>
  </div>

  <!-- CARDS (add more as needed) -->
  <div class="card-track">
    
    <div class="card" id="card1">
      <div class="card-image-placeholder">[ Product Image 1 ]</div>
      <div class="card-content">
        <div class="card-tag">Novo u ponudi</div>
        <div class="card-title">Trenerka Premium</div>
        <div class="card-sizes">Veličine: 3XL – 10XL</div>
        <div class="card-price">KM 80,00</div>
        <div class="card-cta">Naruči online →</div>
      </div>
    </div>

    <div class="card" id="card2">
      <div class="card-image-placeholder">[ Product Image 2 ]</div>
      <div class="card-content">
        <div class="card-tag">Rasprodaja</div>
        <div class="card-title">Majica Premium</div>
        <div class="card-sizes">Veličine: 3XL – 10XL</div>
        <div class="card-price">KM 45,00</div>
        <div class="card-cta">Pogledaj kolekciju</div>
      </div>
    </div>

    <div class="card" id="card3">
      <div class="card-image-placeholder">[ Product Image 3 ]</div>
      <div class="card-content">
        <div class="card-tag">Popularno</div>
        <div class="card-title">Bermude Premium</div>
        <div class="card-sizes">Veličine: 3XL – 10XL</div>
        <div class="card-price">KM 70,00</div>
        <div class="card-cta">Dostupno na mixbox.ba</div>
      </div>
    </div>

  </div>

  <!-- FOOTER -->
  <div class="footer" id="footer">
    <div class="footer-item">📞 +387 62 631 517</div>
    <div class="footer-dot"></div>
    <div class="footer-item">🌐 mixbox.ba</div>
    <div class="footer-dot"></div>
    <div class="footer-item">mixbox.olx.ba</div>
  </div>

  <!-- PROGRESS BAR -->
  <div class="progress" id="progress"></div>

  <script>
    // GSAP timeline — fully seekable (required for HyperFrames deterministic render)
    const totalFrames = 600; // 20s at 30fps
    const tl = gsap.timeline({ defaults: { ease: 'power2.out' } });

    // Header entrance
    tl.from('#header', { y: -140, opacity: 0, duration: 0.5 }, 0)
    
    // Footer entrance
      .from('#footer', { y: 100, opacity: 0, duration: 0.5 }, 0.2)

    // Card 1: frames 0–190 (0–6.3s)
      .to('#card1', { opacity: 1, duration: 0.01 }, 0.3)
      .from('#card1 .card-image-placeholder', { x: -80, opacity: 0, duration: 0.5 }, 0.3)
      .from('#card1 .card-content > *', { y: 30, opacity: 0, duration: 0.4, stagger: 0.1 }, 0.5)
      .to('#card1', { x: -1200, opacity: 0, duration: 0.5 }, 5.5)

    // Card 2: frames 190–390 (6.3–13s)
      .fromTo('#card2', { x: 1200, opacity: 0 }, { x: 0, opacity: 1, duration: 0.5 }, 6)
      .from('#card2 .card-content > *', { y: 30, opacity: 0, duration: 0.4, stagger: 0.1 }, 6.3)
      .to('#card2', { x: -1200, opacity: 0, duration: 0.5 }, 11.5)

    // Card 3: frames 390–570 (13–19s)
      .fromTo('#card3', { x: 1200, opacity: 0 }, { x: 0, opacity: 1, duration: 0.5 }, 12)
      .from('#card3 .card-content > *', { y: 30, opacity: 0, duration: 0.4, stagger: 0.1 }, 12.3)

    // Progress bar
    gsap.to('#progress', { 
      width: '100%', 
      duration: totalFrames / 30, 
      ease: 'linear' 
    });
  </script>

</body>
</html>
```

---

## IMPLEMENTATION STEPS

### Step 1: Determine video content
Ask or infer:
1. **Format** — A/B/C/D (see above)
2. **Duration** — 10 / 15 / 20 / 30 seconds
3. **Canvas** — 1080×1080 (square) or 1080×1920 (story/reel)
4. **Products to feature** — names, prices, images (attached or placeholder)
5. **Category** — XXL moda / Kamoa / Radna odjeća / general

### Step 2: Write index.html
- Use the full example above as base template
- Replace placeholder cards with actual product content
- If images provided: `<img src="./image1.jpg">` (user places in same folder)
- Adjust `totalFrames` to match desired duration (frames = seconds × 30)
- Add/remove cards as needed

### Step 3: Validate structure
Include this comment at top of file so user knows what to run:
```html
<!-- 
  HYPERFRAMES COMPOSITION
  
  To preview:  npx hyperframes preview
  To lint:     npx hyperframes lint  
  To validate: npx hyperframes validate
  To render:   npx hyperframes render --output mixbox-promo.mp4
  
  Requirements: Node 22+, FFmpeg
  Install: npm install -g hyperframes (or use npx)
-->
```

### Step 4: Output
- Save as `index.html` to `/mnt/user-data/outputs/mixbox-video/index.html`
- If product images were uploaded, save them alongside: `/mnt/user-data/outputs/mixbox-video/images/`
- Also provide the render command and instructions

---

## ANIMATION BEST PRACTICES FOR HYPERFRAMES

1. **Always use GSAP timelines or CSS animations** — never `setTimeout`/`setInterval`
2. **`gsap.set()` for initial states** before the timeline starts
3. **Test seekability**: timeline should play correctly if HyperFrames seeks to frame 150 first
4. **Google Fonts**: add `display=swap` and `font-display: swap` — HyperFrames waits for fonts
5. **Images must be local** or absolute HTTPS URLs (no relative blob: URLs)
6. **Stagger animations** make card reveals feel more polished

---

## EXAMPLE PROMPTS THAT TRIGGER THIS SKILL

- "Napravi kratki video za MixBox Instagram"  
- "Animirani promo za novu kolekciju"
- "Reel za MixBox Kamoa fashion"
- "MixBox story video sa 3 proizvoda"
- "Napravi Hyperframes video za MixBox"
- "Short video ad for MixBox social media"
