# SAFFRA DESIGN — Scroll-motion creative studio landing page (rebuild command)

Build exactly the page described below. Every value is a measurement. Do not interpret, restyle or "improve" anything. §2 gives the complete page source: copy it character for character into `index.html`. The sections after it explain what each part does and list what you must not change.

**How to use this command:** §2 is the only build step. §0–§1.5 and §3–§14 are reference and verification only: they describe what the §2 code already does. Do not "apply" them as edits, do not refactor the code to match them, and do not replace literal values in §2 with tokens. The source is about 130 KB; extract the §2 code block programmatically (from `<!DOCTYPE html>` to `</html>`) instead of retyping it, so no character changes.

**Assets (Path A, hosted):** 25 WebP photos on a public CDN, listed in §10 with their exact URLs (all respond `200 image/webp`, CORS `*`). Use those URLs exactly as written. Do not download them into the project, do not replace them, do not add other images. One Google Fonts stylesheet (Inter Tight, variable weight 300–700). Libraries, from these exact CDN URLs only: GSAP 3.12.5, ScrollTrigger 3.12.5 and Lenis 1.1.13. No React, no Tailwind, no icon font, no SplitText (the word splitter is a small function inside the page). The logo, icons, asterisk and quote mark are inline SVG. The hero water effect is raw WebGL2 (no library).

**What it is:** a one-page site for a creative strategy studio called Saffra Design, in a warm palette: near-black `#15120c`, cream `#f2ede3`, paper `#e9e2d3` and a pastel mustard accent (`#c9a45c`, text-safe `#7a5c1f`, soft `#e6d3a1`). Everything happens while you scroll, with Lenis smooth scrolling driving GSAP ScrollTrigger:
1. **Loader (≈1.8s):** the logo draws its three strokes, a 0→100 counter and a gold bar fill, then the loader wipes upward.
2. **Hero (100svh, pinned while the next section slides over it):** a full-bleed photo of a woman in profile at sunset on a golden hillside, half hidden behind a tree trunk. Left: **CLEAR IDEAS, / LASTING IMPACT.** Right: **CREATIVE / STRATEGY / STUDIO**, a short paragraph and a labelled "Start a project" button. Bottom left: a cream stat card (**94%** Client retention) and a mustard glass card whose text changes with five pill tabs at the bottom right. **The portrait is a liquid surface:** moving the pointer or clicking sends ripples that bend only the photo (refraction, a slight colour split and a warm highlight); text and cards stay sharp.
3. **About (cream, rounded top 32px, slides over the hero):** a centered 4-line title whose words fill with the scroll, with a mustard toggle pill whose photo knob slides across; a rotating asterisk; three photos with a clip reveal and parallax.
4. **Story + services:** a 2-line title and four dark service cards with 3D tilt, hover zoom, hidden description and a gold arrow.
5. **Selected work (dark):** the section pins and five project cards scroll horizontally (1,592px of scroll at 1440 wide), with a progress bar and an 01→05 counter.
6. **Numbers + marquee:** four counters and an endless word ribbon whose speed and direction follow the scroll.
7. **Studio (dark):** title, a tall portrait, text, a team row, a cream "350+ Trusted partners" card, a photo card "400+", then **How we build high-performing brands**: five cream and mustard tiles (colours swap on hover), a note and a text block with a button.
8. **Process (cream):** a sticky left column and four steps on the right; a gold line fills as you scroll.
9. **Showcase "View our latest works" (tan, dark panel):** a curved carousel of five tilted cards, the centre one larger; arrows, arrow keys, drag and click on a side card turn it.
10. **Manifesto:** a 120vh dune photo with parallax; a 3-line sentence fills word by word.
11. **Testimonials (dark, auto-advancing):** four slides, each a text card painted in its image's colour next to the image card; a bar fills, then the whole track glides to the next slide, endlessly.
12. **CTA:** a 100svh photo that opens from a rounded window to full bleed; a 150px magnetic gold "LET'S TALK" circle.
13. **Footer (dark):** five columns, a client-side newsletter field, a giant SAFFRA wordmark whose letters rise in, and Back to top.

Around it: a fixed header (logo, menu button, nav, mail icon, gold "Start a project" button) that hides on scroll down and becomes a dark glass pill after the hero, a full-screen overlay menu, a gold arrow cursor with labels (View / Explore) on fine pointers, magnetic buttons, and full `prefers-reduced-motion` support (no loader, no Lenis, no water, everything visible, work row and showcase become native horizontal scrollers).

---

## 0. Global shell

- One file: `index.html`. No build step, no framework. All CSS in one `<style>` in `<head>`, plus a `<noscript><style>` that stacks the testimonial slides. Classic `<script>` elements: a tiny inline one in `<head>` (motion flags), the three CDN libraries and the main inline IIFE at the end of `<body>`, then a second inline IIFE for the testimonial carousel.
- `<html lang="en">`, `<title>Saffra Design — Creative Strategy Studio</title>`, `<meta name="theme-color" content="#15120c">`, favicon = the Saffra icon (§4) stroked `#c9a45c` on a `#15120c` rounded square (rx 8 in a 32×32 viewBox), as an inline SVG data URI. `<link rel="preload" as="image" crossorigin="anonymous">` for the hero photo.
- **The hero `<img>` and its preload carry `crossorigin="anonymous"`** (the photo is uploaded to WebGL). Never load the hero URL without CORS mode anywhere on the page.
- Font: `https://fonts.googleapis.com/css2?family=Inter+Tight:wght@300..700&display=swap`, with `preconnect`. Stack: `"Inter Tight", system-ui, -apple-system, "Segoe UI", Roboto, Arial, sans-serif`. Display titles are uppercase, weight 400 (numbers 300, wordmark 500, logo 650, testimonial titles 600).
- `html` and `body`: `overflow-x: clip`; body background `#15120c`, color `#f2ede3`, 15px / 1.5. `h1,h2,h3{text-wrap:balance}`.
- Head script: adds `js`; if the visitor does not prefer reduced motion it adds `has-motion`; after 3,000ms it removes `has-motion` again if the main script never added `motion-ready`.
- Page order: skip link → `.loader` → `.cursor` → fixed `header.header` → `#menu` → `main` (`section.hero#top`, `.about#about`, `.story#services`, `.work#work`, `.numbers`, `.studio#studio`, `.process#process`, `.showcase#showcase`, `.manifesto`, `.ll#voices`, `.cta#contact`) → `footer.footer`.

## 1. Reference viewport, tokens and layers

Reference viewport: **1440 × 900**. No scaled canvas: the layout is fluid (`--gutter: clamp(16px, 2.5vw, 36px)` = 36px at 1440, `--max: 1440px`, `clamp()` type sizes); the testimonial section uses its own unit `--u = min(100cqw,1920px)/1600` (0.9 at 1440). Total document height at 1440 × 900: **13,982px** with motion (the work pin adds 1,592px and the water canvas nothing), **12,143px** with reduced motion.

Tokens (in `:root`; some rules in §2 also use literal rgba values derived from them — keep both exactly as written): `--ink #15120c`, `--ink-2 #1f1a12`, `--ink-3 #2a2318`, `--cream #f2ede3`, `--paper #e9e2d3`, `--gold #c9a45c`, `--gold-deep #a3803a`, `--gold-soft #e6d3a1`, `--gold-glass rgba(201,164,92,.26)`, `--gold-text #7a5c1f`, `--brown #3a2814`, `--brown-title #4a2f14`, `--muted-card #5f5748`, `--tan #cdb48a`, `--muted-on-dark rgba(242,237,227,.64)`, `--muted-on-light #6d6455`, `--line-dark rgba(242,237,227,.16)`, `--line-light rgba(21,18,12,.14)`, `--ease-out cubic-bezier(.16,1,.3,1)`.

| Layer | Selector | z-index | Position |
|---|---|---|---|
| Hero (pinned, `pinSpacing:false`) | `.hero` | auto | 100svh, min 640px |
| All later sections | `.about` … `.cta`, `.footer` | 3 | relative, slide over the pinned hero |
| Header | `.header` | 100 | fixed top 0 |
| Overlay menu | `.menu` | 150 | fixed inset 0 |
| Skip link | `.skip` | 200 | fixed |
| Cursor | `.cursor` | 250 | fixed, pointer-events none |
| Loader | `.loader` | 300 | fixed inset 0 |

## 1.5 Absolute coordinate table (1440 × 900, page coordinates, reduced-motion static layout)

With motion on, everything from `.numbers` down sits **1,592px lower plus 247px** (work pin spacer and the extra scroll length); use the document heights in §1 as the check. Columns: x, y, w, h.

| Element | x | y | w | h |
|---|---|---|---|---|
| `.header__inner` | 0 | 0 | 1440 | 84 |
| `.logo` | 36 | 20 | 179 | 44 |
| `.menu-btn` | 233 | 20 | 44 | 44 |
| `.nav` | 869 | 20 | 313 | 44 |
| mail `.icon-btn` | 1206 | 20 | 44 | 44 |
| `.header__cta` | 1258 | 20 | 146 | 44 |
| `.hero` | 0 | 0 | 1440 | 900 |
| `.hero__tag` | 36 | 154 | 300 | 50 |
| `.hero__lead` | 36 | 220 | 270 | 65 |
| `.hero__title` | 1076 | 120 | 328 | 212 |
| `.hero__aside` | 1074 | 362 | 330 | 105 |
| hero `.btn-line` | 1074 | 423 | 145 | 44 |
| `.stat-card` | 36 | 688 | 178 | 176 |
| `.glass-card` | 224 | 688 | 210 | 176 |
| `.pills` | 935 | 824 | 469 | 40 |
| `.about` | 0 | 868 | 1440 | 993 |
| `.about__title` | 180 | 978 | 1080 | 294 |
| `.gallery` | 514 | 1438 | 760 | 304 |
| `.story__title` | 230 | 1901 | 980 | 118 |
| `.services` | 36 | 2171 | 1368 | 456 |
| `.work` | 0 | 2767 | 1440 | 900 |
| `.project` (first, 576×460) | 0 | 3114 | 576 | 460 |
| `.numbers` | 0 | 3667 | 1440 | 505 |
| `.marquee` | 0 | 3956 | 1440 | 216 |
| `.studio` | 0 | 4172 | 1440 | 2138 |
| `.studio__title` | 36 | 4372 | 860 | 141 |
| `.studio__photo` | 36 | 4583 | 546 | 683 |
| `.studio__p` | 884 | 4583 | 520 | 81 |
| `.studio__team` | 884 | 4756 | 520 | 78 |
| cream card / photo card (375×340) | 640 / 1029 | 4926 | 375 | 340 |
| `.toolkit__head` | 36 | 5422 | 1368 | 71 |
| tiles a / b / note / c (332×317) | 36 / 382 / 727 / 1073 | 5542 | 332 | 317 |
| text block / tile d / tile e | 36 / 727 / 1073 | 5873 | 677 / 332 / 332 | 317 |
| `.process` | 0 | 6310 | 1440 | 1241 |
| `.steps` | 656 | 6440 | 748 | 991 |
| `.showcase` | 0 | 7551 | 1440 | 963 |
| `.showcase__panel` | 36 | 7856 | 1368 | 527 |
| `.showcase__big` | 365 | 7902 | 983 | 117 |
| `.arc` | 36 | 8045 | 1368 | 299 |
| `.manifesto` | 0 | 8514 | 1440 | 1080 |
| `.ll` | 0 | 9594 | 1440 | 877 |
| `.ll-btn` (2 × 67) | 1157 | 9702 | 67 | 67 |
| active `.ll-text` / `.ll-media` | 140 / 652 | 9868 | 501 / 649 | 495 |
| active `.ll-bar` | 211 | 10289 | 359 | 4 |
| `.cta` | 0 | 10471 | 1440 | 900 |
| `.big-btn` | 546 | 10980 | 150 | 150 |
| `.footer` | 0 | 11371 | 1440 | 773 |
| `.wordmark` | 36 | 11750 | 1368 | 272 |

Computed type at 1440 (size / weight): hero title 72 / 400 · about title 72 · story title 56.16 · work title 86.4 · numbers 77.76 / 300 · studio and toolkit titles ≈ 70 / 400 · showcase big title ≈ 130 / 600 · testimonial title 42.75 / 600, body 20.7 / 400 · CTA title 95.04 · wordmark 309.6 / 500 · nav 11.5 / 500 · pills 11 / 500.

## 2. Complete page source (copy exactly)

Copy this into `index.html` without changing a single character. Everything below §2 only explains it.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Saffra Design — Creative Strategy Studio</title>
<meta name="description" content="Saffra Design is a creative strategy studio shaping brand strategy, visual identity, digital experiences and content.">
<meta name="theme-color" content="#15120c">
<link rel="icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'%3E%3Crect width='32' height='32' rx='8' fill='%2315120c'/%3E%3Cg fill='none' stroke='%23c9a45c' stroke-width='2.4' stroke-linecap='round'%3E%3Cpath d='M16 27V8'/%3E%3Cpath d='M16 27C16 19 10 15 8 8'/%3E%3Cpath d='M16 27C16 19 22 15 24 8'/%3E%3C/g%3E%3C/svg%3E">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter+Tight:wght@300..700&display=swap" rel="stylesheet">
<link rel="preload" as="image" href="https://assets.layerweb.com.br/saffra-design/hero-sunset.webp" crossorigin="anonymous">
<script>
  /* Motion flags: .has-motion hides elements that animate in. Safety net removes it if the main script fails. */
  (function () {
    var d = document.documentElement;
    d.classList.add('js');
    if (!window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
      d.classList.add('has-motion');
      setTimeout(function () { if (!d.classList.contains('motion-ready')) d.classList.remove('has-motion'); }, 3000);
    }
  })();
</script>
<style>
:root{
  --ink:#15120c; --ink-2:#1f1a12; --ink-3:#2a2318;
  --cream:#f2ede3; --paper:#e9e2d3; --sand:#ddd3bf;
  --gold:#c9a45c; --gold-deep:#a3803a; --gold-soft:#e6d3a1; --gold-glass:rgba(201,164,92,.26);
  --gold-text:#7a5c1f; --brown:#3a2814; --brown-title:#4a2f14; --muted-card:#5f5748; --tan:#cdb48a;
  --text-on-dark:#f2ede3; --muted-on-dark:rgba(242,237,227,.64);
  --text-on-light:#15120c; --muted-on-light:#6d6455;
  --line-dark:rgba(242,237,227,.16); --line-light:rgba(21,18,12,.14);
  --gutter:clamp(16px, 2.5vw, 36px);
  --max:1440px;
  --radius:18px;
  --ease-out:cubic-bezier(.16,1,.3,1);
  --font:"Inter Tight", system-ui, -apple-system, "Segoe UI", Roboto, Arial, sans-serif;
}
*,*::before,*::after{box-sizing:border-box}
html,body{margin:0;padding:0}
html{-webkit-text-size-adjust:100%;overflow-x:clip}
html.lenis,html.lenis body{height:auto}
.lenis.lenis-smooth{scroll-behavior:auto!important}
body{background:var(--ink);color:var(--text-on-dark);font-family:var(--font);font-size:15px;line-height:1.5;overflow-x:clip;-webkit-font-smoothing:antialiased;text-rendering:optimizeLegibility}
img{display:block;max-width:100%}
a{color:inherit;text-decoration:none}
h1,h2,h3{text-wrap:balance}
button{font:inherit;color:inherit;background:none;border:0;padding:0;cursor:pointer}
:focus-visible{outline:2px solid var(--gold);outline-offset:3px;border-radius:6px}
.sr-only{position:absolute;width:1px;height:1px;overflow:hidden;clip:rect(0 0 0 0);white-space:nowrap}
.skip{position:fixed;left:16px;top:-60px;z-index:200;background:var(--gold);color:var(--ink);padding:10px 16px;border-radius:999px;font-size:13px;font-weight:600;transition:top .2s}
.skip:focus{top:16px}
.wrap{max-width:var(--max);margin:0 auto;padding:0 var(--gutter)}
.eyebrow{font-size:12px;font-weight:500;letter-spacing:.12em;text-transform:uppercase}
.img-grade{filter:saturate(.86) sepia(.08)}
.media{position:relative;overflow:hidden;background:var(--ink-3)}
.media img{width:100%;height:100%;object-fit:cover}
.media img.broken{opacity:0}

/* ---------- word split (JS) ---------- */
.w{display:inline-block;overflow:hidden;vertical-align:top;padding-bottom:.1em;margin-bottom:-.1em}
.wi{display:inline-block;will-change:transform}
.fill .w{overflow:visible}

/* ---------- loader ---------- */
.loader{position:fixed;inset:0;z-index:300;background:var(--ink);display:none;align-items:center;justify-content:center;flex-direction:column;gap:22px}
.has-motion .loader{display:flex}
.loader__brand{display:flex;align-items:center;gap:12px;color:var(--cream);font-size:18px;font-weight:600;letter-spacing:.18em}
.loader__brand svg{width:34px;height:34px;color:var(--gold)}
.loader__brand svg path{stroke-dasharray:30;stroke-dashoffset:30}
.loader__count{font-size:12px;letter-spacing:.2em;color:var(--muted-on-dark);font-variant-numeric:tabular-nums}
.loader__bar{width:160px;height:1px;background:var(--line-dark);overflow:hidden}
.loader__bar i{display:block;height:100%;width:100%;background:var(--gold);transform:scaleX(0);transform-origin:left}

/* ---------- cursor ---------- */
.cursor{position:fixed;left:0;top:0;z-index:250;pointer-events:none;display:none;opacity:0;transition:opacity .25s}
.cursor__arrow{display:block;width:19px;height:21px;margin:-1px 0 0 -2px;overflow:visible;transform-origin:2px 1px;filter:drop-shadow(0 1px 2px rgba(21,18,12,.28));transition:scale .35s var(--ease-out)}
.cursor__arrow path{fill:var(--gold);stroke:var(--gold);stroke-width:3.6;stroke-linejoin:round;transition:fill .3s,stroke .3s}
.cursor__label{position:absolute;left:14px;top:17px;height:22px;padding:0 10px;border-radius:999px;background:var(--gold);color:var(--ink);font-size:9px;font-weight:600;letter-spacing:.12em;text-transform:uppercase;display:flex;align-items:center;white-space:nowrap;opacity:0;transform:translate(-6px,-6px) scale(.6);transform-origin:0 0;transition:opacity .25s,transform .45s var(--ease-out)}
.cursor.is-label .cursor__label{opacity:1;transform:none}
.cursor.is-label .cursor__arrow,.cursor.is-link .cursor__arrow{scale:1.15}
.cursor.is-link .cursor__arrow path{fill:var(--gold-soft);stroke:var(--gold-soft)}
.cursor.is-down .cursor__arrow{scale:.82}
.cursor.is-text{opacity:0!important}
@media (hover:hover) and (pointer:fine){
  .has-cursor .cursor{display:block}
  .has-cursor,.has-cursor *{cursor:none!important}
  .has-cursor input{cursor:text!important}
}
.cursor-on .cursor{opacity:1}

/* ---------- header ---------- */
.header{position:fixed;left:0;right:0;top:0;z-index:100;transition:transform .5s var(--ease-out)}
.header.is-hidden{transform:translateY(-110%)}
.header__inner{max-width:var(--max);margin:0 auto;height:84px;padding:0 var(--gutter);display:flex;align-items:center;justify-content:space-between;gap:24px;position:relative}
.header__bg{position:absolute;inset:10px calc(var(--gutter) - 12px);border-radius:999px;background:rgba(21,18,12,.72);backdrop-filter:blur(16px);-webkit-backdrop-filter:blur(16px);border:1px solid var(--line-dark);opacity:0;transition:opacity .4s}
.header.is-scrolled .header__bg{opacity:1}
.header__left{display:flex;align-items:center;gap:18px;position:relative}
.logo{display:flex;align-items:center;gap:10px;color:var(--cream);min-height:44px}
.logo svg{width:26px;height:26px;color:var(--gold);flex:none}
.logo b{font-size:17px;font-weight:650;letter-spacing:.16em}
.logo small{font-size:11px;font-weight:400;letter-spacing:.2em;color:var(--muted-on-dark);margin-left:2px}
.menu-btn{width:44px;height:44px;border-radius:50%;display:grid;place-items:center;border:1px solid var(--line-dark);transition:background-color .3s,border-color .3s}
.menu-btn:hover{background:rgba(242,237,227,.08);border-color:rgba(242,237,227,.3)}
.menu-btn i{display:block;width:12px;height:1px;background:var(--cream);position:relative}
.menu-btn i::after{content:"";position:absolute;left:0;top:4px;width:8px;height:1px;background:var(--cream);transition:width .3s}
.menu-btn:hover i::after{width:12px}
.nav{display:flex;align-items:center;gap:4px;position:relative;margin-left:auto}
.nav a{font-size:11.5px;font-weight:500;letter-spacing:.12em;text-transform:uppercase;padding:9px 14px;border-radius:999px;color:var(--cream);position:relative;overflow:hidden;transition:background-color .3s;min-height:44px;display:inline-flex;align-items:center}
.nav a:hover{background:rgba(242,237,227,.1)}
.nav a.is-active{background:rgba(242,237,227,.12)}
.header__icons{display:flex;gap:8px;position:relative}
.icon-btn{width:44px;height:44px;border-radius:10px;display:grid;place-items:center;border:1px solid var(--line-dark);color:var(--cream);transition:background-color .3s,color .3s,border-color .3s}
.icon-btn:hover{background:var(--gold);border-color:var(--gold);color:var(--ink)}
.icon-btn svg{width:15px;height:15px}
.header__cta{white-space:nowrap;display:inline-flex;align-items:center;gap:10px;height:44px;padding:0 5px 0 18px;border-radius:999px;background:var(--gold);color:var(--ink);font-size:12.5px;font-weight:600;transition:background-color .35s}
.header__cta i{width:34px;height:34px;border-radius:50%;background:var(--ink);color:var(--cream);display:grid;place-items:center}
.header__cta svg{width:13px;height:13px;transition:transform .45s var(--ease-out)}
@media (hover:hover) and (pointer:fine){.header__cta:hover{background:var(--gold-soft)}.header__cta:hover svg{transform:rotate(45deg)}}

/* ---------- overlay menu ---------- */
.menu{position:fixed;inset:0;z-index:150;background:var(--ink);color:var(--cream);display:flex;flex-direction:column;justify-content:space-between;padding:110px var(--gutter) 36px;clip-path:inset(0 0 100% 0);visibility:hidden;transition:clip-path .8s var(--ease-out),visibility 0s .8s}
.menu.is-open{clip-path:inset(0 0 0% 0);visibility:visible;transition:clip-path .8s var(--ease-out),visibility 0s}
.menu__close{position:absolute;top:25px;right:var(--gutter);width:34px;height:34px;border-radius:50%;border:1px solid var(--line-dark);display:grid;place-items:center}
.menu__close svg{width:14px;height:14px}
.menu__list{list-style:none;margin:0;padding:0;max-width:var(--max)}
.menu__list li{overflow:hidden;border-bottom:1px solid var(--line-dark)}
.menu__list a{display:flex;align-items:baseline;gap:18px;font-size:clamp(40px,7vw,96px);font-weight:400;line-height:1.08;letter-spacing:-.02em;text-transform:uppercase;padding:6px 0;transform:translateY(105%);transition:transform .8s var(--ease-out),color .3s}
.menu__list a span{font-size:12px;letter-spacing:.12em;color:var(--gold)}
.menu__list a:hover{color:var(--gold)}
.menu.is-open .menu__list a{transform:none}
.menu.is-open .menu__list li:nth-child(2) a{transition-delay:.06s}
.menu.is-open .menu__list li:nth-child(3) a{transition-delay:.12s}
.menu.is-open .menu__list li:nth-child(4) a{transition-delay:.18s}
.menu.is-open .menu__list li:nth-child(5) a{transition-delay:.24s}
.menu__foot{display:flex;justify-content:space-between;flex-wrap:wrap;gap:12px;font-size:13px;color:var(--muted-on-dark)}

/* ---------- hero ---------- */
.hero{position:relative;height:100vh;height:100svh;min-height:640px;overflow:hidden;background:var(--ink)}
.hero__frame{position:absolute;inset:0;overflow:hidden;transform-origin:50% 0}
.hero__img{position:absolute;inset:-6% 0 0 0;height:112%;width:100%}
.hero__img img{width:100%;height:100%;object-fit:cover;object-position:50% 30%}
.hero__water{position:absolute;inset:0;width:100%;height:100%;pointer-events:none;display:none}
.gl .hero__water{display:block}
.gl .hero__img img{visibility:hidden}
.hero__shade{position:absolute;inset:0;pointer-events:none;background:
  linear-gradient(90deg,rgba(21,18,12,.82) 0%,rgba(21,18,12,.35) 30%,rgba(21,18,12,0) 50%,rgba(21,18,12,.3) 70%,rgba(21,18,12,.78) 100%),
  linear-gradient(180deg,rgba(21,18,12,.55) 0%,rgba(21,18,12,0) 22%,rgba(21,18,12,0) 62%,rgba(21,18,12,.85) 100%)}
.hero__dim{position:absolute;inset:0;background:var(--ink);opacity:0;pointer-events:none}
.hero__content{position:relative;z-index:2;height:100%;max-width:var(--max);margin:0 auto;padding:120px var(--gutter) 36px;display:grid;grid-template-columns:minmax(0,1fr) minmax(0,1fr);grid-template-rows:1fr auto}
.hero__left{grid-column:1;grid-row:1;padding-top:34px;max-width:300px}
.hero__tag{font-size:clamp(18px,1.6vw,23px);font-weight:500;line-height:1.08;letter-spacing:-.005em;text-transform:uppercase;margin:0}
.hero__lead{font-size:14px;line-height:1.55;color:var(--muted-on-dark);margin:16px 0 0;max-width:270px}
.hero__right{grid-column:2;grid-row:1;display:flex;flex-direction:column;align-items:flex-end;text-align:right}
.hero__title{font-size:clamp(40px,5vw,76px);font-weight:400;line-height:.98;letter-spacing:-.025em;text-transform:uppercase;margin:0}
.hero__title .w{padding-bottom:.06em}
.hero__aside{display:flex;align-items:flex-start;gap:18px;margin-top:30px;max-width:330px;text-align:left;flex-direction:column}
.hero__aside p{font-size:14px;line-height:1.55;color:var(--muted-on-dark);margin:0}
.hero__bottom{grid-column:1 / -1;grid-row:2;display:flex;justify-content:space-between;align-items:flex-end;gap:24px}
.hero__cards{display:flex;gap:10px}
.stat-card{width:178px;height:176px;border-radius:16px;background:var(--cream);color:var(--ink);padding:14px;display:flex;flex-direction:column;justify-content:space-between}
.stat-card__top{display:flex;align-items:center;gap:9px;font-size:11.5px;line-height:1.2;font-weight:500}
.stat-card__icon{width:28px;height:28px;border-radius:8px;background:var(--ink);display:grid;place-items:center;color:var(--gold)}
.stat-card__icon svg{width:15px;height:15px}
.stat-card__num{font-size:46px;font-weight:500;letter-spacing:-.03em;line-height:1;font-variant-numeric:tabular-nums}
.stat-card__label{font-size:11.5px;color:var(--muted-on-light);margin-top:6px;padding-top:8px;border-top:1px solid var(--line-light)}
.glass-card{width:210px;height:176px;border-radius:16px;padding:14px;display:flex;flex-direction:column;justify-content:space-between;background:var(--gold-glass);border:1px solid rgba(230,211,161,.32);backdrop-filter:blur(14px);-webkit-backdrop-filter:blur(14px);overflow:hidden}
.glass-card__title{font-size:16px;font-weight:500;line-height:1.15;margin:0}
.glass-card__text{font-size:12.5px;line-height:1.5;color:rgba(242,237,227,.78);margin:0}
.glass-card__swap{transition:opacity .35s,transform .45s var(--ease-out)}
.glass-card__swap.is-out{opacity:0;transform:translateY(8px)}
.hero__pills-wrap{display:flex;flex-direction:column;align-items:flex-end;gap:12px}
.scroll-hint{display:flex;align-items:center;gap:10px;font-size:11px;letter-spacing:.2em;text-transform:uppercase;color:var(--muted-on-dark)}
.scroll-hint i{display:block;width:40px;height:1px;background:var(--line-dark);position:relative;overflow:hidden}
.scroll-hint i::after{content:"";position:absolute;inset:0;background:var(--gold);transform:translateX(-100%);animation:hint 2.2s var(--ease-out) infinite}
@keyframes hint{0%{transform:translateX(-100%)}60%,100%{transform:translateX(100%)}}
.pills{display:flex;flex-wrap:wrap;gap:6px;justify-content:flex-end}
.pill{height:40px;padding:0 14px;border-radius:999px;border:1px solid rgba(242,237,227,.34);font-size:11px;font-weight:500;letter-spacing:.12em;text-transform:uppercase;display:inline-flex;align-items:center;gap:7px;color:var(--cream);transition:background-color .3s,color .3s,border-color .3s}
.pill::before{content:"";width:5px;height:5px;border-radius:50%;background:currentColor;display:none}
.pill:hover{border-color:var(--gold-soft)}
.pill[aria-selected="true"]{background:var(--cream);color:var(--ink);border-color:var(--cream)}
.pill[aria-selected="true"]::before{display:block;background:var(--gold-deep)}

/* ---------- about (light) ---------- */
.light{background:var(--cream);color:var(--text-on-light)}
.about{position:relative;z-index:3;border-radius:32px 32px 0 0;margin-top:-32px;padding:110px 0 120px}
.about__top{display:grid;grid-template-columns:120px 1fr 120px;align-items:start;gap:24px}
.asterisk{width:44px;height:44px;color:var(--gold-deep)}
.about__kicker{justify-self:end;text-align:right;font-size:11.5px;font-weight:500;letter-spacing:.12em;line-height:1.5;text-transform:uppercase;color:var(--muted-on-light)}
.about__title{grid-column:2;margin:0;text-align:center;font-size:clamp(32px,5vw,78px);font-weight:400;line-height:1.02;letter-spacing:-.025em;text-transform:uppercase}
.accent{color:var(--gold-deep);font-style:normal}
.switch{display:inline-block;vertical-align:middle;width:1.55em;height:.78em;border-radius:999px;background:var(--gold);position:relative;margin:0 .06em;top:-.06em}
.switch::before{content:"";position:absolute;left:.09em;top:50%;width:.6em;height:.6em;margin-top:-.3em;border-radius:50%;border:.06em solid rgba(21,18,12,.35)}
.switch__knob{position:absolute;left:.09em;top:50%;width:.6em;height:.6em;margin-top:-.3em;border-radius:50%;overflow:hidden;background:var(--ink);box-shadow:0 .04em .12em rgba(21,18,12,.3)}
.switch__knob img{width:100%;height:100%;object-fit:cover}
.about__lead{max-width:380px;margin:34px auto 0;text-align:center;font-size:15px;line-height:1.6;color:var(--muted-on-light)}
.about__body{display:grid;grid-template-columns:200px 1fr;gap:24px;margin-top:60px;align-items:end}
.link-under{display:inline-block;font-size:12px;font-weight:500;letter-spacing:.1em;line-height:1.4;text-transform:uppercase;color:var(--ink);position:relative;padding-bottom:4px}
.link-under::after{content:"";position:absolute;left:0;bottom:0;width:100%;height:1px;background:currentColor;transform-origin:right;transition:transform .5s var(--ease-out)}
.link-under:hover::after{transform:scaleX(0)}
.link-under:hover{color:var(--gold-deep)}
.gallery{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:14px;max-width:760px;margin-left:auto;margin-right:clamp(0px,9vw,140px)}
.gallery__item{aspect-ratio:9/11.2;border-radius:14px}
.gallery__item img{height:118%;margin-top:-9%}

/* ---------- story + services ---------- */
.story{padding:40px 0 140px;position:relative;z-index:3}
.story__title{margin:0 auto;max-width:980px;text-align:center;font-size:clamp(28px,3.9vw,60px);font-weight:400;line-height:1.05;letter-spacing:-.022em;text-transform:uppercase}
.story__lead{max-width:470px;margin:24px auto 0;text-align:center;font-size:15px;line-height:1.6;color:var(--muted-on-light)}
.services{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:14px;margin-top:80px;perspective:1200px;align-items:start}
.service{position:relative;aspect-ratio:8/11;border-radius:18px;background:var(--ink);color:var(--cream);overflow:hidden;display:block;transform-style:preserve-3d}
.service:nth-child(2){margin-top:-44px}
.service .media{position:absolute;inset:0;background:var(--ink)}
.service .media img{transition:transform 1.2s var(--ease-out)}
.service:hover .media img{transform:scale(1.07)}
.service::after{content:"";position:absolute;inset:0;background:linear-gradient(180deg,rgba(21,18,12,.35) 0%,rgba(21,18,12,0) 30%,rgba(21,18,12,.1) 55%,rgba(21,18,12,.9) 100%);pointer-events:none}
.service__tag{position:absolute;z-index:2;left:12px;top:12px;height:24px;padding:0 10px;border-radius:999px;background:rgba(21,18,12,.55);border:1px solid var(--line-dark);backdrop-filter:blur(8px);-webkit-backdrop-filter:blur(8px);font-size:10.5px;font-weight:600;letter-spacing:.14em;text-transform:uppercase;display:flex;align-items:center}
.service__num{position:absolute;z-index:2;right:14px;top:14px;font-size:12px;color:var(--gold-soft);letter-spacing:.08em}
.service__body{position:absolute;z-index:2;left:16px;right:16px;bottom:16px}
.service__title{font-size:clamp(20px,1.9vw,28px);font-weight:400;line-height:1.02;letter-spacing:-.02em;margin:0}
.service__more{display:grid;grid-template-rows:0fr;transition:grid-template-rows .55s var(--ease-out)}
.service__more > div{overflow:hidden}
.service__more p{font-size:13px;line-height:1.5;color:var(--muted-on-dark);margin:10px 0 0;max-width:220px}
.service:hover .service__more,.service:focus-visible .service__more{grid-template-rows:1fr}
.service__arrow{position:absolute;right:0;bottom:0;width:32px;height:32px;border-radius:50%;background:var(--gold);color:var(--ink);display:grid;place-items:center;transform:scale(0) rotate(-45deg);transition:transform .5s var(--ease-out)}
.service__arrow svg{width:13px;height:13px}
.service:hover .service__arrow{transform:none}

/* ---------- work (dark, horizontal) ---------- */
.work{position:relative;z-index:3;background:var(--ink);overflow:hidden}
.work__pin{height:100vh;height:100svh;display:flex;flex-direction:column;justify-content:center;gap:40px;padding:100px 0 50px}
.work__pin > .wrap{width:100%}
.work__head{display:flex;justify-content:space-between;align-items:flex-end;gap:24px}
.work__title{margin:0;font-size:clamp(36px,6vw,92px);font-weight:400;line-height:.95;letter-spacing:-.03em;text-transform:uppercase}
.work__title sup{font-size:max(.22em,12px);letter-spacing:.06em;color:var(--gold);vertical-align:top;margin-left:.3em;position:relative;top:.4em}
.work__intro{max-width:340px;font-size:15px;line-height:1.6;color:var(--muted-on-dark);margin:0 0 6px}
.work__track{display:flex;gap:20px;padding-left:var(--gutter);padding-right:var(--gutter);width:max-content;will-change:transform}
.project{width:clamp(280px,40vw,600px);flex:none;display:block}
.project .media{aspect-ratio:16/11;border-radius:18px}
.project .media img{width:116%;max-width:none;margin-left:-8%;transition:transform 1.2s var(--ease-out)}
.project:hover .media img{transform:scale(1.05)}
.project__meta{display:flex;justify-content:space-between;align-items:baseline;gap:12px;margin-top:16px;padding-bottom:12px;border-bottom:1px solid var(--line-dark)}
.project__name{font-size:clamp(18px,1.6vw,24px);font-weight:400;letter-spacing:-.01em;margin:0}
.project__cat{font-size:11.5px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted-on-dark)}
.project__year{color:var(--gold)}
html:not(.motion-ready) .work__track{width:auto;overflow-x:auto;scroll-snap-type:x mandatory;scrollbar-width:thin}
html:not(.motion-ready) .project{scroll-snap-align:start}
html:not(.motion-ready) .work__progress{display:none}
.work__progress{display:flex;align-items:center;gap:16px;font-size:12px;letter-spacing:.12em;color:var(--muted-on-dark);font-variant-numeric:tabular-nums}
.work__bar{flex:1;height:1px;background:var(--line-dark);position:relative;overflow:hidden}
.work__bar i{position:absolute;inset:0;background:var(--gold);transform:scaleX(0);transform-origin:left}

/* ---------- numbers + marquee ---------- */
.numbers{position:relative;z-index:3;background:var(--ink);padding:40px 0 0;border-top:1px solid var(--line-dark)}
.numbers__grid{display:grid;grid-template-columns:repeat(4,minmax(0,1fr))}
.num{padding:40px 24px 44px 0;border-right:1px solid var(--line-dark)}
.num + .num{padding-left:24px}
.num:last-child{border-right:0}
.num__value{font-size:clamp(44px,5.4vw,84px);font-weight:300;letter-spacing:-.04em;line-height:1;font-variant-numeric:tabular-nums}
.num__value em{font-style:normal;color:var(--gold)}
.num__label{margin-top:14px;font-size:13px;line-height:1.5;color:var(--muted-on-dark);max-width:200px}
.marquee{overflow:hidden;padding:46px 0 54px;border-top:1px solid var(--line-dark);margin-top:20px}
.marquee__track{display:flex;width:max-content;will-change:transform}
.marquee__group{display:flex;align-items:center;flex:none}
.marquee__item{font-size:clamp(48px,8vw,128px);font-weight:400;line-height:1;letter-spacing:-.03em;text-transform:uppercase;padding:0 .3em;white-space:nowrap}
.marquee__item.dim{color:rgba(242,237,227,.3)}
.marquee__star{width:clamp(30px,4vw,56px);height:clamp(30px,4vw,56px);color:var(--gold);flex:none}

/* ---------- process ---------- */
.process{position:relative;z-index:3;padding:130px 0 120px}
.process__grid{display:grid;grid-template-columns:minmax(0,5fr) minmax(0,7fr);gap:clamp(32px,6vw,110px);align-items:start}
.process__sticky{position:sticky;top:120px}
.process__title{margin:16px 0 0;font-size:clamp(36px,4.2vw,64px);font-weight:400;line-height:.98;letter-spacing:-.03em;text-transform:uppercase}
.process__lead{margin:24px 0 0;max-width:340px;font-size:15px;line-height:1.6;color:var(--muted-on-light)}
.process__media{margin-top:36px;width:min(100%,340px);aspect-ratio:4/3;border-radius:16px}
.steps{position:relative;padding:0 0 0 34px}
.steps ol{list-style:none;margin:0;padding:0}
.steps__line{position:absolute;left:0;top:8px;bottom:8px;width:1px;background:var(--line-light)}
.steps__line i{position:absolute;inset:0;background:var(--gold-deep);transform:scaleY(0);transform-origin:top}
.step{position:relative;padding:34px 0 40px;border-bottom:1px solid var(--line-light);opacity:.32;transition:opacity .6s}
.step:first-child{padding-top:0}
.step.is-active{opacity:1}
.step::before{content:"";position:absolute;left:-38px;top:41px;width:9px;height:9px;border-radius:50%;background:var(--cream);border:1px solid var(--muted-on-light);transition:background-color .4s,border-color .4s}
.step:first-child::before{top:7px}
.step.is-active::before{background:var(--gold);border-color:var(--gold-deep)}
.step__head{display:flex;justify-content:space-between;align-items:baseline;gap:16px}
.step__num{font-size:12px;letter-spacing:.14em;color:var(--gold-text)}
.step__time{font-size:11.5px;letter-spacing:.12em;text-transform:uppercase;color:var(--muted-on-light)}
.step__title{margin:10px 0 0;font-size:clamp(28px,3.4vw,52px);font-weight:400;letter-spacing:-.025em;line-height:1;text-transform:uppercase}
.step__text{margin:14px 0 0;max-width:460px;font-size:15px;line-height:1.6;color:var(--muted-on-light)}
.step__tags{display:flex;flex-wrap:wrap;gap:6px;margin-top:16px}
.step__tags span{height:26px;padding:0 11px;border-radius:999px;border:1px solid var(--line-light);font-size:11px;letter-spacing:.1em;text-transform:uppercase;display:inline-flex;align-items:center}

/* ---------- manifesto ---------- */
.manifesto{position:relative;z-index:3;height:120vh;min-height:700px;overflow:hidden;display:flex;align-items:center;background:var(--ink)}
.manifesto .media{position:absolute;inset:-12% 0;background:var(--ink)}
.manifesto__shade{position:absolute;inset:0;background:radial-gradient(ellipse at 50% 50%,rgba(21,18,12,.35) 0%,rgba(21,18,12,.75) 80%)}
.manifesto__inner{position:relative;z-index:2;text-align:center;width:100%}
.manifesto__text{margin:22px auto 0;max-width:1100px;font-size:clamp(28px,4.4vw,68px);font-weight:400;line-height:1.08;letter-spacing:-.025em;text-transform:uppercase}

/* ---------- testimonials ---------- */

/* ---------- CTA ---------- */
.cta{position:relative;z-index:3;background:var(--paper);padding:0 0 0}
.cta__frame{position:relative;height:100vh;height:100svh;min-height:620px;overflow:hidden;clip-path:inset(0 0 0 0 round 0px);background:var(--ink)}
.cta__frame .media{position:absolute;inset:-8% 0;background:var(--ink)}
.cta__shade{position:absolute;inset:0;background:linear-gradient(180deg,rgba(21,18,12,.55),rgba(21,18,12,.35) 40%,rgba(21,18,12,.8))}
.cta__inner{position:relative;z-index:2;height:100%;display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;color:var(--cream);padding:0 var(--gutter)}
.cta__title{margin:20px 0 0;font-size:clamp(36px,6.6vw,104px);font-weight:400;line-height:.98;letter-spacing:-.03em;text-transform:uppercase}
.cta__row{display:flex;align-items:center;gap:18px;margin-top:44px;flex-wrap:wrap;justify-content:center}
.big-btn{width:150px;height:150px;border-radius:50%;background:var(--gold);color:var(--ink);display:grid;place-items:center;text-align:center;font-size:12px;font-weight:600;letter-spacing:.12em;text-transform:uppercase;line-height:1.3;transition:background-color .35s}
.big-btn:hover{background:var(--gold-soft)}
.big-btn span{display:block;pointer-events:none}
.cta__mail{font-size:clamp(16px,1.6vw,22px);border-bottom:1px solid rgba(242,237,227,.4);padding-bottom:4px;transition:color .3s,border-color .3s;min-height:44px;display:inline-flex;align-items:center;overflow-wrap:anywhere}
.cta__mail:hover{color:var(--gold-soft);border-color:var(--gold-soft)}

/* ---------- footer ---------- */
.footer{position:relative;z-index:3;background:var(--ink);padding:90px 0 28px;overflow:hidden}
.footer__grid{display:grid;grid-template-columns:minmax(0,5fr) repeat(3,minmax(0,2fr)) minmax(0,4fr);gap:28px}
.footer__about{font-size:14px;line-height:1.6;color:var(--muted-on-dark);max-width:300px;margin:18px 0 0}
.footer h3{margin:0 0 16px;font-size:11.5px;font-weight:500;letter-spacing:.14em;text-transform:uppercase;color:var(--gold)}
.footer ul{list-style:none;margin:0;padding:0;display:grid;gap:0}
.footer li a{font-size:14px;color:var(--cream);transition:color .3s;display:inline-flex;min-height:44px;align-items:center}
.footer li a:hover{color:var(--gold-soft)}
.news{display:flex;border-bottom:1px solid rgba(242,237,227,.3);padding-bottom:8px}
.news input{flex:1;min-width:0;background:none;border:0;color:var(--cream);font:inherit;font-size:14px;padding:6px 0;min-height:44px}
.news input::placeholder{color:var(--muted-on-dark)}
.news input:focus{outline:none}
.news:focus-within{border-color:var(--gold)}
.news button{width:44px;height:44px;border-radius:50%;background:var(--gold);color:var(--ink);display:grid;place-items:center;flex:none}
.news button svg{width:13px;height:13px}
.news-msg{font-size:13px;color:var(--gold-soft);margin:10px 0 0;min-height:18px}
.wordmark{display:flex;justify-content:space-between;margin-top:80px;font-size:clamp(80px,21.5vw,330px);font-weight:500;line-height:.8;letter-spacing:-.04em;color:var(--cream);overflow:hidden;padding-bottom:.08em}
.wordmark span{display:inline-block}
.wordmark span:nth-child(odd){color:var(--gold)}
.footer__bottom{display:flex;justify-content:space-between;align-items:center;gap:16px;flex-wrap:wrap;margin-top:28px;padding-top:20px;border-top:1px solid var(--line-dark);font-size:12.5px;color:var(--muted-on-dark)}
.to-top{display:inline-flex;align-items:center;gap:8px;color:var(--cream);min-height:44px}
.to-top svg{width:12px;height:12px;transition:transform .4s var(--ease-out)}
.to-top:hover svg{transform:translateY(-3px)}

/* ---------- initial motion states ---------- */
.has-motion [data-reveal]{opacity:0}

/* ---------- studio (bento) + toolkit ---------- */
.studio{position:relative;z-index:3;background:var(--ink);padding:130px 0 120px}
.tag-pill{display:inline-flex;align-items:center;gap:8px;height:26px;padding:0 12px;border-radius:999px;border:1px solid rgba(242,237,227,.3);font-size:11px;font-weight:500;letter-spacing:.12em;text-transform:uppercase}
.tag-pill::after{content:"";width:5px;height:5px;border-right:1px solid currentColor;border-bottom:1px solid currentColor;transform:rotate(45deg) translate(-1px,-1px)}
.studio__head{display:flex;justify-content:space-between;align-items:flex-start;gap:24px}
.studio__mark{display:flex;align-items:center;gap:8px;font-size:13px;font-weight:600;letter-spacing:.16em;color:var(--cream)}
.studio__mark svg{width:20px;height:20px;color:var(--gold)}
.studio__title{margin:44px 0 0;max-width:860px;font-size:clamp(34px,4.9vw,74px);font-weight:400;line-height:1;letter-spacing:-.028em;text-transform:uppercase}
.studio__title em{font-style:normal;color:var(--gold)}
.studio__grid{display:grid;grid-template-columns:minmax(0,5fr) minmax(0,7fr);gap:clamp(24px,4vw,64px);margin-top:70px;align-items:stretch}
.studio__photo{margin:0;border-radius:18px;aspect-ratio:4/5;min-height:100%}
.studio__photo img{height:112%;margin-top:-6%}
.studio__right{display:flex;flex-direction:column;justify-content:space-between;gap:36px}
.studio__p{margin:0;max-width:520px;margin-left:auto;font-size:clamp(15px,1.25vw,18px);line-height:1.5;text-indent:3.2em}
.studio__team{display:flex;align-items:center;justify-content:space-between;gap:24px;max-width:520px;margin-left:auto;width:100%}
.studio__team p{margin:0;font-size:13px;line-height:1.5;color:var(--muted-on-dark);max-width:230px}
.avatars{display:flex;align-items:center;flex:none}
.avatars img{width:38px;height:38px;border-radius:50%;object-fit:cover;border:2px solid var(--ink);margin-left:-10px}
.avatars img:first-child{margin-left:0}
.avatars b{width:38px;height:38px;border-radius:50%;margin-left:-10px;background:var(--gold);color:var(--ink);font-size:11px;font-weight:600;display:grid;place-items:center;border:2px solid var(--ink)}
.studio__cards{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:14px}
.card-b{position:relative;border-radius:18px;padding:18px;min-height:clamp(260px,24vw,340px);display:flex;flex-direction:column;overflow:hidden}
.card-b--cream{background:var(--cream);color:var(--ink)}
.card-b__num{font-size:clamp(34px,3.4vw,50px);font-weight:400;letter-spacing:-.03em;line-height:1;font-variant-numeric:tabular-nums}
.card-b__label{font-size:13px;font-weight:500;margin-top:6px}
.card-b__sub{font-size:11.5px;color:var(--muted-on-light);margin-top:4px;max-width:170px;line-height:1.4}
.partners{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px 10px;margin:auto 0 18px;font-size:12px;font-weight:500;color:var(--muted-card)}
.partners span{display:flex;align-items:center;gap:7px;white-space:nowrap}
.partners i{width:11px;height:11px;border:1.5px solid var(--muted-card);border-radius:3px;flex:none}
.partners span:nth-child(2n) i{border-radius:50%}
.partners span:nth-child(3n) i{transform:rotate(45deg);border-radius:2px}
.btn-gold{display:inline-flex;align-items:center;justify-content:space-between;gap:14px;height:44px;padding:0 5px 0 16px;border-radius:999px;background:var(--gold);color:var(--ink);font-size:12px;font-weight:600;align-self:flex-start;transition:background-color .35s}
.btn-gold:hover{background:var(--gold-soft)}
.btn-gold i,.btn-line i{width:34px;height:34px;border-radius:50%;background:var(--cream);display:grid;place-items:center;flex:none}
.btn-gold svg,.btn-line svg{width:13px;height:13px;transition:transform .45s var(--ease-out)}
.btn-gold:hover svg,.btn-line:hover svg{transform:rotate(45deg)}
.card-b--photo{color:var(--cream);justify-content:flex-end;padding:0}
.card-b--photo .media{position:absolute;inset:0}
.card-b--photo::after{content:"";position:absolute;inset:0;background:linear-gradient(180deg,rgba(21,18,12,0) 35%,rgba(21,18,12,.88) 100%)}
.card-b--photo .card-b__in{position:relative;z-index:2;padding:18px}
.card-b--photo .card-b__sub{color:var(--muted-on-dark)}

.toolkit{margin-top:130px}
.toolkit__head{text-align:right;margin:0 0 50px;font-size:clamp(34px,4.9vw,74px);font-weight:400;line-height:1;letter-spacing:-.028em;text-transform:uppercase}
.toolkit__head em{font-style:normal;color:var(--gold)}
.tiles{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));grid-template-areas:"a b t c" "p p d e";gap:14px}
.tile{position:relative;display:flex;flex-direction:column;justify-content:space-between;gap:24px;min-height:clamp(240px,22vw,320px);padding:18px;border-radius:18px;transition:background-color .5s,transform .6s var(--ease-out),color .4s}
.tile--cream{background:var(--cream);color:var(--ink)}
.tile--gold{background:var(--gold);color:var(--ink)}
@media (hover:hover) and (pointer:fine){.tile--cream:hover{background:var(--gold)}.tile--gold:hover{background:var(--cream)}}
.tile__top{display:flex;justify-content:space-between;align-items:flex-start;gap:14px}
.tile__title{margin:0;font-size:clamp(24px,2.5vw,38px);font-weight:400;line-height:.98;letter-spacing:-.025em}
.tile__arrow{width:34px;height:34px;border-radius:50%;background:var(--ink);color:var(--cream);display:grid;place-items:center;flex:none;transition:background-color .4s,color .4s}
.tile--gold .tile__arrow{background:var(--cream);color:var(--ink)}
.tile:hover .tile__arrow{background:var(--ink);color:var(--cream)}
.tile--gold:hover .tile__arrow{background:var(--ink);color:var(--cream)}
.tile__arrow svg{width:13px;height:13px;transition:transform .5s var(--ease-out)}
.tile:hover .tile__arrow svg{transform:rotate(45deg)}
.tile__desc{margin:0;font-size:13px;line-height:1.45;max-width:240px;color:rgba(21,18,12,.74)}
.tile--gold .tile__desc{color:var(--ink)}
.tile--a{grid-area:a}.tile--b{grid-area:b}.tile--c{grid-area:c}.tile--d{grid-area:d}.tile--e{grid-area:e}
.tile-note{grid-area:t;display:grid;place-items:center;text-align:center;font-size:clamp(14px,1.2vw,17px);line-height:1.3;color:var(--cream);padding:0 18px}
.tile-note p{margin:0;max-width:170px}
.tile-text{grid-area:p;display:flex;flex-direction:column;justify-content:flex-end;gap:22px;padding:0 6px 4px 0}
.tile-text p{margin:0;max-width:430px;font-size:15px;line-height:1.55;color:var(--muted-on-dark)}
.btn-line{display:inline-flex;align-items:center;gap:14px;height:44px;padding:0 5px 0 16px;border-radius:999px;border:1px solid rgba(242,237,227,.4);color:var(--cream);font-size:12px;font-weight:500;align-self:flex-start;transition:background-color .35s,border-color .35s,color .35s}
.btn-line i{background:var(--cream);color:var(--ink)}
.btn-line:hover{background:var(--gold);border-color:var(--gold);color:var(--ink)}

.tiles>*,.studio__grid>*,.studio__cards>*,.studio__right>*,.process__grid>*,.numbers__grid>*,.footer__grid>*{min-width:0}
.tile__title,.service__title,.project__name,.arc__name,.card-b__label,.step__title,.num__value,.card-b__num,.partners span{overflow-wrap:anywhere}

/* ---------- showcase: curved carousel ---------- */
.showcase{position:relative;z-index:3;padding:120px 0 130px;color:var(--brown);background:
  radial-gradient(ellipse 60% 70% at 15% 10%,rgba(255,236,196,.55),transparent 60%),
  radial-gradient(ellipse 50% 60% at 90% 30%,rgba(158,112,62,.5),transparent 65%),
  radial-gradient(ellipse 70% 60% at 50% 100%,rgba(120,84,46,.45),transparent 70%),var(--tan)}
.showcase__head{display:flex;justify-content:space-between;align-items:flex-start;gap:32px}
.showcase__title{margin:0;font-size:clamp(34px,4.6vw,68px);font-weight:500;line-height:.98;letter-spacing:-.025em;text-transform:uppercase;color:var(--brown-title)}
.showcase__intro{margin:8px 0 0;max-width:380px;font-size:clamp(15px,1.3vw,19px);line-height:1.35;color:var(--brown)}
.showcase__panel{margin-top:56px;background:var(--ink);color:var(--cream);border-radius:22px;padding:46px 0 40px;box-shadow:0 0 0 10px rgba(242,237,227,.3);overflow:hidden}
.showcase__top{display:flex;align-items:center;gap:clamp(18px,3vw,44px);padding:0 clamp(20px,4vw,56px)}
.showcase__top > p:not(.showcase__big){margin:0;max-width:230px;font-size:13px;line-height:1.5;color:var(--muted-on-dark);flex:none}
.showcase__big{margin:0;flex:1;text-align:right;font-size:clamp(48px,9vw,136px);font-weight:600;line-height:.9;letter-spacing:-.02em;text-transform:uppercase;white-space:nowrap}
.arc{position:relative;height:clamp(380px,35vw,480px);margin-top:26px;perspective:1500px;touch-action:pan-y;cursor:grab}
.arc:focus-visible{outline:2px solid var(--gold);outline-offset:-8px;border-radius:18px}
.arc__card{position:absolute;left:50%;top:50%;width:clamp(150px,15vw,224px);aspect-ratio:3/4.15;margin:calc(clamp(150px,15vw,224px) * -.6915) 0 0 calc(clamp(150px,15vw,224px) * -.5);border-radius:16px;overflow:hidden;background:var(--ink-3);will-change:transform;transform-style:preserve-3d;-webkit-user-select:none;user-select:none}
.arc__card img{width:100%;height:100%;object-fit:cover;pointer-events:none}
.arc__card::after{content:"";position:absolute;inset:0;background:linear-gradient(180deg,rgba(21,18,12,.05) 40%,rgba(21,18,12,.7) 100%);pointer-events:none}
.arc__name{position:absolute;z-index:2;left:12px;right:12px;bottom:14px;font-size:11px;font-weight:500;letter-spacing:.1em;text-transform:uppercase;text-align:center;line-height:1.3}
.arc__card.is-center .arc__name{font-size:12.5px;text-align:left;text-transform:none;letter-spacing:0;font-weight:400;left:16px;bottom:18px}
.arc__chip{position:absolute;z-index:2;left:12px;top:12px;height:24px;padding:0 11px;border-radius:999px;background:rgba(242,237,227,.22);backdrop-filter:blur(8px);-webkit-backdrop-filter:blur(8px);font-size:11px;font-weight:500;display:flex;align-items:center;opacity:0;transform:translateY(6px);transition:opacity .5s .2s,transform .6s .2s var(--ease-out)}
.arc__card.is-center .arc__chip{opacity:1;transform:none}
.arc__nav{display:flex;justify-content:center;gap:12px;margin-top:22px;position:relative;z-index:30}
.arc__btn{width:56px;height:44px;border-radius:999px;border:1px solid rgba(242,237,227,.4);display:grid;place-items:center;color:var(--cream);transition:background-color .35s,color .35s,border-color .35s}
.arc__btn:hover{background:var(--cream);color:var(--ink)}
.arc__btn svg{width:16px;height:16px}
.btn-gold,.btn-line,.header__cta,.big-btn,.arc__btn,.icon-btn,.menu-btn,.pill,.news button{transition-property:background-color,color,border-color,scale;transition-duration:.35s,.35s,.35s,.16s;transition-timing-function:ease,ease,ease,var(--ease-out)}
.btn-gold:active,.btn-line:active,.header__cta:active,.big-btn:active,.arc__btn:active,.icon-btn:active,.menu-btn:active,.pill:active,.news button:active{scale:.97}
html:not(.motion-ready) .arc{height:auto;display:flex;gap:14px;overflow-x:auto;padding:0 clamp(20px,4vw,56px);perspective:none;cursor:auto;scroll-snap-type:x mandatory}
html:not(.motion-ready) .arc__card{position:relative;left:auto;top:auto;margin:0;flex:none;scroll-snap-align:center}
html:not(.motion-ready) .arc__chip{opacity:1;transform:none}
html:not(.motion-ready) .arc__nav{display:none}

/* ---------- testimonials: auto-advancing carousel (dot grid, image-coloured cards) ---------- */
.ll{position:relative;z-index:3;container-type:inline-size;container-name:ll;overflow:clip;color:var(--cream);
  background-color:var(--ink);}
.ll-in{
  --u:calc(min(100cqw,1920px) / 1600);
  --cw:calc(1290 * var(--u)); --tw:calc(557 * var(--u)); --h:calc(550 * var(--u));
  --gap:calc(12 * var(--u)); --r:calc(60 * var(--u));
  --pad-t:calc(71 * var(--u)); --pad-x:calc(79 * var(--u)); --pad-b:calc(78 * var(--u));
  --ts:calc(47.5 * var(--u)); --bs:calc(23 * var(--u)); --blh:calc(29.5 * var(--u)); --bys:max(calc(15 * var(--u)),12px);
  --bar:calc(4 * var(--u)); --bar-gap:calc(22 * var(--u));
  --btn:calc(74 * var(--u)); --btn-b:calc(3 * var(--u)); --icon:calc(28 * var(--u));
  --pt:calc(120 * var(--u)); --pb:calc(120 * var(--u)); --nav-gap:calc(110 * var(--u));
  --card-off:#211b11; --title-off:#6b5f47; --body-off:#5a4f39; --track-off:#332b1c; --line:#3a3121;
  --btn-hover:#2a2318; --btn-press:#7a6d52; --ink-t:var(--cream); --img-off:.2;
  --state:1450ms ease; --spring:cubic-bezier(.3,2.1,.5,1);
  padding:var(--pt) 0 var(--pb)}
.ll-nav{width:var(--cw);margin:0 auto var(--nav-gap);display:flex;justify-content:flex-end;gap:var(--gap)}
.ll-btn{width:var(--btn);height:var(--btn);flex:0 0 auto;border-radius:50%;border:var(--btn-b) solid var(--line);background:transparent;color:var(--ink-t);display:grid;place-items:center;cursor:pointer;-webkit-tap-highlight-color:transparent;touch-action:manipulation;transition:background-color .2s ease,transform .2s ease}
.ll-btn svg{width:var(--icon);height:calc(var(--icon) * 22 / 28);display:block;overflow:visible}
@media (hover:hover) and (pointer:fine){.ll-btn:hover{background:var(--btn-hover)}}
.ll-btn.is-press{transform:scale(.8);background:var(--btn-press);transition:background-color .18s ease-out,transform .18s ease-out}
.ll-btn.is-rel{transition:background-color .6s var(--spring),transform .6s var(--spring)}
.ll-btn:focus-visible{outline:2px solid var(--gold);outline-offset:3px}
.ll-view{width:100%}
.ll-track{display:flex;gap:var(--gap);width:max-content;padding-left:calc((100cqw - var(--cw)) / 2);will-change:transform}
.ll-slide{flex:0 0 var(--cw);width:var(--cw);height:var(--h);display:flex;gap:var(--gap)}
.ll-text{flex:0 0 var(--tw);height:100%;border-radius:var(--r);background:var(--card-off);padding:var(--pad-t) var(--pad-x) var(--pad-b);display:flex;flex-direction:column;justify-content:space-between;transition:background-color var(--state)}
.ll-title{max-width:8.45em;text-wrap:balance;font-weight:600;font-size:var(--ts);line-height:1;letter-spacing:-.02em;color:var(--title-off);transition:color var(--state)}
.ll-by{margin-bottom:calc(14 * var(--u));font-size:var(--bys);font-weight:600;letter-spacing:.08em;text-transform:uppercase;line-height:1.3;color:var(--body-off);transition:color var(--state)}
.ll-body{font-weight:400;font-size:var(--bs);line-height:var(--blh);color:var(--body-off);transition:color var(--state)}
.ll-bar{position:relative;height:var(--bar);margin-top:var(--bar-gap);border-radius:var(--bar);background:var(--track-off);overflow:hidden;transition:background-color var(--state)}
.ll-fill{position:absolute;left:0;top:0;bottom:0;width:var(--bar);border-radius:var(--bar);background:var(--fill);opacity:0;transition:opacity var(--state)}
.ll-media{flex:1 1 auto;height:100%;border-radius:var(--r);overflow:hidden;background:var(--ink-3);opacity:var(--img-off);transition:opacity var(--state)}
.ll-media img{width:100%;height:100%;object-fit:cover;display:block}
.ll-media img.broken{visibility:hidden}
.ll-slide.is-on .ll-text{background:var(--card)}
.ll-slide.is-on .ll-title{color:var(--title)}
.ll-slide.is-on .ll-by,.ll-slide.is-on .ll-body{color:var(--body)}
.ll-slide.is-on .ll-bar{background:var(--track)}
.ll-slide.is-on .ll-fill{opacity:1}
.ll-slide.is-on .ll-media{opacity:1}
.ll-jump *,.ll-jump{transition:none !important}
@container ll (max-width:1099.98px){
  .ll-in{--cw:calc(100cqw - 80px);--tw:calc(var(--cw) * .46);--h:440px;--gap:12px;--r:36px;--pad-t:36px;--pad-x:36px;--pad-b:38px;--ts:30px;--bs:16px;--blh:22px;--bys:12px;--bar:3px;--bar-gap:18px;--btn:60px;--btn-b:2px;--icon:24px;--pt:80px;--pb:80px;--nav-gap:48px}
}
@container ll (max-width:767.98px){
  .ll-in{--cw:calc(100cqw - 32px);--h:auto;--r:28px;--pad-t:28px;--pad-x:26px;--pad-b:28px;--ts:28px;--bs:16px;--blh:22px;--bar-gap:18px;--btn:52px;--icon:22px;--pt:56px;--pb:56px;--nav-gap:28px}
  .ll-slide{flex-direction:column}
  .ll-text{flex:0 0 auto;height:auto;min-height:300px}
  .ll-media{flex:0 0 auto;height:calc(var(--cw) * .76)}
}
@media (prefers-reduced-motion:reduce){
  .ll-in{--state:0s linear}
  .ll-btn,.ll-btn.is-press,.ll-btn.is-rel{transition:none}
}

/* ---------- tablet ---------- */
@media (max-width:1100px){
  .nav{display:none}
  .header__icons{margin-left:auto}
  .services{grid-template-columns:repeat(2,minmax(0,1fr));gap:14px}
  .service:nth-child(2){margin-top:0}
  .service{aspect-ratio:4/4.4}
  .footer__grid{grid-template-columns:repeat(3,minmax(0,1fr))}
  .footer__brand,.footer__news{grid-column:1 / -1}
}
@media (max-width:1023px){
  .hero__content{grid-template-columns:minmax(0,1fr);grid-template-rows:auto 1fr auto;padding-top:110px}
  .hero__left{grid-row:1}
  .hero__right{grid-column:1;grid-row:2;justify-content:flex-end;padding-bottom:28px}
  .hero__shade{background:linear-gradient(180deg,rgba(21,18,12,.88) 0%,rgba(21,18,12,.62) 26%,rgba(21,18,12,.25) 42%,rgba(21,18,12,.6) 62%,rgba(21,18,12,.94) 100%)}
  .hero__lead,.hero__aside p{color:rgba(242,237,227,.82)}
  .hero__bottom{grid-row:3;flex-direction:column;align-items:stretch}
  .hero__pills-wrap{align-items:flex-start}
  .pills{justify-content:flex-start}
  .about__top{grid-template-columns:1fr}
  .about__title{grid-column:1}
  .asterisk{position:absolute;left:var(--gutter);top:56px;width:34px;height:34px}
  .about__kicker{display:none}
  .about__body{grid-template-columns:1fr;gap:36px}
  .about__body > div:first-child{order:2;text-align:center}
  .gallery{margin:0 auto;max-width:none;width:100%}
  .process__grid{grid-template-columns:minmax(0,1fr)}
  .process__sticky{position:static}
  .process__media{display:none}
  .numbers__grid{grid-template-columns:repeat(2,minmax(0,1fr))}
  .num:nth-child(2){border-right:0}
  .num:nth-child(3){padding-left:0}
  .num:nth-child(-n+2){border-bottom:1px solid var(--line-dark)}
}
/* ---------- phone ---------- */
@media (max-width:767px){
  body{font-size:14px}
  .header__inner{height:70px}
  .header__bg{inset:8px 8px}
  .logo small{display:none}
  .header__icons .icon-btn:first-child{display:none}
  .hero{min-height:680px}
  .hero__content{padding:96px var(--gutter) 24px}
  .hero__left{padding-top:8px}
  .hero__right{align-items:flex-start;text-align:left;justify-content:flex-end;padding-bottom:22px}
  .hero__title{font-size:clamp(38px,11.4vw,54px)}
  .hero__aside{margin-top:18px}
  .hero__img img{object-position:56% 30%}
  .hero__cards{gap:8px}
  .stat-card,.glass-card{width:auto;flex:1;height:150px}
  .stat-card__num{font-size:36px}
  .scroll-hint{display:none}
  .pills{flex-wrap:nowrap;overflow-x:auto;width:100%;scrollbar-width:none;margin:0 calc(var(--gutter) * -1);padding:0 var(--gutter);width:calc(100% + var(--gutter) * 2)}
  .pills::-webkit-scrollbar{display:none}
  .pill{flex:none}
  .about{padding:90px 0 90px;border-radius:24px 24px 0 0;margin-top:-24px}
  .asterisk{top:30px}
  .about__title{font-size:clamp(28px,8.6vw,44px)}
  .gallery{display:flex;overflow-x:auto;scroll-snap-type:x mandatory;gap:10px;margin:0 calc(var(--gutter) * -1);padding:0 var(--gutter);width:calc(100% + var(--gutter) * 2);scrollbar-width:none}
  .gallery::-webkit-scrollbar{display:none}
  .gallery__item{flex:none;width:68vw;scroll-snap-align:center}
  .story{padding:20px 0 90px}
  .story__title{font-size:clamp(26px,7.4vw,40px)}
  .services{display:flex;overflow-x:auto;scroll-snap-type:x mandatory;margin:56px calc(var(--gutter) * -1) 0;padding:0 var(--gutter);scrollbar-width:none}
  .services::-webkit-scrollbar{display:none}
  .service{flex:none;width:74vw;aspect-ratio:8/11;scroll-snap-align:center}
  .service .service__more{grid-template-rows:1fr}
  .service .service__arrow{transform:none}
  .work__pin{height:auto;padding:90px 0 70px;gap:30px}
  .work__head{flex-direction:column;align-items:flex-start}
  .work__track{flex-direction:column;width:auto;gap:34px;transform:none!important}
  .project{width:100%}
  .work__progress{display:none}
  .num{padding:28px 12px 30px 0}
  .num + .num{padding-left:12px}
  .num:nth-child(3){padding-left:0}
  .marquee{padding:32px 0 38px}
  .process{padding:90px 0}
  .steps{padding-left:26px}
  .step::before{left:-30px}
  .manifesto{height:auto;min-height:0;padding:140px 0}
  .cta__frame{min-height:560px}
  .big-btn{width:128px;height:128px}
  .footer{padding-top:70px}
  .footer__grid{grid-template-columns:repeat(2,minmax(0,1fr))}
  .wordmark{margin-top:56px}
}
@media (max-width:1100px){
  .tiles{grid-template-columns:repeat(2,minmax(0,1fr));grid-template-areas:"a b" "c d" "e t" "p p"}
  .tile-note{padding:24px 0}
}
@media (max-width:1023px){
  .studio{padding:100px 0 90px}
  .studio__grid{grid-template-columns:minmax(0,1fr);gap:28px;margin-top:50px}
  .studio__photo{min-height:0;aspect-ratio:4/4.4}
  .studio__p,.studio__team{margin-left:0}
  .toolkit{margin-top:100px}
  .showcase__head{flex-direction:column;gap:18px}
  .showcase__top{flex-direction:column;align-items:flex-start;gap:16px}
  .showcase__top > p:not(.showcase__big){max-width:none}
  .showcase__big{font-size:clamp(44px,12vw,100px);text-align:left;width:100%}
}
@media (max-width:767px){
  .studio__title{margin-top:34px}
  .studio__cards{grid-template-columns:minmax(0,1fr)}
  .card-b{min-height:270px}
  .card-b--photo{min-height:300px}
  .toolkit{margin-top:80px}
  .toolkit__head{text-align:left;margin-bottom:34px}
  .tiles{grid-template-columns:minmax(0,1fr);grid-template-areas:"a" "b" "t" "c" "d" "e" "p";gap:12px}
  .tile{min-height:230px}
  .showcase{padding:90px 0 90px}
  .showcase__panel{margin-top:40px;padding:32px 0 30px;box-shadow:0 0 0 6px rgba(242,237,227,.3)}
  .showcase__top{flex-direction:column;align-items:flex-start;gap:14px}
  .showcase__top > p:not(.showcase__big){max-width:none}
  .showcase__big{text-align:left;font-size:clamp(40px,13.4vw,80px)}
  .arc{height:400px}
  .arc__card{width:48vw;margin:calc(48vw * -.6915) 0 0 -24vw}
}
@media (max-width:1023px),(pointer:coarse){
  .pill{height:44px}
}
@media (max-width:480px){.hide-xs{display:none}}
@media (prefers-reduced-motion:reduce){
  *,*::before,*::after{animation-duration:.001ms!important;animation-iteration-count:1!important;transition-duration:.001ms!important;scroll-behavior:auto!important}
}
</style>
<noscript><style>.ll-track{width:auto;flex-direction:column;padding:0 16px}.ll-slide{flex:none;width:auto;flex-direction:column;height:auto}.ll-text{flex:none;height:auto;background:var(--card);padding:28px 26px}.ll-title{color:var(--title)}.ll-by,.ll-body{color:var(--body)}.ll-media{flex:none;height:320px;opacity:1}.ll-nav{display:none}</style></noscript>
</head>
<body>
<a class="skip" href="#main">Skip to content</a>

<!-- Loader -->
<div class="loader" aria-hidden="true">
  <div class="loader__brand">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M12 21V6"/><path d="M12 21C12 15 7.5 11.5 6 5"/><path d="M12 21C12 15 16.5 11.5 18 5"/></svg>
    <span>SAFFRA</span>
  </div>
  <div class="loader__bar"><i></i></div>
  <div class="loader__count">000</div>
</div>

<div class="cursor" aria-hidden="true">
  <svg class="cursor__arrow" viewBox="0 0 26 28" aria-hidden="true"><path d="M4 3 L22 16.5 L12.5 17.8 L6.5 25.5 Z"/></svg>
  <span class="cursor__label">View</span>
</div>

<!-- Header -->
<header class="header">
  <div class="header__inner">
    <div class="header__bg"></div>
    <div class="header__left">
      <a class="logo" href="#top" aria-label="Saffra Design, back to top">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" aria-hidden="true"><path d="M12 21V6"/><path d="M12 21C12 15 7.5 11.5 6 5"/><path d="M12 21C12 15 16.5 11.5 18 5"/></svg>
        <b>SAFFRA</b><small>DESIGN</small>
      </a>
      <button class="menu-btn" type="button" aria-label="Open menu" aria-expanded="false" aria-controls="menu"><i></i></button>
    </div>
    <nav class="nav" aria-label="Main">
      <a href="#top" class="is-active">Home</a>
      <a href="#services">Services</a>
      <a href="#work">Work</a>
      <a href="#process">Studio</a>
    </nav>
    <div class="header__icons">
      <a class="icon-btn" href="#contact" aria-label="Write to us">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6"><rect x="3" y="5" width="18" height="14" rx="3"/><path d="m4 7 8 6 8-6"/></svg>
      </a>
      <a class="header__cta" href="#contact"><span>Start<span class="hide-xs"> a project</span></span><i><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" aria-hidden="true"><path d="M7 17 17 7M9 7h8v8"/></svg></i></a>
    </div>
  </div>
</header>

<!-- Overlay menu -->
<div class="menu" id="menu" role="dialog" aria-modal="true" aria-label="Menu">
  <button class="menu__close" type="button" aria-label="Close menu"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"><path d="M5 5l14 14M19 5 5 19"/></svg></button>
  <ul class="menu__list">
    <li><a href="#top"><span>01</span>Home</a></li>
    <li><a href="#services"><span>02</span>Services</a></li>
    <li><a href="#work"><span>03</span>Work</a></li>
    <li><a href="#process"><span>04</span>Studio</a></li>
    <li><a href="#contact"><span>05</span>Contact</a></li>
  </ul>
  <div class="menu__foot"><span>hello@saffra.studio</span><span>Lisbon · Remote worldwide</span></div>
</div>

<main id="main">
  <!-- 1. Hero -->
  <section class="hero" id="top" aria-label="Introduction">
    <div class="hero__frame">
      <div class="hero__img"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/hero-sunset.webp" alt="Woman in profile at sunset on a golden hillside, half hidden behind a tree trunk" width="2400" height="1350" fetchpriority="high" crossorigin="anonymous"><canvas class="hero__water img-grade" aria-hidden="true"></canvas></div>
      <div class="hero__shade"></div>
      <div class="hero__dim"></div>
    </div>
    <div class="hero__content">
      <div class="hero__left">
        <p class="hero__tag" data-hero-split>Clear ideas,<br>lasting impact.</p>
        <p class="hero__lead" data-hero-fade>Saffra shapes strategy, identity and digital work for brands that want to be remembered.</p>
      </div>
      <div class="hero__right">
        <h1 class="hero__title" data-hero-split>Creative<br>Strategy<br>Studio</h1>
        <div class="hero__aside" data-hero-fade>
          <p>We turn sharp insight into brand systems people notice, trust and come back to.</p>
          <a class="btn-line" href="#contact" data-magnetic>Start a project <i><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" aria-hidden="true"><path d="M7 17 17 7M9 7h8v8"/></svg></i></a>
        </div>
      </div>
      <div class="hero__bottom">
        <div class="hero__cards">
          <div class="stat-card" data-hero-card>
            <div class="stat-card__top">
              <span class="stat-card__icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M12 21V6"/><path d="M12 21C12 15 7.5 11.5 6 5"/><path d="M12 21C12 15 16.5 11.5 18 5"/></svg></span>
              <span>Brand<br>Growth</span>
            </div>
            <div>
              <div class="stat-card__num"><span data-count="94">94</span>%</div>
              <div class="stat-card__label">Client retention</div>
            </div>
          </div>
          <div class="glass-card" data-hero-card aria-live="polite">
            <div class="glass-card__swap"><p class="glass-card__title">Shape Your<br>Brand</p></div>
            <div class="glass-card__swap"><p class="glass-card__text">Positioning and research that point every decision in one direction.</p></div>
          </div>
        </div>
        <div class="hero__pills-wrap" data-hero-fade>
          <div class="scroll-hint"><i></i>Scroll</div>
          <div class="pills" role="tablist" aria-label="What we do">
            <button class="pill" role="tab" aria-selected="true" data-tab="0">Strategy</button>
            <button class="pill" role="tab" aria-selected="false" data-tab="1">Branding</button>
            <button class="pill" role="tab" aria-selected="false" data-tab="2">Digital</button>
            <button class="pill" role="tab" aria-selected="false" data-tab="3">Content</button>
            <button class="pill" role="tab" aria-selected="false" data-tab="4">Design</button>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- 2. About -->
  <section class="about light" id="about" aria-labelledby="about-title">
    <div class="wrap">
      <div class="about__top">
        <svg class="asterisk" viewBox="0 0 48 48" fill="currentColor" aria-hidden="true"><rect x="21" y="2" width="6" height="44" rx="3"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(45 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(90 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(135 24 24)"/></svg>
        <h2 class="about__title fill" id="about-title" data-fill>Saffra builds brands<br>that resonate <span class="switch keep" aria-hidden="true"><span class="switch__knob"><img src="https://assets.layerweb.com.br/saffra-design/about-portrait.webp" alt="" width="900" height="1120" loading="lazy"></span></span> and<br><em class="accent">spark lasting</em><br>connections</h2>
        <p class="about__kicker">Building<br>what comes next</p>
      </div>
      <p class="about__lead" data-reveal>Research, design and technology working as one. We take your idea from first sketch to public launch and keep every touchpoint in the same voice.</p>
      <div class="about__body">
        <div><a class="link-under" href="#work" data-reveal>Browse our<br>newest<br>case studies</a></div>
        <div class="gallery">
          <figure class="gallery__item media" data-clip data-cursor="View" style="margin:0"><img class="img-grade" data-speed="-6" src="https://assets.layerweb.com.br/saffra-design/about-plant-warm.webp" alt="Heart-shaped leaves of a climbing plant against a sunlit wall" width="900" height="1120" loading="lazy"></figure>
          <figure class="gallery__item media" data-clip data-cursor="View" style="margin:0"><img class="img-grade" data-speed="-12" src="https://assets.layerweb.com.br/saffra-design/about-vase.webp" alt="Clay vase with dried flowers casting long shadows in late sun" width="900" height="1120" loading="lazy"></figure>
          <figure class="gallery__item media" data-clip data-cursor="View" style="margin:0"><img class="img-grade" data-speed="-6" src="https://assets.layerweb.com.br/saffra-design/about-portrait.webp" alt="Close-up of a face lit by a band of warm amber light" width="900" height="1120" loading="lazy"></figure>
        </div>
      </div>
    </div>
  </section>

  <!-- 3. Story + services -->
  <section class="story light" id="services" aria-labelledby="story-title">
    <div class="wrap">
      <h2 class="story__title" id="story-title" data-split>Each brand holds a story.<br>We make it <em class="accent">impossible to miss.</em></h2>
      <p class="story__lead" data-reveal>From positioning to product launches, Saffra gives growing brands the clarity and craft they need to stand out in crowded markets.</p>
      <div class="services">
        <a class="service" href="#contact" data-tilt data-cursor="Explore">
          <div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/service-strategy.webp" alt="Golden king chess piece on a dark board" width="800" height="1100" loading="lazy"></div>
          <span class="service__tag">Brand Strategy</span><span class="service__num">01</span>
          <div class="service__body"><h3 class="service__title">Brand<br>Strategy</h3><div class="service__more"><div><p>Research, positioning and messaging that give every choice a reason.</p></div></div><span class="service__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div>
        </a>
        <a class="service" href="#contact" data-tilt data-cursor="Explore">
          <div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/service-identity.webp" alt="Abstract sculpture of polished golden rings" width="800" height="1100" loading="lazy"></div>
          <span class="service__tag">Visual Identity</span><span class="service__num">02</span>
          <div class="service__body"><h3 class="service__title">Visual<br>Identity</h3><div class="service__more"><div><p>Logos, type and color systems built to work at every size.</p></div></div><span class="service__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div>
        </a>
        <a class="service" href="#contact" data-tilt data-cursor="Explore">
          <div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/service-digital.webp" alt="Open laptop on a desk crossed by warm window light" width="800" height="1100" loading="lazy"></div>
          <span class="service__tag">Digital Experiences</span><span class="service__num">03</span>
          <div class="service__body"><h3 class="service__title">Digital<br>Experiences</h3><div class="service__more"><div><p>Websites and products that load fast, read clearly and convert.</p></div></div><span class="service__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div>
        </a>
        <a class="service" href="#contact" data-tilt data-cursor="Explore">
          <div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/service-content.webp" alt="Vintage rangefinder camera on a dark wooden table" width="800" height="1100" loading="lazy"></div>
          <span class="service__tag">Creative Content</span><span class="service__num">04</span>
          <div class="service__body"><h3 class="service__title">Creative<br>Content</h3><div class="service__more"><div><p>Photo, film and social stories that keep people watching.</p></div></div><span class="service__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div>
        </a>
      </div>
    </div>
  </section>

  <!-- 4. Selected work (pinned horizontal scroll) -->
  <section class="work" id="work" aria-labelledby="work-title">
    <div class="work__pin">
      <div class="wrap work__head">
        <h2 class="work__title" id="work-title" data-split>Selected<br>work<sup>(05)</sup></h2>
        <p class="work__intro" data-reveal>A few recent launches across beauty, home and culture. Each one started with a single question: what should people feel?</p>
      </div>
      <div class="work__track">
        <a class="project" href="#contact" data-cursor="View"><div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/work-arch.webp" alt="Cream arch and round podium set for a product display" width="1600" height="1100" loading="lazy"></div><div class="project__meta"><h3 class="project__name">Arco Atelier</h3><span class="project__cat">Identity · <span class="project__year">2026</span></span></div></a>
        <a class="project" href="#contact" data-cursor="View"><div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/work-scarf.webp" alt="Woman in a golden patterned scarf lit by dappled sunlight" width="1600" height="1100" loading="lazy"></div><div class="project__meta"><h3 class="project__name">Hollow &amp; Field</h3><span class="project__cat">Campaign · <span class="project__year">2026</span></span></div></a>
        <a class="project" href="#contact" data-cursor="View"><div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/work-podium.webp" alt="Brushed gold podium on a soft cream backdrop with fern shadows" width="1600" height="1100" loading="lazy"></div><div class="project__meta"><h3 class="project__name">Oro Skin</h3><span class="project__cat">Packaging · <span class="project__year">2025</span></span></div></a>
        <a class="project" href="#contact" data-cursor="View"><div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/work-silk.webp" alt="Flowing golden fabric sculpture on a dark background" width="1600" height="1100" loading="lazy"></div><div class="project__meta"><h3 class="project__name">Vela Studio</h3><span class="project__cat">Digital · <span class="project__year">2025</span></span></div></a>
        <a class="project" href="#contact" data-cursor="View"><div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/work-vessel.webp" alt="White ceramic vessel with a curved neck against black" width="1600" height="1100" loading="lazy"></div><div class="project__meta"><h3 class="project__name">Terra Form</h3><span class="project__cat">Art direction · <span class="project__year">2024</span></span></div></a>
      </div>
      <div class="wrap work__progress" aria-hidden="true"><span class="work__count">01</span><div class="work__bar"><i></i></div><span>05</span></div>
    </div>
  </section>

  <!-- 5. Numbers + marquee -->
  <section class="numbers" aria-label="Studio in numbers">
    <div class="wrap numbers__grid">
      <div class="num" data-reveal><div class="num__value"><span data-count="12">12</span><em>+</em></div><p class="num__label">Years shaping brands across three continents</p></div>
      <div class="num" data-reveal><div class="num__value"><span data-count="180">180</span><em>+</em></div><p class="num__label">Brands launched, renamed or rebuilt</p></div>
      <div class="num" data-reveal><div class="num__value"><span data-count="32">32</span></div><p class="num__label">Design and strategy awards</p></div>
      <div class="num" data-reveal><div class="num__value"><span data-count="94">94</span><em>%</em></div><p class="num__label">Clients who return for the next project</p></div>
    </div>
    <div class="marquee" aria-hidden="true">
      <div class="marquee__track">
        <div class="marquee__group">
          <span class="marquee__item">Strategy</span><svg class="marquee__star" viewBox="0 0 48 48" fill="currentColor"><rect x="21" y="2" width="6" height="44" rx="3"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(45 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(90 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(135 24 24)"/></svg>
          <span class="marquee__item dim">Branding</span><svg class="marquee__star" viewBox="0 0 48 48" fill="currentColor"><rect x="21" y="2" width="6" height="44" rx="3"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(45 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(90 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(135 24 24)"/></svg>
          <span class="marquee__item">Digital</span><svg class="marquee__star" viewBox="0 0 48 48" fill="currentColor"><rect x="21" y="2" width="6" height="44" rx="3"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(45 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(90 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(135 24 24)"/></svg>
          <span class="marquee__item dim">Content</span><svg class="marquee__star" viewBox="0 0 48 48" fill="currentColor"><rect x="21" y="2" width="6" height="44" rx="3"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(45 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(90 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(135 24 24)"/></svg>
          <span class="marquee__item">Design</span><svg class="marquee__star" viewBox="0 0 48 48" fill="currentColor"><rect x="21" y="2" width="6" height="44" rx="3"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(45 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(90 24 24)"/><rect x="21" y="2" width="6" height="44" rx="3" transform="rotate(135 24 24)"/></svg>
        </div>
      </div>
    </div>
  </section>

  <!-- 5b. Studio + toolkit -->
  <section class="studio" id="studio" aria-labelledby="studio-title">
    <div class="wrap">
      <div class="studio__head">
        <span class="tag-pill">About us</span>
        <span class="studio__mark"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" aria-hidden="true"><path d="M12 21V6"/><path d="M12 21C12 15 7.5 11.5 6 5"/><path d="M12 21C12 15 16.5 11.5 18 5"/></svg>SAFFRA</span>
      </div>
      <h2 class="studio__title" id="studio-title" data-split>We build brands that <em>grow and stand out</em></h2>
      <div class="studio__grid">
        <figure class="studio__photo media" data-clip data-cursor="View"><img class="img-grade" data-speed="-8" src="https://assets.layerweb.com.br/saffra-design/studio-portrait.webp" alt="Portrait of a man in a dark leather jacket against warm autumn light" width="900" height="1100" loading="lazy"></figure>
        <div class="studio__right">
          <p class="studio__p" data-reveal>We bring together designers and strategists to create brands that feel right and perform even better. Every decision we make is focused on growth, clarity and long-term impact.</p>
          <div class="studio__team" data-reveal>
            <p>A team focused on real results, not just visuals: building brands that perform, grow and stand out in a competitive market.</p>
            <div class="avatars" aria-label="Some of the team"><img src="https://assets.layerweb.com.br/saffra-design/avatar-01.webp" alt="" width="400" height="400" loading="lazy"><img src="https://assets.layerweb.com.br/saffra-design/avatar-02.webp" alt="" width="400" height="400" loading="lazy"><img src="https://assets.layerweb.com.br/saffra-design/avatar-03.webp" alt="" width="400" height="400" loading="lazy"><b>12+</b></div>
          </div>
          <div class="studio__cards">
            <div class="card-b card-b--cream">
              <div class="card-b__num"><span data-count="350">350</span>+</div>
              <div class="card-b__label">Trusted partners</div>
              <div class="card-b__sub">Brands that chose us for long-term growth</div>
              <div class="partners" aria-label="A few of our partners"><span><i></i>Northfold</span><span><i></i>Lumina Co</span><span><i></i>Tessara</span><span><i></i>Oakline</span><span><i></i>Kiln &amp; Co</span><span><i></i>Vantage</span></div>
              <a class="btn-gold" href="#contact" data-magnetic>Get started <i><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></i></a>
            </div>
            <figure class="card-b card-b--photo" data-cursor="View" style="margin:0">
              <div class="media"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/studio-glow.webp" alt="Silhouette of a woman against a blaze of warm golden light" width="800" height="900" loading="lazy"></div>
              <div class="card-b__in"><div class="card-b__num"><span data-count="400">400</span>+</div><div class="card-b__label">Trusted by growing brands</div><div class="card-b__sub">From idea to full-scale brand execution</div></div>
            </figure>
          </div>
        </div>
      </div>

      <div class="toolkit">
        <span class="tag-pill">Services</span>
        <h2 class="toolkit__head" data-split>How we build <em>high-performing</em> brands</h2>
        <div class="tiles">
          <a class="tile tile--cream tile--a" href="#contact" data-cursor="Explore"><div class="tile__top"><h3 class="tile__title">Naming<br>&amp; voice</h3><span class="tile__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div><p class="tile__desc">Names, taglines and a tone of voice that sound like you and nobody else.</p></a>
          <a class="tile tile--gold tile--b" href="#contact" data-cursor="Explore"><div class="tile__top"><h3 class="tile__title">Packaging<br>design</h3><span class="tile__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div><p class="tile__desc">Boxes, labels and unboxing moments that make a product feel worth keeping.</p></a>
          <div class="tile-note" data-reveal><p>You don’t need everything, just what works.</p></div>
          <a class="tile tile--cream tile--c" href="#contact" data-cursor="Explore"><div class="tile__top"><h3 class="tile__title">Art<br>direction</h3><span class="tile__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div><p class="tile__desc">Photography, styling and film that keep every touchpoint in one look.</p></a>
          <div class="tile-text" data-reveal><p>We don’t sell a fixed list of services. We combine the right tools to solve your specific goals. From strategy to launch, every step is built around growth, clarity and real results.</p><a class="btn-line" href="#work">View our work <i><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></i></a></div>
          <a class="tile tile--cream tile--d" href="#contact" data-cursor="Explore"><div class="tile__top"><h3 class="tile__title">Web<br>design</h3><span class="tile__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div><p class="tile__desc">Fast, clear websites built to turn first visits into real conversations.</p></a>
          <a class="tile tile--gold tile--e" href="#contact" data-cursor="Explore"><div class="tile__top"><h3 class="tile__title">Motion &amp;<br>content</h3><span class="tile__arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M7 17 17 7M9 7h8v8"/></svg></span></div><p class="tile__desc">Motion design and content systems that capture attention and tell your story.</p></a>
        </div>
      </div>
    </div>
  </section>

  <!-- 6. Process -->
  <section class="process light" id="process" aria-labelledby="process-title">
    <div class="wrap process__grid">
      <div class="process__sticky">
        <p class="eyebrow" style="color:var(--gold-text);margin:0">How we work</p>
        <h2 class="process__title" id="process-title" data-split>Four steps.<br>No guesswork.</h2>
        <p class="process__lead" data-reveal>A clear rhythm keeps big ideas on time and on budget. You always know what happens next and why.</p>
        <div class="process__media media" data-clip><img class="img-grade" data-speed="-8" src="https://assets.layerweb.com.br/saffra-design/about-vase.webp" alt="" width="900" height="1120" loading="lazy" style="height:130%;margin-top:-15%"></div>
      </div>
      <div class="steps">
        <span class="steps__line" aria-hidden="true"><i></i></span>
        <ol>
        <li class="step"><div class="step__head"><span class="step__num">01</span><span class="step__time">Week 1–2</span></div><h3 class="step__title">Discover</h3><p class="step__text">Interviews, market maps and audits. We listen first, so every later choice stands on facts, not taste.</p><div class="step__tags"><span>Research</span><span>Workshops</span><span>Audit</span></div></li>
        <li class="step"><div class="step__head"><span class="step__num">02</span><span class="step__time">Week 3–4</span></div><h3 class="step__title">Define</h3><p class="step__text">Positioning, story and voice, written down in one clear platform your whole team can use.</p><div class="step__tags"><span>Positioning</span><span>Naming</span><span>Voice</span></div></li>
        <li class="step"><div class="step__head"><span class="step__num">03</span><span class="step__time">Week 5–9</span></div><h3 class="step__title">Design</h3><p class="step__text">Identity, product and campaign work, built as a system and tested with real people before launch.</p><div class="step__tags"><span>Identity</span><span>Web</span><span>Packaging</span></div></li>
        <li class="step"><div class="step__head"><span class="step__num">04</span><span class="step__time">Week 10+</span></div><h3 class="step__title">Deliver</h3><p class="step__text">Launch support, guidelines and a library of ready assets, so the brand keeps its shape after we leave.</p><div class="step__tags"><span>Launch</span><span>Guidelines</span><span>Support</span></div></li>
        </ol>
      </div>
    </div>
  </section>

  <!-- 6b. Showcase: curved carousel -->
  <section class="showcase" id="showcase" aria-labelledby="showcase-title">
    <div class="wrap">
      <div class="showcase__head">
        <h2 class="showcase__title" id="showcase-title" data-split>View our<br>latest works</h2>
        <p class="showcase__intro" data-reveal>We tried to shape a new way to show our latest work: warmer, more tactile and more different than ever.</p>
      </div>
      <div class="showcase__panel">
        <div class="showcase__top">
          <p>Every project blends strategy and craft into a look that is as unique as the brand behind it.</p>
          <p class="showcase__big" aria-hidden="true">Our works</p>
        </div>
        <div class="arc" tabindex="0" role="group" aria-roledescription="carousel" aria-label="Latest works. Use the arrow keys to browse.">
          <figure class="arc__card"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/arc-rings.webp" alt="Hand wearing stacked gold rings against a cream coat" width="700" height="940" loading="lazy"><span class="arc__chip">Campaign</span><figcaption class="arc__name">The Golden Hour series</figcaption></figure>
          <figure class="arc__card"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/arc-leaf.webp" alt="Profile of a woman with a leaf shadow across her face" width="700" height="940" loading="lazy"><span class="arc__chip">Editorial</span><figcaption class="arc__name">Soft light study</figcaption></figure>
          <figure class="arc__card"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/arc-sunset.webp" alt="Close-up profile of a woman with eyes closed in sunset light" width="700" height="940" loading="lazy"><span class="arc__chip">Identity</span><figcaption class="arc__name">The Amber Notes II</figcaption></figure>
          <figure class="arc__card"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/arc-pearl.webp" alt="Woman with a pearl earring looking down" width="700" height="940" loading="lazy"><span class="arc__chip">Packaging</span><figcaption class="arc__name">The Pearl edit</figcaption></figure>
          <figure class="arc__card"><img class="img-grade" src="https://assets.layerweb.com.br/saffra-design/arc-backlit.webp" alt="Backlit woman with curly hair glowing in golden light" width="700" height="940" loading="lazy"><span class="arc__chip">Film</span><figcaption class="arc__name">Dune portrait</figcaption></figure>
        </div>
        <div class="arc__nav">
          <button class="arc__btn" type="button" aria-label="Previous work" data-arc="-1"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M19 12H5M11 6l-6 6 6 6"/></svg></button>
          <button class="arc__btn" type="button" aria-label="Next work" data-arc="1"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M5 12h14M13 6l6 6-6 6"/></svg></button>
        </div>
      </div>
    </div>
  </section>

  <!-- 7. Manifesto -->
  <section class="manifesto" aria-label="Our belief">
    <div class="media"><img class="img-grade" data-speed="14" src="https://assets.layerweb.com.br/saffra-design/band-dunes.webp" alt="" width="2400" height="1300" loading="lazy" style="height:100%"></div>
    <div class="manifesto__shade"></div>
    <div class="wrap manifesto__inner">
      <p class="eyebrow" style="color:var(--gold-soft);margin:0" data-reveal>What we believe</p>
      <p class="manifesto__text fill" data-fill>Good design is quiet confidence.<br>It does not shout for attention.<br><em class="accent" style="color:var(--gold-soft)">It simply stays.</em></p>
    </div>
  </section>

  <!-- 8. Testimonials -->
  <section class="ll" id="voices" aria-roledescription="carousel" aria-label="Client testimonials">
    <h2 class="sr-only">What clients say</h2>
    <div class="ll-in">
      <div class="ll-nav">
        <button class="ll-btn" type="button" data-dir="-1" aria-label="Previous testimonial">
          <svg viewBox="0 0 28 22" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M27 11H1.5M11.5 1 1.5 11l10 10"/></svg>
        </button>
        <button class="ll-btn" type="button" data-dir="1" aria-label="Next testimonial">
          <svg viewBox="0 0 28 22" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M1 11h25.5M16.5 1l10 10-10 10"/></svg>
        </button>
      </div>
      <div class="ll-view" aria-live="polite">
        <div class="ll-track">
          <article class="ll-slide" aria-roledescription="slide" aria-label="1 of 4" style="--card:#e6d3a1;--title:#2a1d08;--body:#4f3d17;--track:rgba(42,29,8,.18);--fill:#2a1d08">
            <div class="ll-text">
              <h3 class="ll-title">Sales doubled in one season</h3>
              <div>
                <p class="ll-by">Clara Mendes · Founder, Oro Skin</p>
                <p class="ll-body">Saffra found the one sentence our brand was missing, then built a whole identity around it.</p>
                <div class="ll-bar"><span class="ll-fill"></span></div>
              </div>
            </div>
            <div class="ll-media"><img src="https://assets.layerweb.com.br/saffra-design/work-podium.webp" width="1600" height="1100" alt="Brushed gold podium on a soft cream backdrop with fern shadows" decoding="async"></div>
          </article>
          <article class="ll-slide" aria-roledescription="slide" aria-label="2 of 4" style="--card:#b98a3e;--title:#1e1408;--body:#2f2008;--track:rgba(30,20,8,.2);--fill:#1e1408">
            <div class="ll-text">
              <h3 class="ll-title">One voice in every touchpoint</h3>
              <div>
                <p class="ll-by">Iris Novak · CMO, Hollow &amp; Field</p>
                <p class="ll-body">They listen like strategists and design like artists. Our team finally speaks about the company in the same words.</p>
                <div class="ll-bar"><span class="ll-fill"></span></div>
              </div>
            </div>
            <div class="ll-media"><img src="https://assets.layerweb.com.br/saffra-design/work-scarf.webp" width="1600" height="1100" alt="Woman in a golden patterned scarf lit by dappled sunlight" decoding="async"></div>
          </article>
          <article class="ll-slide" aria-roledescription="slide" aria-label="3 of 4" style="--card:#6b5421;--title:#fbf3dc;--body:#e8d9ac;--track:rgba(255,255,255,.2);--fill:#fbf3dc">
            <div class="ll-text">
              <h3 class="ll-title">Enquiries tripled in two months</h3>
              <div>
                <p class="ll-by">Amara Cole · Director, Vela Studio</p>
                <p class="ll-body">Calm, fast and precise. The new site launched on schedule and the enquiries kept coming.</p>
                <div class="ll-bar"><span class="ll-fill"></span></div>
              </div>
            </div>
            <div class="ll-media"><img src="https://assets.layerweb.com.br/saffra-design/work-silk.webp" width="1600" height="1100" alt="Flowing golden fabric sculpture on a dark background" decoding="async"></div>
          </article>
          <article class="ll-slide" aria-roledescription="slide" aria-label="4 of 4" style="--card:#ece6da;--title:#15120c;--body:#5f5748;--track:rgba(21,18,12,.16);--fill:#15120c">
            <div class="ll-text">
              <h3 class="ll-title">A shelf that finally looks like us</h3>
              <div>
                <p class="ll-by">Noah Brandt · Founder, Terra Form</p>
                <p class="ll-body">From art direction to packaging, every detail now feels like one brand. Customers notice, and so does our team.</p>
                <div class="ll-bar"><span class="ll-fill"></span></div>
              </div>
            </div>
            <div class="ll-media"><img src="https://assets.layerweb.com.br/saffra-design/work-vessel.webp" width="1600" height="1100" alt="White ceramic vessel with a curved neck against black" decoding="async"></div>
          </article>
        </div>
      </div>
    </div>
  </section>

  <!-- 9. CTA -->
  <section class="cta" id="contact" aria-labelledby="cta-title">
    <div class="cta__frame">
      <div class="media"><img class="img-grade" data-speed="10" src="https://assets.layerweb.com.br/saffra-design/cta-silhouette.webp" alt="" width="2200" height="1300" loading="lazy" style="height:100%"></div>
      <div class="cta__shade"></div>
      <div class="cta__inner">
        <p class="eyebrow" style="color:var(--gold-soft);margin:0" data-reveal>Start a project</p>
        <h2 class="cta__title" id="cta-title" data-split>Have a brand<br>worth talking about?</h2>
        <div class="cta__row" data-reveal>
          <a class="big-btn" href="mailto:hello@saffra.studio" data-magnetic><span>Let’s<br>talk</span></a>
          <a class="cta__mail" href="mailto:hello@saffra.studio">hello@saffra.studio</a>
        </div>
      </div>
    </div>
  </section>
</main>

<!-- 10. Footer -->
<footer class="footer">
  <div class="wrap">
    <div class="footer__grid">
      <div class="footer__brand">
        <a class="logo" href="#top" aria-label="Saffra Design, back to top"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" aria-hidden="true"><path d="M12 21V6"/><path d="M12 21C12 15 7.5 11.5 6 5"/><path d="M12 21C12 15 16.5 11.5 18 5"/></svg><b>SAFFRA</b><small>DESIGN</small></a>
        <p class="footer__about">An independent creative strategy studio for brands that would rather be remembered than seen once.</p>
      </div>
      <div><h3>Studio</h3><ul><li><a href="#about">About</a></li><li><a href="#process">Process</a></li><li><a href="#work">Work</a></li></ul></div>
      <div><h3>Services</h3><ul><li><a href="#services">Strategy</a></li><li><a href="#services">Identity</a></li><li><a href="#services">Digital</a></li><li><a href="#services">Content</a></li></ul></div>
      <div><h3>Social</h3><ul><li><a href="https://www.instagram.com/" target="_blank" rel="noopener noreferrer">Instagram<span class="sr-only"> (opens in a new tab)</span></a></li><li><a href="https://www.linkedin.com/" target="_blank" rel="noopener noreferrer">LinkedIn<span class="sr-only"> (opens in a new tab)</span></a></li><li><a href="https://www.behance.net/" target="_blank" rel="noopener noreferrer">Behance<span class="sr-only"> (opens in a new tab)</span></a></li></ul></div>
      <div class="footer__news">
        <h3>Newsletter</h3>
        <form class="news" novalidate>
          <label class="sr-only" for="news-mail">Email address</label>
          <input id="news-mail" type="email" placeholder="Your email address" autocomplete="email" required>
          <button type="submit" aria-label="Subscribe"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M5 12h14M13 6l6 6-6 6"/></svg></button>
        </form>
        <p class="news-msg" role="status"></p>
      </div>
    </div>
    <div class="wordmark" aria-hidden="true"><span>S</span><span>A</span><span>F</span><span>F</span><span>R</span><span>A</span></div>
    <div class="footer__bottom">
      <span>© 2026 Saffra Design. All rights reserved.</span>
      <span>Lisbon · Remote worldwide</span>
      <a class="to-top" href="#top">Back to top <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M12 19V5M6 11l6-6 6 6"/></svg></a>
    </div>
  </div>
</footer>

<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lenis@1.1.13/dist/lenis.min.js"></script>
<script>
(function () {
  'use strict';
  var root = document.documentElement;
  var motion = root.classList.contains('has-motion') && window.gsap && window.ScrollTrigger;
  var finePointer = window.matchMedia('(hover:hover) and (pointer:fine)').matches;
  var $ = function (s, c) { return (c || document).querySelector(s); };
  var $$ = function (s, c) { return Array.prototype.slice.call((c || document).querySelectorAll(s)); };

  /* Broken image fallback: keep the box, hide the image */
  $$('img').forEach(function (img) {
    img.addEventListener('error', function () { img.classList.add('broken'); });
  });

  /* ---------- Word splitter: wraps each word in .w > .wi, keeps <br>, <em> and .keep elements ---------- */
  function split(el) {
    function walk(node) {
      Array.prototype.slice.call(node.childNodes).forEach(function (child) {
        if (child.nodeType === 3) {
          var parts = child.textContent.split(/(\s+)/);
          var frag = document.createDocumentFragment();
          parts.forEach(function (p) {
            if (!p) return;
            if (/^\s+$/.test(p)) { frag.appendChild(document.createTextNode(' ')); return; }
            var w = document.createElement('span'); w.className = 'w';
            var wi = document.createElement('span'); wi.className = 'wi'; wi.textContent = p;
            w.appendChild(wi); frag.appendChild(w);
          });
          node.replaceChild(frag, child);
        } else if (child.nodeType === 1 && child.tagName !== 'BR') {
          if (child.classList.contains('keep')) {
            var w = document.createElement('span'); w.className = 'w';
            var wi = document.createElement('span'); wi.className = 'wi';
            node.replaceChild(w, child); wi.appendChild(child); w.appendChild(wi);
          } else { walk(child); }
        }
      });
    }
    walk(el);
    return $$('.wi', el);
  }
  $$('[data-split],[data-fill],[data-hero-split]').forEach(split);

  /* ---------- Hero tabs (work without motion too) ---------- */
  var tabs = [
    ['Shape Your<br>Brand', 'Positioning and research that point every decision in one direction.'],
    ['Name It,<br>Own It', 'Names, logos and voice that feel like you from the very first glance.'],
    ['Built To<br>Be Used', 'Websites and products that move fast, read clearly and convert.'],
    ['Stories On<br>Repeat', 'Photo, film and social content that keeps your audience coming back.'],
    ['Details<br>That Last', 'Packaging, print and spaces crafted with the same care as the brand.']
  ];
  var pills = $$('.pill');
  var swaps = $$('.glass-card__swap');
  var current = 0, swapTimer;
  function setTab(i) {
    if (i === current) return;
    current = i;
    pills.forEach(function (p, k) { p.setAttribute('aria-selected', k === i ? 'true' : 'false'); p.tabIndex = k === i ? 0 : -1; });
    swaps.forEach(function (s) { s.classList.add('is-out'); });
    clearTimeout(swapTimer);
    swapTimer = setTimeout(function () {
      $('.glass-card__title').innerHTML = tabs[i][0];
      $('.glass-card__text').textContent = tabs[i][1];
      swaps.forEach(function (s) { s.classList.remove('is-out'); });
    }, 320);
  }
  pills.forEach(function (p, k) {
    p.tabIndex = k === 0 ? 0 : -1;
    p.addEventListener('click', function () { setTab(k); });
    p.addEventListener('keydown', function (e) {
      var d = e.key === 'ArrowRight' ? 1 : e.key === 'ArrowLeft' ? -1 : 0;
      if (!d) return;
      e.preventDefault();
      var n = (k + d + pills.length) % pills.length; pills[n].focus(); setTab(n);
    });
  });

  /* ---------- Newsletter ---------- */
  $('.news').addEventListener('submit', function (e) {
    e.preventDefault();
    var input = $('#news-mail'), msg = $('.news-msg');
    if (!input.value || !input.checkValidity()) { msg.textContent = 'Please enter a valid email address.'; input.focus(); return; }
    msg.textContent = 'Thank you. Our next letter is on its way.'; input.value = '';
  });

  /* ---------- Menu ---------- */
  var menu = $('#menu'), menuBtn = $('.menu-btn'), lenis = null;
  function openMenu(open) {
    menu.classList.toggle('is-open', open);
    menuBtn.setAttribute('aria-expanded', open ? 'true' : 'false');
    if (lenis) { open ? lenis.stop() : lenis.start(); }
    if (open) setTimeout(function () { $('.menu__close').focus(); }, 50); else menuBtn.focus();
  }
  menuBtn.addEventListener('click', function () { openMenu(true); });
  $('.menu__close').addEventListener('click', function () { openMenu(false); });
  document.addEventListener('keydown', function (e) { if (e.key === 'Escape' && menu.classList.contains('is-open')) openMenu(false); });

  /* ---------- Anchor links (smooth with Lenis when available) ---------- */
  $$('a[href^="#"]').forEach(function (a) {
    a.addEventListener('click', function (e) {
      var id = a.getAttribute('href');
      var target = id === '#top' ? document.body : $(id);
      if (!target) return;
      e.preventDefault();
      var wasOpen = menu.classList.contains('is-open');
      if (wasOpen) { menu.classList.remove('is-open'); menuBtn.setAttribute('aria-expanded', 'false'); if (lenis) lenis.start(); }
      if (lenis) lenis.scrollTo(id === '#top' ? 0 : target, { duration: 1.6, offset: 0 });
      else window.scrollTo({ top: id === '#top' ? 0 : target.getBoundingClientRect().top + window.scrollY, behavior: 'auto' });
    });
  });

  /* ---------- Header: glass pill after the hero; hides on scroll down only when motion is on ---------- */
  var header = $('.header'), lastY = 0;
  function onScroll() {
    var y = window.scrollY;
    header.classList.toggle('is-scrolled', y > window.innerHeight * 0.6);
    if (motion && !menu.classList.contains('is-open') && !header.contains(document.activeElement)) header.classList.toggle('is-hidden', y > lastY && y > 160);
    lastY = y;
  }
  window.addEventListener('scroll', onScroll, { passive: true });
  header.addEventListener('focusin', function () { header.classList.remove('is-hidden'); });
  onScroll();

  if (!motion) {
    root.classList.remove('has-motion');
    $$('[data-count]').forEach(function (el) { el.textContent = el.getAttribute('data-count'); });
    return;
  }

  /* ================= MOTION ================= */
  gsap.registerPlugin(ScrollTrigger);
  root.classList.add('motion-ready');

  /* Initial states (set before any tween records its start values; the loader covers the page meanwhile) */
  gsap.set('[data-split] .wi, .hero__title .wi, .hero__tag .wi', { yPercent: 110 });
  gsap.set('.wordmark span', { yPercent: 100 });
  gsap.set('[data-reveal]', { opacity: 0, y: 40 });
  gsap.set('[data-hero-fade], [data-hero-card]', { opacity: 0 });
  gsap.set('[data-clip]', { clipPath: 'inset(100% 0% 0% 0% round 14px)' });

  /* Smooth scroll */
  if (window.Lenis) {
    lenis = new Lenis({ lerp: 0.09, smoothWheel: true, wheelMultiplier: 1 });
    lenis.on('scroll', ScrollTrigger.update);
    gsap.ticker.add(function (t) { lenis.raf(t * 1000); });
    gsap.ticker.lagSmoothing(0);
    lenis.stop();
  }

  var mm = gsap.matchMedia();
  var isPhone = function () { return window.innerWidth < 768; };

  /* ---------- Loader ---------- */
  var count = { v: 0 };
  var loaderTl = gsap.timeline({ onComplete: intro });
  loaderTl
    .to('.loader__brand path', { strokeDashoffset: 0, duration: 0.7, stagger: 0.1, ease: 'power2.inOut' }, 0)
    .to('.loader__bar i', { scaleX: 1, duration: 0.9, ease: 'power2.inOut' }, 0)
    .to(count, { v: 100, duration: 0.9, ease: 'power2.inOut', onUpdate: function () { $('.loader__count').textContent = String(Math.round(count.v)).padStart(3, '0'); } }, 0)
    .to('.loader', { clipPath: 'inset(0 0 100% 0)', duration: 0.8, ease: 'expo.inOut' }, 1.0)
    .set('.loader', { display: 'none' });

  /* ---------- Hero entrance ---------- */
  function intro() {
    if (lenis) lenis.start();
    var tl = gsap.timeline({ defaults: { ease: 'expo.out' } });
    tl.fromTo('.hero__img', { scale: 1.18 }, { scale: 1, duration: 2.2, ease: 'power3.out' }, 0)
      .to('.hero__title .wi', { y: 0, yPercent: 0, duration: 1.3, stagger: 0.09 }, 0.15)
      .to('.hero__tag .wi', { yPercent: 0, y: 0, duration: 1.1, stagger: 0.05 }, 0.3)
      .fromTo('[data-hero-fade]', { opacity: 0, y: 24 }, { opacity: 1, y: 0, duration: 1.1, stagger: 0.1 }, 0.55)
      .fromTo('[data-hero-card]', { opacity: 0, y: 60 }, { opacity: 1, y: 0, duration: 1.2, stagger: 0.12 }, 0.6)
      .fromTo('.stat-card [data-count]', { textContent: 0 }, { textContent: 94, duration: 1.6, ease: 'power2.out', snap: { textContent: 1 } }, 0.7)
      .fromTo('.header__inner', { yPercent: -100, opacity: 0 }, { yPercent: 0, opacity: 1, duration: 1.1 }, 0.4);
  }

  /* active nav link */
  [['#top', '.hero'], ['#services', '.story'], ['#work', '.work'], ['#process', '.process']].forEach(function (pair) {
    var link = $('.nav a[href="' + pair[0] + '"]');
    ScrollTrigger.create({ trigger: pair[1], start: 'top 50%', end: 'bottom 50%', onToggle: function (s) { if (s.isActive) { $$('.nav a').forEach(function (l) { l.classList.remove('is-active'); }); link.classList.add('is-active'); } } });
  });

  /* ---------- Hero: pinned while About slides over it ---------- */
  ScrollTrigger.create({ trigger: '.hero', start: 'top top', end: '+=100%', pin: true, pinSpacing: false });
  gsap.timeline({ scrollTrigger: { trigger: '.about', start: 'top bottom', end: 'top top', scrub: true } })
    .to('.hero__frame', { scale: 0.9, borderRadius: 28, ease: 'none' }, 0)
    .to('.hero__dim', { opacity: 0.7, ease: 'none' }, 0)
    .to('.hero__img', { yPercent: 8, ease: 'none' }, 0)
    .to('.hero__content', { yPercent: -14, opacity: 0, ease: 'none' }, 0);

  /* ---------- Generic reveals ---------- */
  $$('[data-reveal]').forEach(function (el) {
    gsap.to(el, { opacity: 1, y: 0, duration: 1.2, ease: 'expo.out', scrollTrigger: { trigger: el, start: 'top 88%' } });
  });
  $$('[data-split]').forEach(function (el) {
    gsap.to($$('.wi', el), { yPercent: 0, duration: 1.2, ease: 'expo.out', stagger: 0.045, scrollTrigger: { trigger: el, start: 'top 85%' } });
  });

  /* Scroll-scrubbed word fill (About title, manifesto) */
  $$('[data-fill]').forEach(function (el) {
    gsap.fromTo($$('.wi', el), { opacity: 0.16 }, { opacity: 1, stagger: 0.1, ease: 'none', scrollTrigger: { trigger: el, start: 'top 82%', end: 'bottom 45%', scrub: true } });
  });

  /* Toggle knob in the About title slides across while scrolling */
  gsap.fromTo('.switch__knob', { x: 0 }, { x: '0.77em', ease: 'none', scrollTrigger: { trigger: '.about__title', start: 'top 75%', end: 'center 40%', scrub: true } });
  gsap.to('.asterisk', { rotation: 180, ease: 'none', scrollTrigger: { trigger: '.about', start: 'top bottom', end: 'bottom top', scrub: true } });

  /* Image clip reveals (staggered per group) */
  $$('.gallery, .process__sticky, .studio__grid').forEach(function (group) {
    var items = $$('[data-clip]', group);
    gsap.to(items, { clipPath: 'inset(0% 0% 0% 0% round 14px)', duration: 1.4, ease: 'expo.inOut', stagger: 0.12, scrollTrigger: { trigger: group, start: 'top 80%' } });
  });

  /* Parallax on images with data-speed (percent of own height) */
  $$('[data-speed]').forEach(function (img) {
    var s = parseFloat(img.getAttribute('data-speed'));
    gsap.fromTo(img, { yPercent: -s / 2 }, { yPercent: s / 2, ease: 'none', scrollTrigger: { trigger: img.parentElement, start: 'top bottom', end: 'bottom top', scrub: true } });
  });

  /* ---------- Services: rise in, staggered depth ---------- */
  mm.add('(min-width: 768px)', function () {
    gsap.fromTo('.service', { y: 120, opacity: 0 }, { y: 0, opacity: 1, duration: 1.4, ease: 'expo.out', stagger: 0.1, scrollTrigger: { trigger: '.services', start: 'top 85%' } });
    gsap.utils.toArray('.service').forEach(function (card, i) {
      gsap.to(card, { yPercent: [2, -5, 2, -5][i], ease: 'none', scrollTrigger: { trigger: '.services', start: 'top bottom', end: 'bottom top', scrub: true } });
    });
  });

  /* ---------- Work: pinned horizontal scroll (tablet + desktop) ---------- */
  mm.add('(min-width: 768px)', function () {
    var track = $('.work__track');
    var dist = function () { return Math.max(0, track.scrollWidth - window.innerWidth); };
    var countEl = $('.work__count'), total = $$('.project').length;
    var tween = gsap.to(track, {
      x: function () { return -dist(); }, ease: 'none',
      scrollTrigger: {
        trigger: '.work', start: 'top top', end: function () { return '+=' + dist(); }, pin: true, scrub: 0.8, invalidateOnRefresh: true,
        onUpdate: function (self) {
          gsap.set('.work__bar i', { scaleX: self.progress });
          countEl.textContent = String(Math.min(total, Math.floor(self.progress * (total - 0.001)) + 1)).padStart(2, '0');
        }
      }
    });
    $$('.project .media img').forEach(function (img) {
      gsap.fromTo(img, { xPercent: 6 }, { xPercent: -6, ease: 'none', scrollTrigger: { trigger: img.closest('.project'), containerAnimation: tween, start: 'left right', end: 'right left', scrub: true } });
    });
    /* keyboard focus: move the page scroll to the focused project and undo the browser's own sideways scroll */
    track.addEventListener('focusin', function (e) {
      var pr = e.target.closest('.project'); if (!pr) return;
      var idx = $$('.project').indexOf(pr), st = tween.scrollTrigger, y = st.start + (idx / (total - 1)) * (st.end - st.start);
      setTimeout(function () {
        $('.work').scrollLeft = 0; $('.work__pin').scrollLeft = 0;
        if (lenis) lenis.scrollTo(y, { immediate: true }); else window.scrollTo(0, y);
      }, 0);
    });
  });
  mm.add('(max-width: 767px)', function () {
    $$('.project').forEach(function (p) {
      gsap.fromTo(p, { y: 60, opacity: 0 }, { y: 0, opacity: 1, duration: 1.2, ease: 'expo.out', scrollTrigger: { trigger: p, start: 'top 88%' } });
    });
  });

  /* ---------- Counters ---------- */
  $$('.numbers [data-count], .studio [data-count]').forEach(function (el) {
    var end = parseInt(el.getAttribute('data-count'), 10);
    gsap.fromTo(el, { textContent: 0 }, { textContent: end, duration: 2, ease: 'power2.out', snap: { textContent: 1 }, scrollTrigger: { trigger: el, start: 'top 90%' } });
  });

  /* ---------- Marquee: infinite loop, speed and direction follow scroll ---------- */
  (function () {
    var track = $('.marquee__track'), group = $('.marquee__group');
    track.appendChild(group.cloneNode(true));
    var loop = gsap.to(track, { xPercent: -50, duration: 28, ease: 'none', repeat: -1 });
    var dir = 1;
    ScrollTrigger.create({
      start: 0, end: 'max',
      onUpdate: function (self) {
        dir = self.direction;
        var boost = Math.min(Math.abs(self.getVelocity()) / 300, 6);
        gsap.to(loop, { timeScale: dir * (1 + boost), duration: 0.3, overwrite: true, onComplete: function () { gsap.to(loop, { timeScale: dir, duration: 1.2 }); } });
      }
    });
  })();

  /* ---------- Process: line fill and active steps ---------- */
  gsap.to('.steps__line i', { scaleY: 1, ease: 'none', scrollTrigger: { trigger: '.steps', start: 'top 60%', end: 'bottom 60%', scrub: true } });
  $$('.step').forEach(function (step) {
    ScrollTrigger.create({ trigger: step, start: 'top 62%', end: 'bottom 62%', onToggle: function (s) { if (s.isActive || s.direction === 1) step.classList.add('is-active'); if (!s.isActive && s.direction === -1) step.classList.remove('is-active'); } });
  });

  /* ---------- CTA: frame opens from a rounded window to full bleed ---------- */
  gsap.fromTo('.cta__frame', { clipPath: 'inset(10% 12% 10% 12% round 28px)' }, { clipPath: 'inset(0% 0% 0% 0% round 0px)', ease: 'none', scrollTrigger: { trigger: '.cta', start: 'top 85%', end: 'top 10%', scrub: true } });

  /* ---------- Footer wordmark ---------- */
  gsap.to('.wordmark span', { yPercent: 0, duration: 1.3, ease: 'expo.out', stagger: 0.06, scrollTrigger: { trigger: '.wordmark', start: 'top 95%' } });

  /* ---------- Studio cards and tiles rise in ---------- */
  gsap.set('.card-b, .tile', { opacity: 0, y: 70 });
  ScrollTrigger.batch('.card-b, .tile', { start: 'top 90%', onEnter: function (els) { gsap.to(els, { opacity: 1, y: 0, duration: 1.2, ease: 'expo.out', stagger: 0.09, overwrite: true }); } });

  /* ---------- Showcase: curved carousel ---------- */
  (function () {
    var stage = $('.arc'), cards = $$('.arc__card', stage), n = cards.length, active = 2, built = false, drag = null, prevD = [];
    function wrap(d) { var h = Math.floor(n / 2); return ((d + h) % n + n) % n - h; }
    function pose(d, phone) {
      var ad = Math.abs(d), W = stage.clientWidth, step = phone ? W * 0.5 : W * 0.192;
      return { x: d * step * (ad === 2 ? 1.03 : 1), y: d === 0 ? 26 : ad === 1 ? -14 : -4, scale: d === 0 ? 1.2 : ad === 1 ? 0.95 : 0.88, rotationY: -d * 13, rotationZ: d * 1.6, opacity: phone && ad > 1 ? 0 : 1 };
    }
    function layout(first) {
      var phone = window.innerWidth < 768;
      cards.forEach(function (c, i) {
        var d = wrap(i - active), ad = Math.abs(d), p = pose(d, phone), jumped = !first && prevD[i] !== undefined && Math.abs(d - prevD[i]) > 2;
        prevD[i] = d;
        c.classList.toggle('is-center', d === 0);
        c.style.zIndex = 10 - ad;
        gsap.killTweensOf(c);
        if (jumped) {
          /* a card leaving one end re-enters at the other end: fade out, hop while invisible, fade in (never slide across the others) */
          gsap.timeline().to(c, { opacity: 0, duration: 0.16, ease: 'power2.out' }).set(c, { x: p.x, y: p.y, scale: p.scale, rotationY: p.rotationY, rotationZ: p.rotationZ }).to(c, { opacity: p.opacity, duration: 0.5, ease: 'power2.out' });
        } else {
          gsap.to(c, { x: p.x, y: p.y, scale: p.scale, rotationY: p.rotationY, rotationZ: p.rotationZ, opacity: p.opacity, duration: first ? 1.5 : 0.85, ease: 'expo.out', delay: first ? ad * 0.1 : 0 });
        }
      });
    }
    function go(dir) { active = ((active + dir) % n + n) % n; layout(false); }
    /* pointer: a drag of 50px or more turns the carousel; a short press on a side card brings it to the centre */
    stage.addEventListener('pointerdown', function (e) { drag = { x: e.clientX, moved: false }; });
    window.addEventListener('pointermove', function (e) { if (drag && Math.abs(e.clientX - drag.x) > 8) drag.moved = true; });
    window.addEventListener('pointerup', function (e) {
      if (!drag) return;
      var dx = e.clientX - drag.x; if (Math.abs(dx) > 50) go(dx < 0 ? 1 : -1);
      drag.done = true;
    });
    cards.forEach(function (c, i) {
      c.addEventListener('click', function () { if (drag && drag.moved) { drag = null; return; } var d = wrap(i - active); if (d) go(d); drag = null; });
    });
    $$('[data-arc]').forEach(function (b) { b.addEventListener('click', function () { go(parseInt(b.getAttribute('data-arc'), 10)); }); });
    stage.addEventListener('keydown', function (e) { if (e.key === 'ArrowRight') { e.preventDefault(); go(1); } if (e.key === 'ArrowLeft') { e.preventDefault(); go(-1); } });
    gsap.set(cards, { x: 0, y: 40, scale: 0.7, opacity: 0, rotationY: 0 });
    ScrollTrigger.create({ trigger: stage, start: 'top 80%', once: true, onEnter: function () { built = true; layout(true); } });
    window.addEventListener('resize', function () { if (built) layout(false); });
  })();

  /* ---------- Pointer effects (fine pointers only) ---------- */
  if (finePointer) {
    root.classList.add('has-cursor');
    var cur = $('.cursor'), arrow = $('.cursor__arrow'), label = $('.cursor__label');
    var cx = gsap.quickTo(cur, 'x', { duration: 0.16, ease: 'power3' }), cy = gsap.quickTo(cur, 'y', { duration: 0.16, ease: 'power3' });
    var tilt = gsap.quickTo(arrow, 'rotation', { duration: 0.5, ease: 'power3' }), tiltTimer, lastX = 0;
    document.addEventListener('mouseleave', function () { root.classList.remove('cursor-on'); });
    window.addEventListener('mousemove', function (e) {
      root.classList.add('cursor-on'); cx(e.clientX); cy(e.clientY);
      tilt(Math.max(-18, Math.min(18, (e.clientX - lastX) * 0.9))); lastX = e.clientX;
      clearTimeout(tiltTimer); tiltTimer = setTimeout(function () { tilt(0); }, 90);
    });
    window.addEventListener('mousedown', function () { cur.classList.add('is-down'); });
    window.addEventListener('mouseup', function () { cur.classList.remove('is-down'); });
    document.addEventListener('mouseover', function (e) {
      var t = e.target.closest('[data-cursor]');
      var l = e.target.closest('a, button');
      cur.classList.toggle('is-label', !!t);
      cur.classList.toggle('is-link', !t && !!l);
      cur.classList.toggle('is-text', !!e.target.closest('input'));
      if (t) label.textContent = t.getAttribute('data-cursor');
    });

    /* Magnetic buttons */
    $$('[data-magnetic]').forEach(function (el) {
      var mx = gsap.quickTo(el, 'x', { duration: 0.6, ease: 'power3.out' }), my = gsap.quickTo(el, 'y', { duration: 0.6, ease: 'power3.out' });
      el.addEventListener('mousemove', function (e) { var r = el.getBoundingClientRect(); mx((e.clientX - r.left - r.width / 2) * 0.35); my((e.clientY - r.top - r.height / 2) * 0.35); });
      el.addEventListener('mouseleave', function () { mx(0); my(0); });
    });

    /* 3D tilt on service cards */
    $$('[data-tilt]').forEach(function (card) {
      var rx = gsap.quickTo(card, 'rotationX', { duration: 0.6, ease: 'power3' }), ry = gsap.quickTo(card, 'rotationY', { duration: 0.6, ease: 'power3' });
      card.addEventListener('mousemove', function (e) { var r = card.getBoundingClientRect(); ry(((e.clientX - r.left) / r.width - 0.5) * 6); rx(-((e.clientY - r.top) / r.height - 0.5) * 6); });
      card.addEventListener('mouseleave', function () { rx(0); ry(0); });
    });

    /* Hero image follows the pointer slightly */
    var hx = gsap.quickTo('.hero__img img, .hero__water', 'x', { duration: 1.2, ease: 'power3' }), hy = gsap.quickTo('.hero__img img, .hero__water', 'y', { duration: 1.2, ease: 'power3' });
    $('.hero').addEventListener('mousemove', function (e) { hx((e.clientX / window.innerWidth - 0.5) * -24); hy((e.clientY / window.innerHeight - 0.5) * -16); });

    /* Hero liquid cursor: a ripple height-field bends the portrait only (WebGL2). Text, cards and buttons stay sharp. */
    (function () {
      var hero = $('.hero'), img = $('.hero__img img'), canvas = $('.hero__water');
      var gl = canvas.getContext('webgl2', { alpha: false, antialias: false, premultipliedAlpha: false });
      if (!gl) return;
      var imgReady = img.complete && img.naturalWidth ? Promise.resolve() : new Promise(function (r) { img.addEventListener('load', r); img.addEventListener('error', r); });

      var VS = '#version 300 es\nin vec2 p; out vec2 vUv;\nvoid main(){ vUv = vec2(p.x * .5 + .5, .5 - p.y * .5); gl_Position = vec4(p, 0., 1.); }';
      var FS = '#version 300 es\nprecision highp float;\nin vec2 vUv; out vec4 o;\nuniform sampler2D uImg, uWave;\nuniform vec2 uScale, uStart;\nuniform float uAmp;\n' +
        'void main(){\n  vec4 w = texture(uWave, vUv);\n  vec2 n = (w.rg - .5) * 2.;\n  float h = (w.b - .5) * 2.;\n  vec2 off = n * uAmp;\n  vec2 iu = uStart + (vUv + off) * uScale;\n  vec2 ca = off * uScale * .2;\n' +
        '  vec3 c = vec3(texture(uImg, iu + ca).r, texture(uImg, iu).g, texture(uImg, iu - ca).b);\n  vec3 N = normalize(vec3(-n * 1.4, 1.));\n  float spec = pow(max(dot(N, normalize(vec3(-.35, .45, 1.))), 0.), 60.);\n' +
        '  c *= 1. + h * .12;\n  c += spec * vec3(1., .78, .5) * .35;\n  o = vec4(c, 1.);\n}';
      function sh(type, src) { var s = gl.createShader(type); gl.shaderSource(s, src); gl.compileShader(s); return s; }
      var prog = gl.createProgram();
      gl.attachShader(prog, sh(gl.VERTEX_SHADER, VS)); gl.attachShader(prog, sh(gl.FRAGMENT_SHADER, FS));
      gl.bindAttribLocation(prog, 0, 'p'); gl.linkProgram(prog);
      if (!gl.getProgramParameter(prog, gl.LINK_STATUS)) return;
      gl.useProgram(prog);
      gl.bindBuffer(gl.ARRAY_BUFFER, gl.createBuffer());
      gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1, -1, 3, -1, -1, 3]), gl.STATIC_DRAW);
      gl.enableVertexAttribArray(0); gl.vertexAttribPointer(0, 2, gl.FLOAT, false, 0, 0);
      var uScale = gl.getUniformLocation(prog, 'uScale'), uStart = gl.getUniformLocation(prog, 'uStart'), uAmp = gl.getUniformLocation(prog, 'uAmp');
      gl.uniform1i(gl.getUniformLocation(prog, 'uImg'), 0); gl.uniform1i(gl.getUniformLocation(prog, 'uWave'), 1);
      function tex(unit) {
        var t = gl.createTexture(); gl.activeTexture(gl.TEXTURE0 + unit); gl.bindTexture(gl.TEXTURE_2D, t);
        gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE); gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
        gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR); gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR); return t;
      }
      tex(0); tex(1);
      var imgW = 0, imgH = 0;

      /* ripple simulation: CPU height field, one cell = 5 css px */
      var CELL = 5, DAMP = 0.98, gw = 0, gh = 0, cur, prev, pack, cw = 1, ch = 1, energy = 0, lastInput = 0, running = false, visible = true;
      function cover() {
        if (!imgW) return;
        var A = cw / ch, I = imgW / imgH, px = window.innerWidth < 768 ? 0.56 : 0.5, py = 0.3;
        if (A < I) { var s = A / I; gl.uniform2f(uScale, s, 1); gl.uniform2f(uStart, px * (1 - s), 0); }
        else { var s2 = I / A; gl.uniform2f(uScale, 1, s2); gl.uniform2f(uStart, 0, py * (1 - s2)); }
      }
      function draw() { if (imgW) { gl.uniform1f(uAmp, 0.018); gl.drawArrays(gl.TRIANGLES, 0, 3); } }
      function resize() {
        cw = Math.max(1, canvas.clientWidth); ch = Math.max(1, canvas.clientHeight);
        var dpr = Math.min(window.devicePixelRatio || 1, 1.75); dpr = Math.min(dpr, Math.sqrt(4.2e6 / (cw * ch)));
        canvas.width = Math.round(cw * dpr); canvas.height = Math.round(ch * dpr);
        gl.viewport(0, 0, canvas.width, canvas.height);
        gw = Math.min(360, Math.ceil(cw / CELL)); gh = Math.min(260, Math.ceil(ch / CELL));
        cur = new Float32Array(gw * gh); prev = new Float32Array(gw * gh); pack = new Uint8Array(gw * gh * 4).fill(128);
        gl.activeTexture(gl.TEXTURE1);
        gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gw, gh, 0, gl.RGBA, gl.UNSIGNED_BYTE, pack);
        cover(); draw();
      }
      function drop(x, y, radius, amp) {
        var gx = x / CELL, gy = y / CELL, r = radius, r2 = r * r;
        for (var j = Math.max(1, Math.floor(gy - r)); j <= Math.min(gh - 2, Math.ceil(gy + r)); j++)
          for (var i = Math.max(1, Math.floor(gx - r)); i <= Math.min(gw - 2, Math.ceil(gx + r)); i++) {
            var d2 = (i - gx) * (i - gx) + (j - gy) * (j - gy);
            if (d2 < r2) cur[j * gw + i] -= amp * 0.5 * (1 + Math.cos(Math.PI * Math.sqrt(d2) / r));
          }
      }
      function step() {
        var e = 0;
        for (var j = 1; j < gh - 1; j++) for (var i = 1; i < gw - 1; i++) {
          var k = j * gw + i, v = ((cur[k - 1] + cur[k + 1] + cur[k - gw] + cur[k + gw]) * 0.5 - prev[k]) * DAMP;
          prev[k] = v; var a = v < 0 ? -v : v; if (a > e) e = a;
        }
        var t = prev; prev = cur; cur = t; energy = e;
      }
      function upload() {
        for (var j = 0; j < gh; j++) for (var i = 0; i < gw; i++) {
          var k = j * gw + i, o = k * 4;
          var dx = cur[i < gw - 1 ? k + 1 : k] - cur[i > 0 ? k - 1 : k], dy = cur[j < gh - 1 ? k + gw : k] - cur[j > 0 ? k - gw : k];
          pack[o] = Math.max(0, Math.min(255, 128 + dx * 480)); pack[o + 1] = Math.max(0, Math.min(255, 128 + dy * 480)); pack[o + 2] = Math.max(0, Math.min(255, 128 + cur[k] * 110));
        }
        gl.activeTexture(gl.TEXTURE1);
        gl.texSubImage2D(gl.TEXTURE_2D, 0, 0, 0, gw, gh, gl.RGBA, gl.UNSIGNED_BYTE, pack);
      }
      /* pointer trail: a drop every ~1.5 cells along the travelled segment */
      var px0 = null, py0 = null, queue = [];
      function onMove(e) {
        if (!visible) return;
        var r = canvas.getBoundingClientRect(), x = (e.clientX - r.left) * (cw / r.width), y = (e.clientY - r.top) * (ch / r.height);
        if (x < 0 || y < 0 || x > cw || y > ch) { px0 = null; return; }
        queue.push([x, y]); lastInput = performance.now(); start();
      }
      function flush() {
        for (var q = 0; q < queue.length; q++) {
          var x = queue[q][0], y = queue[q][1];
          if (px0 === null) { px0 = x; py0 = y; continue; }
          var dx = x - px0, dy = y - py0, dist = Math.hypot(dx, dy), amp = Math.min(1, dist / 40) * 0.55 + 0.08, n = Math.max(1, Math.ceil(dist / (CELL * 1.5)));
          for (var s = 1; s <= n; s++) drop(px0 + dx * s / n, py0 + dy * s / n, 3.2, amp / Math.sqrt(n));
          px0 = x; py0 = y;
        }
        queue.length = 0;
      }
      var acc = 0, then = 0;
      function tick(now) {
        if (!running) return;
        var dt = Math.min(50, now - (then || now)); then = now; acc += dt; flush();
        var steps = 0; while (acc >= 1000 / 60 && steps < 3) { step(); acc -= 1000 / 60; steps++; }
        if (steps) { upload(); draw(); }
        if (energy < 0.002 && now - lastInput > 1200) { running = false; cur.fill(0); prev.fill(0); upload(); draw(); return; }
        requestAnimationFrame(tick);
      }
      function start() { if (!running && imgW) { running = true; then = 0; acc = 0; requestAnimationFrame(tick); } }

      function init() {
        imgW = img.naturalWidth; imgH = img.naturalHeight; if (!imgW) return;
        gl.activeTexture(gl.TEXTURE0);
        gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, img);
        gl.generateMipmap(gl.TEXTURE_2D);
        gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR_MIPMAP_LINEAR);
        if (gl.getError() !== gl.NO_ERROR) return;
        root.classList.add('gl'); resize();
        new ResizeObserver(resize).observe(canvas.parentElement);
        window.addEventListener('pointermove', onMove, { passive: true });
        window.addEventListener('pointerdown', onMove, { passive: true });
        new IntersectionObserver(function (en) { visible = en[0].isIntersecting; }).observe(hero);
        /* one soft ring on the portrait once the loader is gone */
        setTimeout(function () { if (!visible) return; drop(cw * (window.innerWidth < 1100 ? 0.55 : 0.5), ch * 0.42, 9, 0.8); lastInput = performance.now(); start(); }, 2300);
      }
      imgReady.then(function () { return img.decode ? img.decode().catch(function () {}) : 0; }).then(init);
    })();
  }

  /* Refresh after fonts and images change layout */
  if (document.fonts && document.fonts.ready) document.fonts.ready.then(function () { ScrollTrigger.refresh(); });
  window.addEventListener('load', function () { ScrollTrigger.refresh(); });
})();
</script>
<script>
/* Testimonials carousel: bar fills, then the whole track glides to the next slide (clone set on both sides, endless loop). */
(() => {
  const FILL = 2900, HOLD_FILL = 60, MOVE = 1450, HOLD_MOVE = 70;
  const reduce = matchMedia('(prefers-reduced-motion: reduce)');
  const bezier = (x1, y1, x2, y2) => {
    const cx = 3 * x1, bx = 3 * (x2 - x1) - cx, ax = 1 - cx - bx;
    const cy = 3 * y1, by = 3 * (y2 - y1) - cy, ay = 1 - cy - by;
    const X = t => ((ax * t + bx) * t + cx) * t, Y = t => ((ay * t + by) * t + cy) * t;
    return x => {
      if (x <= 0) return 0; if (x >= 1) return 1;
      let lo = 0, hi = 1;
      for (let i = 0; i < 24; i++) { const m = (lo + hi) / 2; if (X(m) < x) lo = m; else hi = m; }
      return Y((lo + hi) / 2);
    };
  };
  const easeFill = bezier(0.3, 0, 0.4, 1), easeMove = bezier(0.45, 0, 0, 0.95);
  const root = document.querySelector('.ll');
  const track = root.querySelector('.ll-track');
  const originals = [...track.children], N = originals.length;
  const cloneSet = () => originals.map(s => { const c = s.cloneNode(true); c.setAttribute('aria-hidden', 'true'); c.inert = true; return c; });
  track.prepend(...cloneSet()); track.append(...cloneSet());
  const slides = [...track.children];
  slides.forEach(s => s.querySelectorAll('img').forEach(img => img.addEventListener('error', () => img.classList.add('broken'))));
  let idx = N, x = 0, pitch = 0, phase = 'fill', t = 0, from = 0, to = 0, visible = false;
  const gapOf = () => parseFloat(getComputedStyle(track).columnGap) || 0;
  const measure = () => { pitch = slides[0].getBoundingClientRect().width + gapOf(); };
  const place = v => { x = v; track.style.transform = `translate3d(${v}px,0,0)`; };
  const setFill = p => { const f = slides[idx].querySelector('.ll-fill'); f.style.width = `calc(var(--bar) + (100% - var(--bar)) * ${p})`; };
  const setActive = (i, clear) => {
    slides.forEach((s, k) => s.classList.toggle('is-on', k === i));
    if (clear) slides.forEach((s, k) => { if (k !== i) s.querySelector('.ll-fill').style.width = ''; });
  };
  const jump = fn => { track.classList.add('ll-jump'); fn(); void track.offsetWidth; track.classList.remove('ll-jump'); };
  const go = dir => {
    from = x; idx += dir; setActive(idx); setFill(0); to = -idx * pitch; phase = 'move'; t = 0;
    if (reduce.matches) { place(to); settle(); }
  };
  const settle = () => {
    const n = idx >= 2 * N ? idx - N : idx < N ? idx + N : idx;
    jump(() => { idx = n; setActive(idx, true); setFill(0); place(-idx * pitch); });
    phase = 'hold-move'; t = 0;
  };
  let last = performance.now();
  const tick = now => {
    const dt = Math.min(now - last, 100); last = now;
    if (Math.abs(pitch - (slides[0].getBoundingClientRect().width + gapOf())) > 0.5) { measure(); if (phase !== 'move') place(-idx * pitch); }
    if (phase === 'move') {
      t += dt; const p = Math.min(t / MOVE, 1); place(from + (to - from) * easeMove(p)); if (p >= 1) settle();
    } else if (visible && !document.hidden) {
      t += dt;
      if (phase === 'hold-move' && t >= HOLD_MOVE) { phase = 'fill'; t = 0; }
      if (phase === 'fill') { const p = Math.min(t / FILL, 1); setFill(easeFill(p)); if (p >= 1) { phase = 'hold-fill'; t = 0; } }
      if (phase === 'hold-fill' && t >= HOLD_FILL) go(1);
    }
    requestAnimationFrame(tick);
  };
  root.querySelectorAll('.ll-btn').forEach(b => {
    let relTimer = 0;
    const release = () => {
      if (!b.classList.contains('is-press')) return;
      b.classList.remove('is-press'); b.classList.add('is-rel');
      clearTimeout(relTimer); relTimer = setTimeout(() => b.classList.remove('is-rel'), 650);
    };
    b.addEventListener('pointerdown', () => { clearTimeout(relTimer); b.classList.remove('is-rel'); b.classList.add('is-press'); });
    b.addEventListener('pointerup', release); b.addEventListener('pointerleave', release); b.addEventListener('pointercancel', release);
    b.addEventListener('click', () => go(+b.dataset.dir));
  });
  new IntersectionObserver(es => { visible = es[0].isIntersecting; }, { threshold: 0.3 }).observe(root);
  addEventListener('resize', () => { measure(); if (phase !== 'move') place(-idx * pitch); });
  new ResizeObserver(() => { measure(); if (phase !== 'move') place(-idx * pitch); }).observe(root);
  measure();
  jump(() => { setActive(idx); setFill(0); place(-idx * pitch); });
  requestAnimationFrame(tick);
})();
</script>
</body>
</html>
```

## 3. PIXEL CONTRACT — header (fragile)

- `.header` is fixed, full width, `transition: transform .5s var(--ease-out)`. `.header__inner` is 84px tall, max-width 1440, padding 0 36px.
- Glass state: `.header__bg` is an absolute pill (`inset: 10px calc(var(--gutter) - 12px)`, radius 999, `rgba(21,18,12,.72)`, `backdrop-filter: blur(16px)`) that fades in when `.is-scrolled` is set (scrollY > 0.6 × innerHeight). Hide state `.is-hidden` = `translateY(-110%)`, only with motion, scrolling down past 160px, and never while the header holds keyboard focus (`focusin` removes it).
- The entrance animates `.header__inner`, never `.header` itself.
- Buttons: menu and mail buttons are 44×44; `.header__cta` is a 44px gold pill "Start a project" (below 480px it reads "Start") with a 34px ink circle holding the arrow. DO NOT add a background to the header at scroll 0.

## 4. Brand — Saffra icon and wordmark

```html
<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" aria-hidden="true"><path d="M12 21V6"/><path d="M12 21C12 15 7.5 11.5 6 5"/><path d="M12 21C12 15 16.5 11.5 18 5"/></svg>
```

- 26×26px, gold `#c9a45c`; same paths at 34px in the loader, 15px in the stat card, 20px in the studio header; the favicon redraws the strokes for a 32×32 viewBox (`M16 27V8`, `M16 27C16 19 10 15 8 8`, `M16 27C16 19 22 15 24 8`, stroke 2.4).
- Wordmark `SAFFRA` 17px / 650 / +.16em; `DESIGN` 11px / 400 / +.2em (hidden below 768px). DO NOT redraw, fill or outline the icon.

## 5. PIXEL CONTRACT — hero and the liquid portrait (fragile)

- Photo `hero-sunset.webp` (2400×1350) in `.hero__img` (`inset:-6% 0 0 0; height:112%`), `object-fit: cover; object-position: 50% 30%` (56% 30% below 768px), class `img-grade` (`saturate(.86) sepia(.08)`).
- **Liquid canvas:** `canvas.hero__water` (also `img-grade`) sits inside `.hero__img` after the `<img>`. When WebGL2 and the texture work, `<html>` gets the class `gl`, the canvas is shown and the `<img>` becomes `visibility:hidden`. Otherwise the `<img>` stays and the page is complete.
- Shader framing = the same cover crop: aspect A = canvas width/height, I = 2400/1350; if A < I the scale is (A/I, 1) and the start (px·(1−A/I), 0), else (1, I/A) and (0, .3·(1−I/A)), with px = .5 (.56 below 768px).
- Simulation: CPU height field, one cell = 5 CSS px, grid capped at 360×260, step `v = ((L+R+U+D)·.5 − prev)·.98`, fixed 60 steps/s (max 3 per frame, dt capped at 50ms). Canvas DPR capped at 1.75 and at 4.2 million pixels.
- Texture packing (RGBA8, LINEAR, CLAMP): R = 128 + dx·480, G = 128 + dy·480, B = 128 + h·110. Fragment: offset = n·0.018, chromatic split `off·uScale·.2`, brightness `1 + h·.12`, warm specular `pow(max(dot(normalize(vec3(−n·1.4,1)),normalize(vec3(−.35,.45,1))),0),60)·vec3(1,.78,.5)·.35`.
- Pointer: `pointermove` and `pointerdown` on `window`; drops every 7.5px along the travelled segment, radius 3.2 cells, amplitude `(min(1,dist/40)·.55+.08)/√n`. One intro ring (radius 9, amplitude .8) at (50% x, 42% y) 2,300ms after the texture is ready. The loop stops when max |height| < .002 and 1,200ms passed without input; it restarts on input and ignores input while the hero is off screen. The whole block only runs on fine pointers with motion on. The photo also follows the pointer by ±12px / ±8px (applied to the img and the canvas).
- Grid: two columns `minmax(0,1fr)` (NOT plain `1fr`), rows `1fr auto`, padding 120px 36px 36px. Title `clamp(40px,5vw,76px)`, right-aligned, three lines. Aside = paragraph above a `.btn-line` "Start a project".
- Stat card 178×176 (number counts 0→94 in 1.6s); glass card 210×176 (`backdrop-filter: blur(14px)`); five pill tabs (`role="tablist"`, 40px tall, 44 on touch) swap the glass card text after a 320ms fade.
- DO NOT put the cards inside the photo frame, round the hero, distort any text, use an SVG or CSS filter for the liquid, or animate ripples on their own (only the single intro ring).

## 6. PIXEL CONTRACT — hero → about overlap (fragile)

- `ScrollTrigger.create({ trigger: '.hero', start: 'top top', end: '+=100%', pin: true, pinSpacing: false })` and one scrubbed timeline from `.about` `top bottom` to `top top`: `.hero__frame` scale 1 → .9 and radius 0 → 28, `.hero__dim` opacity 0 → .7, `.hero__img` yPercent 0 → 8, `.hero__content` yPercent 0 → −14 and opacity 1 → 0. `.about` has `border-radius:32px 32px 0 0; margin-top:-32px; z-index:3`. DO NOT set `pinSpacing: true`.

## 7. Word splitting, fills and reveals

- `split()` wraps every word in `span.w > span.wi` (keeps `<br>`, `<em>`, and wraps `.keep` elements as one word). `[data-split]` titles: `.wi` yPercent 110 → 0, 1.2s `expo.out`, stagger .045. `[data-fill]` titles: opacity .16 → 1 per word, scrubbed. `[data-reveal]`: opacity 0, y 40 → visible, 1.2s. `[data-clip]` images: `inset(100% 0% 0% 0% round 14px)` → `inset(0% 0% 0% 0% round 14px)`, 1.4s `expo.inOut`, stagger .12 per group (`.gallery`, `.process__sticky`, `.studio__grid`). `[data-speed]` images move yPercent ∓speed/2 while crossing the viewport. Studio cards and toolkit tiles rise from y 70 with `ScrollTrigger.batch` (stagger .09). All initial hidden states are set with `gsap.set` before any tween; only `[data-reveal]` hides via CSS under `.has-motion`.

## 8. Services, work, marquee, studio, process

- Services: 4 columns, card 2 sits 44px higher; hover zoom 1.07, description opens with `grid-template-rows 0fr → 1fr`, 3D tilt up to ±3° (`quickTo`).
- Work: `.work` pinned from `top top` for `track.scrollWidth − innerWidth` px (1,592 at 1440), `scrub .8`; cards `clamp(280px,40vw,600px)`; below 768px they stack with no pin. **Keyboard:** a `focusin` on a project resets the browser's own sideways scroll and moves the page scroll to that project's position (otherwise the section shifts sideways).
- Marquee: five words cloned once, `xPercent 0 → −50`, 28s linear loop; `timeScale = direction × (1 + min(|velocity|/300, 6))`. Dim words are `rgba(242,237,227,.3)`. No outlined text.
- Studio: tag pill, title with a gold `em`, photo card (aspect 4/5, parallax), paragraph, team row with three avatars and a "12+" disc, cream card (350+, partner names with small outline glyphs, gold `Get started` button), photo card (400+). Toolkit grid areas `"a b t c" "p p d e"`, 14px gaps; tiles are cream (`--cream`) or mustard (`--gold`), the hover colour swap only runs under `(hover:hover) and (pointer:fine)`; tablet 2 columns, phone 1.
- Process: sticky left column, gold line `scaleY` scrubbed, a step turns `.is-active` when its top passes 62% of the viewport.

## 9. Showcase carousel (curved)

- Panel: `--ink`, radius 22, `box-shadow: 0 0 0 10px rgba(242,237,227,.3)`, on a tan multi-radial background. Title block "View our latest works", intro text, small paragraph, giant `p.showcase__big` "OUR WORKS" (`aria-hidden`, must NOT inherit the small paragraph style).
- `.arc` is `position:relative; height:clamp(380px,35vw,480px); perspective:1500px`. Cards are `position:absolute; left/top 50%` with negative margins (`width·−.5` left, `width·−.6915` top; width `clamp(150px,15vw,224px)`, 48vw on phones). **The rule that turns cards into a flat scrolling row (`position:relative`) must be scoped to `html:not(.motion-ready) .arc__card`.**
- Active index starts at 2 and wraps. Pose per offset d: x = d·0.192·stageWidth (×1.03 at |d|=2; phone 0.5·W and only |d|≤1 visible), y = 26 / −14 / −4, scale 1.2 / .95 / .88, rotationY −13d°, rotationZ 1.6d°. Moves use `expo.out` 0.85s (first entrance 1.5s, stagger .1 from the centre). **A card that wraps from one end to the other fades out (.16s), hops while invisible and fades in (.5s); it never slides across the others.**
- Controls: two 56×44 arrow buttons, ArrowLeft/ArrowRight on the focused stage (visible gold focus ring), a ≥50px drag, and a click on a side card (ignored when the pointer moved more than 8px).

## 10. Manifesto, testimonials, CTA, footer, cursor, menu

- Manifesto: `height:120vh; min-height:700px`; parallax photo; radial shade; three lines, the last in `--gold-soft`.
- **Testimonials (`section.ll`, container query unit):** plain `--ink` background (no dot pattern). Desktop ≥ 1100px uses `--u`: slide 1290u, text card 557u, gap 12u, image 721u, height 550u, radius 60u, title 47.5u / 600, body 23u / 29.5u, author line `max(15u,12px)` uppercase, bar 4u. Tablet (768–1099) fixed: slide `100cqw − 80px`, text 46%, height 440px, radius 36px. Phone (< 768): column slides, radius 28px, image height 0.76 × width.
- Inactive card `#211b11`, title `#6b5f47`, body `#5a4f39`, bar track `#332b1c`, image opacity .2; all transition 1450ms `ease`. Active slides carry per-slide variables: 1 Oro Skin `--card #e6d3a1 / --title #2a1d08 / --body #4f3d17`; 2 Hollow & Field `#b98a3e / #1e1408 / #2f2008`; 3 Vela Studio `#6b5421 / #fbf3dc / #e8d9ac`; 4 Terra Form `#ece6da / #15120c / #5f5748`.
- Cycle (one `requestAnimationFrame` loop): bar fills 2,900ms (`cubic-bezier(.3,0,.4,1)`), hold 60ms, glide 1,450ms (`cubic-bezier(.45,0,0,.95)`, `translate3d` only), hold 70ms; 12 slides in the track (clones before and after, `aria-hidden` + `inert`), silent jump after each glide; only advances while ≥30% visible and the tab is visible. Arrow buttons (67px at 1440) glide one slide at once, with press scale .8 and a spring release. Reduced motion: no cross-fade, instant jump.
- CTA: `.cta__frame` clip-path `inset(10% 12% round 28px)` → full bleed, scrubbed. Button 150×150, magnetic (follows the pointer by 35% with `power3.out`, never elastic).
- Footer: five-column grid `5fr 2fr 2fr 2fr 4fr`; social links open generic network home pages in a new tab (`rel="noopener noreferrer"`, "(opens in a new tab)" for screen readers) — replace with real profiles. Newsletter validates with `checkValidity()` and sends nothing. Wordmark letters rise in (stagger .06).
- **Cursor** (fine pointers only): a 19×21px gold rounded arrow (`M4 3 L22 16.5 L12.5 17.8 L6.5 25.5 Z`, stroke-linejoin round, soft drop shadow, **no outline**) following with .16s `power3`; it tilts ±18° with horizontal speed, shrinks to .82 on press, grows to 1.15 over links (soft gold) and shows a 22px gold label pill ("View" / "Explore") over `[data-cursor]`. Hidden until the first mouse move; the system cursor is hidden except in the email field.
- Menu: overlay clip-path `inset(0 0 100% 0)` → `inset(0 0 0% 0)` .8s; links rise with .06s steps; Escape closes; Lenis stops while open.

## 11. Assets (exact URLs, all respond 200 `image/webp`, CORS `*`)

Base: `https://assets.layerweb.com.br/saffra-design`

| File | Size (px) | Used in |
|---|---|---|
| hero-sunset.webp | 2400×1350 | Hero photo (img + WebGL texture) |
| about-plant-warm.webp, about-vase.webp, about-portrait.webp | 900×1120 | Gallery; vase also in process; portrait also the toggle knob |
| service-strategy / -identity / -digital / -content.webp | 800×1100 | Service cards |
| work-arch / -scarf / -podium / -silk / -vessel.webp | 1600×1100 | Work cards; podium, scarf, silk, vessel also testimonial images |
| studio-portrait.webp | 900×1100 | Studio photo |
| studio-glow.webp | 800×900 | Studio "400+" card |
| arc-rings / -leaf / -sunset / -pearl / -backlit.webp | 700×940 | Showcase cards |
| band-dunes.webp | 2400×1300 | Manifesto |
| cta-silhouette.webp | 2200×1300 | CTA |
| avatar-01 / -02 / -03.webp | 400×400 | Studio team row |

Each file is `{Base}/{name}`. Every `<img>` has `width`/`height`, so a failed image only adds `.broken` and causes no layout shift. All images except the hero are `loading="lazy"` (testimonial images load eagerly so neighbours are ready).

## 12. Responsive

- **≥ 1101px:** as measured. **≤ 1100px:** nav hidden, services 2×2, toolkit tiles 2 columns, footer 3 columns. **≤ 1023px:** hero becomes one column, studio and process one column, showcase top stacks. **≤ 767px:** header 70px, hero min-height 680 with a stronger vertical shade, services and gallery become swipeable rows, work stacks (no pin), tiles one column, arc cards 48vw (only the centre and its neighbours visible), testimonial slides become columns.
- `html` never scrolls sideways at 1440, 834 and 390. Touch targets are ≥ 44px on touch widths.

## 13. Entrance timeline (after the loader)

Loader: strokes draw 0.7s, bar and counter 0.9s, wipe at 1.0s for 0.8s. Then Lenis starts: `.hero__img` scale 1.18 → 1 (2.2s), title words (1.3s, stagger .09), tag words, header `.header__inner` y −100% → 0, `[data-hero-fade]`, `[data-hero-card]`, stat number 0 → 94.

## 14. Accessibility

- `prefers-reduced-motion: reduce`: no loader, no Lenis, no water, no GSAP, everything visible; work row and showcase are native scrollers with snap; the testimonial carousel switches slides instantly.
- Skip link; `:focus-visible` = 2px gold outline; pills use `role="tab"`; the glass card is `aria-live="polite"`; the menu is a dialog; the testimonial section is `aria-roledescription="carousel"` with `aria-live="polite"`; decorative images have `alt=""`.

## 15. Final self-check (run before answering)

1. `index.html` is byte-identical to §2; no other files; document height 13,982px with motion (12,143px with reduced motion); no horizontal scroll.
2. Console: no errors, no 404; 25 images load from the CDN; `<html>` ends with the classes `js has-motion motion-ready has-cursor gl` in a WebGL2 browser with a mouse.
3. Moving the mouse over the hero bends only the photo and the ripples settle within about 2s; text and cards stay sharp; the cursor is a small gold arrow with no dark border.
4. The showcase shows five curved cards in a row (centre larger, 1.2×) — not stacked — with the "OUR WORKS" title at ≈130px; the arrows rotate the cards and a wrapping card fades instead of sliding across.
5. The testimonial bar fills for ≈2.9s, then the track glides; after four cycles slide 1 is active again; clicking the arrows glides at once.
6. Tab order reaches every control; the showcase stage and the testimonial arrows show a gold focus ring; tabbing through the work cards never shifts the section sideways.
7. No green anywhere; accents are only `#c9a45c`, `#7a5c1f`/`#a3803a`, `#e6d3a1`. No outlined text, no extra libraries, no hotlinked images from other domains.
