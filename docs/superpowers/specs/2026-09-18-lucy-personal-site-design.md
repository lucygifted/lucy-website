# Lucy Chen personal site — v1 design

## Purpose

A single-page personal homepage for Lucy Chen: who she is, what she does,
representative work, contact. Deployed to Cloudflare Pages. v1 goal is to
ship the smallest complete version — not every idea in this doc.

## Content (final copy for v1)

**Hero**
- Eyebrow: "Coach · Speaker · Author"
- H1: "Lucy Chen"
- Mission line: "I help tech professionals lead with resilience so they
  can build high-performing teams in a rapidly changing world."
- Buttons: "Work With Me" (→ `#work`), "Read the Book" (→ `#highlights`)
- Photo: Lucy's existing headshot (reused from giftedcoaching.info,
  downloaded to `assets/lucy.jpg`, resized/compressed for web)

**About**
> First-generation immigrant, mother of three, and a black belt in
> taekwondo, Lucy spent three decades as an engineer, consultant, and
> senior leader in finance and tech before becoming a certified Human
> Potential Coach. She has since mentored more than 100 professionals,
> helping them build the resilience to lead through change.

**Highlights** (four lines, no icons)
1. Author, *Build Resilience: Live, Learn, and Lead* — International
   Impact Book Award & PenCraft Best Book Award winner
2. Featured speaker, SXSW 2025
3. 2025 Gracie Award honoree, audiobook narration
4. Founder, Gifted Coaching & Gifted Books

**What I Do**
> Through Gifted Coaching, Lucy offers one-on-one coaching, keynote
> speaking, and communication training in English and Mandarin — for
> women, immigrants, and professionals building their next chapter.

CTA: "Visit Gifted Coaching →" → `https://giftedcoaching.info`

**Contact**
- `lucy@giftedcoaching.info` (mailto link) — the only contact method in
  v1. No placeholder social links: a fake `#` link is worse than no
  link. Real LinkedIn/Instagram URLs go in when she gives them
  (v2 backlog item, not a blocker).

**Footer**: `© 2026 Lucy Chen`

## Visual design (approved via mockup)

- Layout: single page, five sections top to bottom, anchor-linked from
  hero buttons.
- Palette (sampled live from giftedcoaching.info's actual rendered
  colors, not invented): deep plum `#361731` (hero background), pale
  lilac `#F1E3F5` (alternating section stripes — About, What I Do),
  white `#FFFFFF` (Highlights, Contact), lavender `#D0ACDA` (primary
  button), near-black plum `#2A1B2E` (body text).
- Type: **Fraunces** (serif, headings) + **Public Sans** (sans, body),
  loaded from Google Fonts.
- Hero photo: 380×460, object-fit cover, right side on desktop.

## Non-functional requirements (not optional, not v2)

- **Mobile-first responsive**: one CSS breakpoint (~768px) that stacks
  the hero (photo below text), reduces heading sizes, and full-widths
  the buttons. Test at 390px wide before calling it done.
- **Fast load**: no JS framework, no build step, one compressed photo
  (target < 150KB), system fallback fonts while Google Fonts loads.
- **No jargon in the UI**: nothing like "responsive design" or
  "optimized" appears in on-page copy — plain English only.

## Explicitly out of scope for v1 (backlog, not forgotten)

These were flagged as gaps against best-in-class personal-brand sites,
but v1 ships without them — add only after the simple version is live:

- Testimonial quote (real one exists on giftedcoaching.info, can be
  reused later)
- Book cover image + direct buy link
- "As featured at" press/logo strip (SXSW, Gracie Award, Toastmasters)
- Real LinkedIn/Instagram links in Contact
- Cloudflare Web Analytics (free, privacy-friendly, one script tag —
  cheap to add once the page is live)

v1 still includes, because these are near-zero cost and the page is
incomplete without them: `<title>`, meta description, favicon, and
Open Graph tags (title/description/image) so shared links don't look
broken.

## File structure

```
lucy-website/
  index.html
  style.css
  script.js       (smooth-scroll for anchor nav only — optional, add
                    only if plain CSS `scroll-behavior: smooth` isn't
                    enough)
  assets/
    lucy.jpg
    favicon.png
  docs/superpowers/specs/   (this file)
```

No build tooling, no `package.json`, no framework.

## Deployment

1. Git repo already initialized locally (`main` branch).
2. Push to a new GitHub repository.
3. Connect that repo in Cloudflare Pages (no build command — static
   files served as-is).
4. Every push to `main` auto-deploys to `<project>.pages.dev`.
5. Custom domain: none yet: bind later when Lucy owns one, no code
   changes needed.

## Testing plan

- Open `index.html` directly and at each breakpoint (390px, 768px,
  1280px) in a real browser before shipping.
- Click every link (`#work`, `#highlights`, mailto, Gifted Coaching
  external link) and confirm it goes somewhere real.
- Lighthouse or equivalent: confirm fast load, no console errors.
