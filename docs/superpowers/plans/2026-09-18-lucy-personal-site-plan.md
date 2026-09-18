# Lucy Chen Personal Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and ship a single-page personal homepage for Lucy Chen,
hand-written HTML/CSS with no build step, deployed to Cloudflare Pages.

**Architecture:** One `index.html` (five sections: hero/about/highlights/
work/contact), one `style.css`, two static assets (a resized photo and a
generated monogram favicon). No JS file — `scroll-behavior: smooth` in CSS
covers the in-page anchor nav, so `script.js` from the spec's file listing
is dropped as unneeded (YAGNI: don't ship a file with no job).

**Tech Stack:** Plain HTML5 + CSS3. Google Fonts (Fraunces, Public Sans).
Image processing via Windows' built-in `System.Drawing` (PowerShell) — no
ImageMagick/Python/npm install needed, verified working on this machine.

**Spec:** `docs/superpowers/specs/2026-09-18-lucy-personal-site-design.md`

## Global Constraints

- No build tooling, no `package.json`, no JS framework (spec: File structure)
- Palette: plum `#361731`, lilac tint `#F1E3F5`, lavender `#D0ACDA`, white
  `#FFFFFF`, body text `#2A1B2E`, secondary text `#4A3A4C`, border
  `#E3D5E8`, footer text `#9A8998` (spec: Visual design)
- Fonts: Fraunces (headings), Public Sans (body), via Google Fonts (spec:
  Visual design)
- No placeholder links: every `href` must go somewhere real. No fake
  social links in v1 (spec: Contact)
- No jargon in on-page copy — the words "responsive", "optimized", "SEO"
  must never appear in visible page text (spec: Non-functional requirements)
- Mobile breakpoint at 768px; verify actual rendering at 390px (spec:
  Non-functional requirements, Testing plan)
- Photo target: under 150KB (spec: Non-functional requirements)

---

### Task 1: HTML skeleton with final content

**Files:**
- Create: `index.html`

**Interfaces:**
- Produces: section ids `about`, `highlights`, `work`, `contact` that
  Task 2's CSS selectors and Task 6's link audit both target. Also
  produces class names `service-cards` (grid wrapper) and `service-card`
  (one per card, holding an `h3` + `p`) that Task 2's CSS styles.

- [ ] **Step 1: Write `index.html`**

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lucy Chen — Coach, Speaker, Author</title>
<link rel="stylesheet" href="style.css">
</head>
<body>
<header class="hero">
  <div class="hero-text">
    <p class="eyebrow">Coach · Speaker · Author</p>
    <h1>Lucy Chen</h1>
    <p class="mission">I help tech professionals lead with resilience so they can build high-performing teams in a rapidly changing world.</p>
    <div class="hero-buttons">
      <a href="#work" class="button button-primary">Work With Me</a>
      <a href="#highlights" class="button button-secondary">Read the Book</a>
    </div>
  </div>
  <img src="assets/lucy.jpg" alt="Lucy Chen" class="hero-photo">
</header>

<section id="about" class="section section-tint">
  <div class="content">
    <h2>About</h2>
    <p>First-generation immigrant, mother of three, and a black belt in taekwondo, Lucy spent three decades as an engineer, consultant, and senior leader in finance and tech before becoming a certified Human Potential Coach. She has since mentored more than 100 professionals, helping them build the resilience to lead through change.</p>
  </div>
</section>

<section id="highlights" class="section">
  <div class="content">
    <h2>Highlights</h2>
    <ul class="highlights-list">
      <li>Author, <em>Build Resilience: Live, Learn, and Lead</em> — International Impact Book Award &amp; PenCraft Best Book Award winner</li>
      <li>Featured speaker, SXSW 2025</li>
      <li>2025 Gracie Award honoree, audiobook narration</li>
      <li>Founder, Gifted Coaching &amp; Gifted Books</li>
    </ul>
  </div>
</section>

<section id="work" class="section section-tint">
  <div class="content">
    <h2>What I Do</h2>
    <div class="service-cards">
      <div class="service-card">
        <h3>1:1 Coaching</h3>
        <p>Personalized career and life coaching in English and Mandarin, for women and immigrants building their next chapter.</p>
      </div>
      <div class="service-card">
        <h3>Keynote Speaking</h3>
        <p>Talks on resilience and leadership, delivered everywhere from corporate stages to SXSW.</p>
      </div>
      <div class="service-card">
        <h3>Communication Training</h3>
        <p>Practical coaching to help you lead and speak with confidence.</p>
      </div>
    </div>
    <a href="https://giftedcoaching.info" class="text-link">Visit Gifted Coaching →</a>
  </div>
</section>

<section id="contact" class="section">
  <div class="content">
    <h2>Get in Touch</h2>
    <a href="mailto:lucy@giftedcoaching.info" class="email-link">lucy@giftedcoaching.info</a>
  </div>
</section>

<footer class="footer">© 2026 Lucy Chen</footer>
</body>
</html>
```

- [ ] **Step 2: Verify structure (write the check, see it fail first if you're unsure the file is absent, then create the file and re-run)**

Run:
```bash
for id in about highlights work contact; do
  grep -q "id=\"$id\"" index.html && echo "OK: $id present" || echo "MISSING: $id"
done
for card in "1:1 Coaching" "Keynote Speaking" "Communication Training"; do
  grep -qF "$card" index.html && echo "OK: $card card present" || echo "MISSING: $card"
done
grep -Eio "responsive|optimized|\bSEO\b" index.html && echo "FAIL: jargon found" || echo "OK: no jargon"
```
Expected: four `OK: <id> present` lines, three `OK: <card> card present` lines, then `OK: no jargon`.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "Add homepage HTML structure and content"
```

---

### Task 2: Base CSS — palette, typography, hero, sections

**Files:**
- Create: `style.css`

**Interfaces:**
- Consumes: class names from Task 1 (`hero`, `hero-text`, `eyebrow`,
  `mission`, `hero-buttons`, `button`, `button-primary`,
  `button-secondary`, `hero-photo`, `section`, `section-tint`, `content`,
  `highlights-list`, `service-cards`, `service-card`, `text-link`,
  `email-link`, `footer`)
- Produces: CSS custom properties (`--plum`, `--plum-text`, `--lavender`,
  `--lilac-tint`, `--border`, `--text-secondary`, `--footer-text`) that
  Task 3's media query reuses.

- [ ] **Step 1: Write `style.css`**

```css
:root {
  --plum: #361731;
  --plum-text: #2A1B2E;
  --lavender: #D0ACDA;
  --lilac-tint: #F1E3F5;
  --white: #FFFFFF;
  --border: #E3D5E8;
  --text-secondary: #4A3A4C;
  --footer-text: #9A8998;
}

* { box-sizing: border-box; }

html {
  scroll-behavior: smooth;
}

body {
  margin: 0;
  font-family: 'Public Sans', system-ui, sans-serif;
  background: var(--white);
  color: var(--plum-text);
}

h1, h2 {
  font-family: 'Fraunces', Georgia, serif;
  margin: 0;
}

a {
  color: var(--plum);
  text-decoration: none;
}

a:hover {
  color: var(--lavender);
}

.hero {
  background: var(--plum);
  padding: 96px 48px;
  display: flex;
  gap: 48px;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}

.hero-text {
  display: flex;
  flex-direction: column;
  gap: 20px;
  max-width: 560px;
}

.eyebrow {
  margin: 0;
  font-size: 14px;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: var(--lavender);
  font-weight: 600;
}

.hero h1 {
  font-size: 56px;
  font-weight: 600;
  line-height: 1.05;
  color: var(--white);
}

.mission {
  margin: 0;
  font-size: 20px;
  line-height: 1.5;
  color: #E2CEE7;
}

.hero-buttons {
  display: flex;
  gap: 16px;
  flex-wrap: wrap;
  margin-top: 8px;
}

.button {
  display: inline-block;
  padding: 14px 28px;
  border-radius: 4px;
  font-size: 16px;
  font-weight: 600;
}

.button-primary {
  background: var(--lavender);
  color: var(--plum-text);
}

.button-secondary {
  border: 1px solid var(--white);
  color: var(--white);
}

.hero-photo {
  width: 320px;
  height: 400px;
  object-fit: cover;
  border-radius: 4px;
  border: 1px solid #6B2F63;
  flex-shrink: 0;
}

.section {
  padding: 72px 24px;
}

.section-tint {
  background: var(--lilac-tint);
}

.content {
  max-width: 720px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.content h2 {
  font-size: 30px;
  font-weight: 600;
  color: var(--plum);
}

.content p {
  margin: 0;
  font-size: 18px;
  line-height: 1.7;
  color: var(--text-secondary);
}

.service-cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}

.service-card {
  padding: 24px;
  background: var(--white);
  border: 1px solid var(--border);
  border-radius: 4px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.service-card h3 {
  margin: 0;
  font-family: 'Fraunces', Georgia, serif;
  font-size: 20px;
  color: var(--plum);
}

.service-card p {
  margin: 0;
  font-size: 16px;
  line-height: 1.5;
  color: var(--text-secondary);
}

.highlights-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.highlights-list li {
  padding: 20px 0;
  border-top: 1px solid var(--border);
  font-size: 18px;
  color: var(--plum-text);
}

.highlights-list li:last-child {
  border-bottom: 1px solid var(--border);
}

.text-link {
  align-self: flex-start;
  font-weight: 600;
}

.email-link {
  font-size: 20px;
  font-weight: 600;
}

.footer {
  padding: 24px;
  text-align: center;
  font-size: 14px;
  color: var(--footer-text);
  border-top: 1px solid var(--border);
}
```

- [ ] **Step 2: Verify the palette and fonts are wired in, not just declared**

Run:
```bash
for hex in "#361731" "#D0ACDA" "#F1E3F5" "#2A1B2E"; do
  grep -qF "$hex" style.css && echo "OK: $hex used" || echo "MISSING: $hex"
done
grep -q "Fraunces" style.css && grep -q "Public Sans" style.css && echo "OK: fonts declared" || echo "MISSING: fonts"
grep -q "\.service-card " style.css && echo "OK: service-card styled" || echo "MISSING: service-card"
```
Expected: four `OK: <hex> used` lines, `OK: fonts declared`, `OK: service-card styled`.

- [ ] **Step 3: Commit**

```bash
git add style.css
git commit -m "Add base styles: palette, typography, hero and section layout"
```

---

### Task 3: Mobile breakpoint

**Files:**
- Modify: `style.css` (append)

**Interfaces:**
- Consumes: the same class names as Task 2; no new classes introduced.

- [ ] **Step 1: Append the media query to `style.css`**

```css
@media (max-width: 768px) {
  .hero {
    padding: 64px 24px;
    flex-direction: column-reverse;
    text-align: center;
  }

  .hero-text {
    align-items: center;
  }

  .hero h1 {
    font-size: 40px;
  }

  .mission {
    font-size: 18px;
  }

  .hero-buttons {
    justify-content: center;
    width: 100%;
  }

  .button {
    flex: 1;
    text-align: center;
  }

  .hero-photo {
    width: 220px;
    height: 270px;
  }

  .section {
    padding: 48px 20px;
  }

  .content h2 {
    font-size: 26px;
  }
}
```

- [ ] **Step 2: Verify the rule exists**

Run: `grep -q "@media (max-width: 768px)" style.css && echo "OK: breakpoint present"`
Expected: `OK: breakpoint present`

- [ ] **Step 3: Visual check at phone width (real verification, not just grep)**

Use the browser tool: navigate to the local `index.html` (`file://` URL),
resize the viewport to 390px wide, screenshot, and confirm: hero photo is
below the text (not beside it), buttons are full-width, the three
service cards stack into one column (via `auto-fit`/`minmax` in Task 2 —
no extra media query needed, confirm it actually reflows), no horizontal
scrollbar, no text overflowing its container.

- [ ] **Step 4: Commit**

```bash
git add style.css
git commit -m "Add mobile breakpoint at 768px"
```

---

### Task 4: Photo asset

**Files:**
- Create: `assets/lucy.jpg`

**Interfaces:**
- Produces: `assets/lucy.jpg`, referenced by `index.html`'s
  `<img class="hero-photo">` (already written in Task 1).

Source photo (Lucy's own, already public on giftedcoaching.info) is
saved locally at:
`C:\Users\lucyh\AppData\Local\Temp\claude\C--Users-lucyh-lucy-website\e1fe08ad-88af-424e-88ef-c4f64cdbcd03\scratchpad\style-canvas\lucy-photo.jpg`
(2498×1912, from `IMG_6828.jpg` on giftedcoaching.info). It needs
resizing before it ships — at full size it's 400KB+, and the spec caps
the hero photo at 150KB.

- [ ] **Step 1: Create the `assets/` directory**

```bash
mkdir -p assets
```

- [ ] **Step 2: Resize and compress with PowerShell's built-in System.Drawing (no install needed — verified working on this machine)**

```powershell
Add-Type -AssemblyName System.Drawing
$src = "C:\Users\lucyh\AppData\Local\Temp\claude\C--Users-lucyh-lucy-website\e1fe08ad-88af-424e-88ef-c4f64cdbcd03\scratchpad\style-canvas\lucy-photo.jpg"
$out = "C:\Users\lucyh\lucy-website\assets\lucy.jpg"
$img = [System.Drawing.Image]::FromFile($src)
$newW = 640
$newH = [int]($img.Height * ($newW / $img.Width))
$bmp = New-Object System.Drawing.Bitmap($newW, $newH)
$g = [System.Drawing.Graphics]::FromImage($bmp)
$g.InterpolationMode = [System.Drawing.Drawing2D.InterpolationMode]::HighQualityBicubic
$g.DrawImage($img, 0, 0, $newW, $newH)
$encoder = [System.Drawing.Imaging.ImageCodecInfo]::GetImageEncoders() | Where-Object { $_.MimeType -eq 'image/jpeg' }
$encParams = New-Object System.Drawing.Imaging.EncoderParameters(1)
$encParams.Param[0] = New-Object System.Drawing.Imaging.EncoderParameter([System.Drawing.Imaging.Encoder]::Quality, [int64]80)
$bmp.Save($out, $encoder, $encParams)
$g.Dispose(); $bmp.Dispose(); $img.Dispose()
Get-Item $out | Select-Object Name, Length
```
Expected output: `lucy.jpg`, length around 40–50KB (verified 43,636 bytes
in a dry run of this exact script).

- [ ] **Step 3: Verify size constraint**

Run:
```bash
size=$(stat -c%s assets/lucy.jpg 2>/dev/null || stat -f%z assets/lucy.jpg)
[ "$size" -lt 153600 ] && echo "OK: ${size} bytes, under 150KB" || echo "FAIL: ${size} bytes, over 150KB"
```
Expected: `OK: ... bytes, under 150KB`

- [ ] **Step 4: Commit**

```bash
git add assets/lucy.jpg
git commit -m "Add hero photo (resized and compressed)"
```

---

### Task 5: Favicon and meta tags

**Files:**
- Create: `assets/favicon.png`
- Modify: `index.html:6` (inside `<head>`, after the `<title>` line)

**Interfaces:**
- Produces: `assets/favicon.png`, referenced by a new `<link rel="icon">`
  in `index.html`.

- [ ] **Step 1: Generate a 64×64 monogram favicon with PowerShell (verified working — produces a plum circle with "LC" in lavender)**

```powershell
Add-Type -AssemblyName System.Drawing
$out = "C:\Users\lucyh\lucy-website\assets\favicon.png"
$bmp = New-Object System.Drawing.Bitmap(64,64)
$g = [System.Drawing.Graphics]::FromImage($bmp)
$g.SmoothingMode = [System.Drawing.Drawing2D.SmoothingMode]::AntiAlias
$g.TextRenderingHint = [System.Drawing.Text.TextRenderingHint]::AntiAlias
$plum = [System.Drawing.ColorTranslator]::FromHtml("#361731")
$lavender = [System.Drawing.ColorTranslator]::FromHtml("#D0ACDA")
$g.FillEllipse((New-Object System.Drawing.SolidBrush($plum)), 0, 0, 64, 64)
$font = New-Object System.Drawing.Font("Georgia", 24, [System.Drawing.FontStyle]::Bold)
$brush = New-Object System.Drawing.SolidBrush($lavender)
$sf = New-Object System.Drawing.StringFormat
$sf.Alignment = [System.Drawing.StringAlignment]::Center
$sf.LineAlignment = [System.Drawing.StringAlignment]::Center
$g.DrawString("LC", $font, $brush, (New-Object System.Drawing.RectangleF(0,0,64,64)), $sf)
$bmp.Save($out, [System.Drawing.Imaging.ImageFormat]::Png)
$g.Dispose(); $bmp.Dispose()
Get-Item $out | Select-Object Name, Length
```

- [ ] **Step 2: Add meta tags to `index.html`'s `<head>`, replacing the current single `<title>` line with:**

```html
<title>Lucy Chen — Coach, Speaker, Author</title>
<meta name="description" content="Lucy Chen helps tech professionals lead with resilience so they can build high-performing teams in a rapidly changing world. Coach, speaker, and award-winning author.">
<meta property="og:title" content="Lucy Chen — Coach, Speaker, Author">
<meta property="og:description" content="I help tech professionals lead with resilience so they can build high-performing teams in a rapidly changing world.">
<meta property="og:image" content="https://lucy-website.pages.dev/assets/lucy.jpg">
<meta property="og:type" content="website">
<link rel="icon" type="image/png" href="assets/favicon.png">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,600&family=Public+Sans:wght@400;500;600&display=swap" rel="stylesheet">
```

(If the actual `*.pages.dev` project name from Task 8 differs from
`lucy-website`, update the `og:image` URL to match before shipping —
note it here rather than guess a name that doesn't exist yet.)

- [ ] **Step 3: Verify**

Run:
```bash
for tag in "og:title" "og:description" "og:image" "rel=\"icon\"" "fonts.googleapis.com"; do
  grep -q "$tag" index.html && echo "OK: $tag" || echo "MISSING: $tag"
done
test -f assets/favicon.png && echo "OK: favicon.png exists" || echo "MISSING: favicon.png"
```
Expected: five `OK` lines.

- [ ] **Step 4: Commit**

```bash
git add index.html assets/favicon.png
git commit -m "Add favicon, meta description, and Open Graph tags"
```

---

### Task 6: Link audit

**Files:**
- Read only: `index.html`

No file changes expected — this task is a check. If it finds a problem,
fix it in `index.html` before committing.

- [ ] **Step 1: List every href and confirm none is a bare placeholder**

Run:
```bash
grep -oE 'href="[^"]*"' index.html
```
Expected output — exactly these five, nothing else:
```
href="style.css"
href="#work"
href="#highlights"
href="https://giftedcoaching.info"
href="mailto:lucy@giftedcoaching.info"
```
If any line is `href="#"` with no fragment, or points somewhere not on
this list without a reason, fix it — that's the dead-link smell the spec
explicitly ruled out.

- [ ] **Step 2: Confirm the two in-page anchors resolve to real ids**

Run:
```bash
grep -q 'id="work"' index.html && echo "OK: #work resolves"
grep -q 'id="highlights"' index.html && echo "OK: #highlights resolves"
```
Expected: both `OK` lines.

- [ ] **Step 3: Commit (only if Step 1 required a fix)**

```bash
git add index.html
git commit -m "Fix dead link found in link audit"
```

---

### Task 7: Full QA pass

**Files:** none (verification only)

- [ ] **Step 1: Open `index.html` in the browser at three widths and screenshot each**

Widths: 390px (phone), 768px (the breakpoint edge), 1280px (desktop).
Confirm at each: no horizontal scrollbar, hero photo and text don't
overlap, button text isn't clipped, highlights list is readable.

- [ ] **Step 2: Click every link and confirm the real destination**

`Work With Me` and `Read the Book` scroll to their sections; `Visit
Gifted Coaching →` opens giftedcoaching.info; the email link opens a
mail compose window addressed to `lucy@giftedcoaching.info`.

- [ ] **Step 3: Confirm fast load — no console errors, no failed network requests**

Open the browser console and the network tab while loading the page;
confirm zero errors and that total page weight (HTML + CSS + fonts +
photo + favicon) stays under 1MB.

- [ ] **Step 4: Commit only if this pass required fixes; otherwise this task has nothing to commit**

---

### Task 8: Push to GitHub

**Files:** none (this task is git/gh operations)

GitHub username confirmed: `lucygifted`. This creates a repository
visible to others — confirm the repo name and visibility (public/private)
with Lucy before running Step 2.

- [ ] **Step 1: Check `gh` is installed and authenticated**

Run: `gh auth status`
Expected: shows a logged-in account. If not authenticated, run
`gh auth login` interactively and follow its prompts before continuing.

- [ ] **Step 2: Create the repository and push (confirm name/visibility with Lucy first)**

```bash
gh repo create lucygifted/lucy-website --public --source=. --remote=origin --push
```

- [ ] **Step 3: Verify**

Run: `git remote -v` — expected: `origin` pointing at
`https://github.com/lucygifted/lucy-website.git` (or the confirmed
alternate name), for both `fetch` and `push`.

---

### Task 9: Connect Cloudflare Pages

**Files:** none — this is a one-time manual setup in the Cloudflare
dashboard. It needs Lucy's own Cloudflare login, so it can't be scripted
from here; these are the exact steps to hand her.

- [ ] **Step 1: In the Cloudflare dashboard, go to Workers & Pages → Create → Pages → Connect to Git**

- [ ] **Step 2: Select the `lucy-website` GitHub repository (authorize Cloudflare's GitHub App if prompted)**

- [ ] **Step 3: Build settings: leave "Build command" and "Build output directory" as their defaults empty/`/` — this is a static site with no build step**

- [ ] **Step 4: Deploy. Cloudflare assigns a `*.pages.dev` subdomain — note the exact name it picks**

- [ ] **Step 5: If the assigned subdomain differs from `lucy-website.pages.dev`, go back to `index.html` and update the `og:image` URL from Task 5 to match, then commit and push — the auto-deploy picks it up**

- [ ] **Step 6: Confirm: open the `*.pages.dev` URL, repeat Task 7's QA pass against the live site (not just the local file)**
