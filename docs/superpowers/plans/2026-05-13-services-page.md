# Services Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Move the services content into a dedicated `services.html` page, add two new services (AI Product Content, Web Design & SEO), and replace the homepage services section with a compact teaser.

**Architecture:** `services.html` is built by copying `index.html` (the live homepage) and stripping it down to nav + menu + services + footer, keeping the existing dark "services" aesthetic unchanged (Approach 1 from the spec). The homepage's full `#services` block is replaced with a names-only teaser whose rows deep-link into `services.html`. All new copy is bilingual (EN/中文) using the site's existing `data-lang` span pattern.

**Tech Stack:** Static HTML/CSS/JS, no build step, no framework. Hosted on Netlify (static from repo root). **There is no automated test suite** — verification is done by running a local static server and checking in a browser, plus `grep` assertions for mechanical link changes.

**Spec:** `docs/superpowers/specs/2026-05-13-services-page-design.md`

**Conventions used throughout:**
- The site translates text two ways. The `#services` block and new content use the **span pattern**: `<span data-lang="en">English</span><span data-lang="zh" style="display:none">中文</span>`. The language-toggle JS shows/hides these by `display`.
- Service rows alternate layout: odd numbers (01, 03, 05) are `class="svc-row reveal"`, even numbers (02, 04, 06) are `class="svc-row flip reveal"`.
- To run the site locally for verification: from the repo root run `npx serve .`, then open the printed URL (e.g. `http://localhost:3000`). Use `Ctrl+C` to stop it.

---

## Task 1: Create services.html as a copy of index.html

**Files:**
- Create: `services.html` (copy of `index.html`)

- [ ] **Step 1: Copy the file**

From the repo root (`C:\Users\Joey Reed\reed-creative-agency`):

```bash
cp index.html services.html
```

- [ ] **Step 2: Verify the copy is byte-identical**

```bash
diff index.html services.html
```

Expected: no output (files identical).

- [ ] **Step 3: Commit**

```bash
git add services.html
git commit -m "Create services.html from index.html copy"
```

---

## Task 2: Strip unused body sections from services.html

`services.html` only needs: custom cursor, nav, menu overlay, the `#services` block, and the footer. Remove the Hero, Founder Strip, China Market, Our Clients, and Contact sections.

At this point `services.html` is still line-for-line identical to `index.html`, so the line numbers below are exact. **Delete the higher range first** so the lower line numbers stay valid.

**Files:**
- Modify: `services.html`

- [ ] **Step 1: Delete the China Market + Our Clients + Contact sections**

Delete lines **1313–1377** inclusive. Line 1313 is `    <!-- China Market Section -->` and line 1377 is the blank line immediately before `    <!-- Footer -->`. After deletion, `    <!-- Services -->`'s closing `    </div>` should be followed directly by `    <!-- Footer -->`.

- [ ] **Step 2: Delete the Hero + Founder Strip sections**

Delete lines **1173–1204** inclusive. Line 1173 is `    <!-- Hero -->` and line 1204 is the blank line immediately before `    <!-- Services -->`. After deletion, the `</div>` closing `<!-- Fullscreen Menu Overlay -->` should be followed directly by `    <!-- Services -->`.

- [ ] **Step 3: Confirm the body now contains only the expected blocks**

```bash
grep -nE '<!-- (Custom Cursor|Nav|Fullscreen Menu Overlay|Services|Footer) -->' services.html
```

Expected: exactly 5 lines — Custom Cursor, Nav, Fullscreen Menu Overlay, Services, Footer (in that order).

```bash
grep -cE 'id="hero"|id="founder-strip"|id="china"|class="our-clients"|class="contact"' services.html
```

Expected: `0`

- [ ] **Step 4: Verify the page still renders**

Run `npx serve .`, open `http://localhost:3000/services.html`. Expected: page shows the dark "What we do." header and the 4 existing service rows, with nav at top and footer at bottom. The JS console will show errors (the script still references removed elements) — that is fixed in Task 7. Stop the server with `Ctrl+C`.

- [ ] **Step 5: Commit**

```bash
git add services.html
git commit -m "Strip unused sections from services.html"
```

---

## Task 3: Update the services.html `<head>`

**Files:**
- Modify: `services.html`

- [ ] **Step 1: Update the title**

Replace:

```html
    <title>Reed Creative Agency</title>
```

with:

```html
    <title>Services — Reed Creative Agency</title>
```

- [ ] **Step 2: Update the meta description**

Replace:

```html
    <meta name="description" content="Reed Creative Agency — a creator-led agency built for brand partnerships, campaigns, and cultural moments. US + China dual market. 1.5B+ network reach.">
```

with:

```html
    <meta name="description" content="What we do — Mega KOL & celebrity campaigns, creator management, brand activations, AI product content, and web design & SEO.">
```

- [ ] **Step 3: Update the Open Graph / Twitter tags**

Replace this block:

```html
    <meta property="og:title" content="Reed Creative Agency">
    <meta property="og:description" content="A creator-led agency built for brand partnerships, campaigns, and cultural moments.">
    <meta property="og:type" content="website">
    <meta property="og:url" content="https://www.reedcreativeagency.com">
    <meta property="og:image" content="https://www.reedcreativeagency.com/og-image.png">
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Reed Creative Agency">
    <meta name="twitter:description" content="A creator-led agency built for brand partnerships, campaigns, and cultural moments.">
    <meta name="twitter:image" content="https://www.reedcreativeagency.com/og-image.png">
```

with:

```html
    <meta property="og:title" content="Services — Reed Creative Agency">
    <meta property="og:description" content="Mega KOL & celebrity campaigns, creator management, brand activations, AI product content, and web design & SEO.">
    <meta property="og:type" content="website">
    <meta property="og:url" content="https://www.reedcreativeagency.com/services.html">
    <meta property="og:image" content="https://www.reedcreativeagency.com/og-image.png">
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Services — Reed Creative Agency">
    <meta name="twitter:description" content="Mega KOL & celebrity campaigns, creator management, brand activations, AI product content, and web design & SEO.">
    <meta name="twitter:image" content="https://www.reedcreativeagency.com/og-image.png">
```

- [ ] **Step 4: Verify**

```bash
grep -nE '<title>|og:url' services.html
```

Expected: title is `Services — Reed Creative Agency`, og:url ends in `/services.html`.

- [ ] **Step 5: Commit**

```bash
git add services.html
git commit -m "Update services.html head metadata"
```

---

## Task 4: Rewrite the services.html nav menu links

The fullscreen menu overlay was copied from the homepage, so its links point to homepage anchors (`#services`, `#china`, `#our-clients`) that do not exist on `services.html`. Repoint them.

**Files:**
- Modify: `services.html`

- [ ] **Step 1: Replace the menu links**

Replace this block:

```html
        <ul class="menu-links">
            <li><a href="#services" class="menu-link" data-en="Services" data-zh="服务">Services</a></li>
            <li><a href="about.html" class="menu-link" data-en="About" data-zh="关于我们">About</a></li>
            <li><a href="#china" class="menu-link" data-en="China Market" data-zh="中国市场">China Market</a></li>
            <li><a href="#our-clients" class="menu-link" data-en="Our Clients" data-zh="我们的客户">Our Clients</a></li>
            <li><a href="contact.html" class="menu-link" data-en="Contact" data-zh="联系我们">Contact</a></li>
        </ul>
```

with:

```html
        <ul class="menu-links">
            <li><a href="services.html" class="menu-link" data-en="Services" data-zh="服务">Services</a></li>
            <li><a href="about.html" class="menu-link" data-en="About" data-zh="关于我们">About</a></li>
            <li><a href="index.html#china" class="menu-link" data-en="China Market" data-zh="中国市场">China Market</a></li>
            <li><a href="index.html#our-clients" class="menu-link" data-en="Our Clients" data-zh="我们的客户">Our Clients</a></li>
            <li><a href="contact.html" class="menu-link" data-en="Contact" data-zh="联系我们">Contact</a></li>
        </ul>
```

- [ ] **Step 2: Make the logo link home**

The homepage logo is a non-clickable `<div>`. On `services.html` it should link back to the homepage. Replace:

```html
        <div class="nav-logo">Reed Creative Agency</div>
```

with:

```html
        <a href="index.html" class="nav-logo">Reed Creative Agency</a>
```

- [ ] **Step 3: Verify**

```bash
grep -nE 'href="#(services|china|our-clients)"' services.html
```

Expected: no output (no leftover homepage-only anchors).

- [ ] **Step 4: Commit**

```bash
git add services.html
git commit -m "Repoint services.html nav links for standalone page"
```

---

## Task 5: Add anchor IDs to the four existing service rows

The homepage teaser (Task 8) deep-links to `services.html#service-01` … `#service-06`. Add `id` attributes to the four existing rows.

**Files:**
- Modify: `services.html`

- [ ] **Step 1: Add id to row 01**

Replace:

```html
      <!-- 01 -->
      <div class="svc-row reveal">
```

with:

```html
      <!-- 01 -->
      <div class="svc-row reveal" id="service-01">
```

- [ ] **Step 2: Add id to row 02**

Replace:

```html
      <!-- 02 -->
      <div class="svc-row flip reveal">
```

with:

```html
      <!-- 02 -->
      <div class="svc-row flip reveal" id="service-02">
```

- [ ] **Step 3: Add id to row 03**

Replace:

```html
      <!-- 03 -->
      <div class="svc-row reveal">
```

with:

```html
      <!-- 03 -->
      <div class="svc-row reveal" id="service-03">
```

- [ ] **Step 4: Add id to row 04**

Replace:

```html
      <!-- 04 -->
      <div class="svc-row flip reveal">
```

with:

```html
      <!-- 04 -->
      <div class="svc-row flip reveal" id="service-04">
```

- [ ] **Step 5: Verify**

```bash
grep -cE 'id="service-0[1-4]"' services.html
```

Expected: `4`

- [ ] **Step 6: Commit**

```bash
git add services.html
git commit -m "Add anchor IDs to existing service rows"
```

---

## Task 6: Add the two new service rows to services.html

Add rows **05 (AI Product Content)** and **06 (Web Design & SEO)** after row 04, before the `</div>` that closes `<div id="services">`. They use the dark placeholder panel (`.svc-photo-fill` with an SVG icon + label) — the existing `.svc-photo-fill` CSS already styles this. An HTML comment in each row documents how to swap in a real image later.

**Files:**
- Modify: `services.html`

- [ ] **Step 1: Insert the two new rows**

Find the end of row 04 followed by the closing of the services block:

```html
          <a href="contact.html" class="svc-cta-link"><span data-lang="en">Sponsor an Event</span><span data-lang="zh" style="display:none">赞助活动</span></a>
        </div>
      </div>

    </div>
```

Replace it with (row 04's CTA line and `</div></div>` are unchanged; the two new rows are inserted before the final `    </div>`):

```html
          <a href="contact.html" class="svc-cta-link"><span data-lang="en">Sponsor an Event</span><span data-lang="zh" style="display:none">赞助活动</span></a>
        </div>
      </div>

      <!-- 05 -->
      <div class="svc-row reveal" id="service-05">
        <div class="svc-photo">
          <!-- To use a real image, replace the .svc-photo-fill div below with:
               <div class="svc-photo-fill"><img src="pictures/ai product content.JPG" alt="AI Product Content" style="width:100%;height:100%;object-fit:cover;"></div> -->
          <div class="svc-photo-fill">
            <svg viewBox="0 0 24 24"><rect x="3" y="5" width="18" height="14" rx="1"/><circle cx="9" cy="11" r="2"/><path d="M3 17l5-4 4 3 5-5 4 4"/></svg>
            <span data-lang="en">AI Product Content</span><span data-lang="zh" style="display:none">AI产品内容</span>
          </div>
          <div class="svc-num-tag">05</div>
        </div>
        <div class="svc-body">
          <div class="svc-vline"></div>
          <div class="svc-cat"><span data-lang="en">AI Production</span><span data-lang="zh" style="display:none">AI制作</span></div>
          <div class="svc-heading"><span data-lang="en">AI Product<br><em>Content</em></span><span data-lang="zh" style="display:none">AI产品<br><em>内容</em></span></div>
          <p class="svc-p"><span data-lang="en">Studio-grade photos and videos of your products — generated with AI. No shoots, no sets, no waiting. Scroll-stopping creative at the speed of your roadmap.</span><span data-lang="zh" style="display:none">由AI生成的工作室级产品照片和视频。无需拍摄、无需布景、无需等待。以您的节奏打造吸睛创意内容。</span></p>
          <a href="contact.html" class="svc-cta-link"><span data-lang="en">Get Product Content</span><span data-lang="zh" style="display:none">获取产品内容</span></a>
        </div>
      </div>

      <!-- 06 -->
      <div class="svc-row flip reveal" id="service-06">
        <div class="svc-photo">
          <!-- To use a real image, replace the .svc-photo-fill div below with:
               <div class="svc-photo-fill"><img src="pictures/web design seo.JPG" alt="Web Design & SEO" style="width:100%;height:100%;object-fit:cover;"></div> -->
          <div class="svc-photo-fill">
            <svg viewBox="0 0 24 24"><rect x="3" y="4" width="18" height="16" rx="1"/><path d="M3 9h18M7 13h6M7 16h9"/></svg>
            <span data-lang="en">Web Design &amp; SEO</span><span data-lang="zh" style="display:none">网站设计与SEO</span>
          </div>
          <div class="svc-num-tag">06</div>
        </div>
        <div class="svc-body">
          <div class="svc-vline"></div>
          <div class="svc-cat"><span data-lang="en">Digital</span><span data-lang="zh" style="display:none">数字业务</span></div>
          <div class="svc-heading"><span data-lang="en">Web Design<br><em>&amp; SEO</em></span><span data-lang="zh" style="display:none">网站设计<br><em>与SEO</em></span></div>
          <p class="svc-p"><span data-lang="en">Conversion-built websites and the search visibility to fill them. We design, build, and rank — so the traffic your campaigns drive actually lands somewhere that sells.</span><span data-lang="zh" style="display:none">以转化为导向的网站，加上让流量涌入的搜索可见度。我们设计、构建并优化排名——让您营销活动带来的流量真正落地成交。</span></p>
          <a href="contact.html" class="svc-cta-link"><span data-lang="en">Build My Site</span><span data-lang="zh" style="display:none">打造我的网站</span></a>
        </div>
      </div>

    </div>
```

- [ ] **Step 2: Verify the rows exist and the block is well-formed**

```bash
grep -cE 'id="service-0[1-6]"' services.html
```

Expected: `6`

- [ ] **Step 3: Verify in the browser**

Run `npx serve .`, open `http://localhost:3000/services.html`. Expected: 6 service rows total. Rows 05 and 06 show a dark placeholder panel with a small icon and label ("AI Product Content" / "Web Design & SEO") on the photo side, and full copy on the body side. Row 05 has the photo on the left; row 06 (flip) has the photo on the right. (Reveal animations and language toggle are wired in Task 7 — at this point rows may appear faded; that is expected.) Stop the server.

- [ ] **Step 4: Commit**

```bash
git add services.html
git commit -m "Add AI Product Content and Web Design & SEO service rows"
```

---

## Task 7: Replace the services.html `<script>` with a trimmed version

The copied script references removed elements (`#hero`, `#founder-strip`, `.client-tab`). `founderObserver.observe(founderStrip)` throws because `founderStrip` is `null`, which halts the rest of the script. Replace the whole `<script>` block with a version that keeps only what `services.html` needs: custom cursor, hamburger menu, a simple nav-scrolled toggle, the reversible scroll observer for `.svc-row.reveal`, and the language toggle.

**Files:**
- Modify: `services.html`

- [ ] **Step 1: Replace the entire `<script>` … `</script>` block**

Replace everything from `    <script>` to `    </script>` (inclusive) with:

```html
    <script>
        // ── Custom Cursor (dot only) ──
        const dot = document.querySelector('.cursor-dot');
        let mouseX = 0, mouseY = 0;

        document.addEventListener('mousemove', (e) => {
            mouseX = e.clientX;
            mouseY = e.clientY;
            dot.style.left = mouseX + 'px';
            dot.style.top = mouseY + 'px';
        });

        // Hover detection for interactive elements
        document.querySelectorAll('a, button, .menu-links a, .hamburger, .lang-btn, .svc-cta-link').forEach(el => {
            el.addEventListener('mouseenter', () => dot.classList.add('hover'));
            el.addEventListener('mouseleave', () => dot.classList.remove('hover'));
        });

        // ── Hamburger Menu ──
        const hamburger = document.getElementById('hamburger');
        const overlay = document.getElementById('menuOverlay');
        const menuLinks = document.querySelectorAll('.menu-link');
        let menuOpen = false;

        hamburger.addEventListener('click', () => {
            menuOpen = !menuOpen;
            hamburger.classList.toggle('active');
            overlay.classList.toggle('active');
            document.body.style.overflow = menuOpen ? 'hidden' : '';
        });

        menuLinks.forEach(link => {
            link.addEventListener('click', () => {
                menuOpen = false;
                hamburger.classList.remove('active');
                overlay.classList.remove('active');
                document.body.style.overflow = '';
            });
        });

        // Close menu when clicking outside the links
        overlay.addEventListener('click', (e) => {
            if (e.target === overlay || !e.target.closest('.menu-links')) {
                menuOpen = false;
                hamburger.classList.remove('active');
                overlay.classList.remove('active');
                document.body.style.overflow = '';
            }
        });

        // ── Nav: solid background after scrolling ──
        const mainNav = document.getElementById('mainNav');
        window.addEventListener('scroll', () => {
            mainNav.classList.toggle('scrolled', window.scrollY > 50);
        });

        // ── Scroll direction tracking ──
        let lastScroll = window.scrollY;
        let scrollDir = 'down';
        window.addEventListener('scroll', () => {
            const curr = window.scrollY;
            scrollDir = curr > lastScroll ? 'down' : 'up';
            lastScroll = curr;
        });

        // ── Reversible observer (animate in on scroll down, out on scroll up) ──
        function createReversibleObserver(opts) {
            return new IntersectionObserver((entries) => {
                entries.forEach(entry => {
                    if (entry.isIntersecting) {
                        entry.target.classList.add('visible');
                    } else if (scrollDir === 'up' && entry.boundingClientRect.top > 0) {
                        entry.target.classList.remove('visible');
                    }
                });
            }, opts);
        }

        const observer = createReversibleObserver({
            threshold: 0.15,
            rootMargin: '0px 0px -40px 0px'
        });

        document.querySelectorAll('.svc-row.reveal').forEach((el, i) => {
            el.style.transitionDelay = `${i % 4 * 0.15}s`;
            observer.observe(el);
        });

        // ── Language Toggle ──
        const langEN = document.getElementById('langEN');
        const langZH = document.getElementById('langZH');
        let currentLang = localStorage.getItem('reed-lang') || 'en';

        function switchLang(lang) {
            currentLang = lang;
            localStorage.setItem('reed-lang', lang);

            langEN.classList.toggle('active', lang === 'en');
            langZH.classList.toggle('active', lang === 'zh');

            document.body.classList.toggle('lang-zh', lang === 'zh');

            document.querySelectorAll('[data-en][data-zh]').forEach(el => {
                const text = el.getAttribute('data-' + lang);
                if (el.tagName === 'A' && el.getAttribute('href') && el.getAttribute('href').startsWith('mailto:')) {
                    return;
                }
                el.innerHTML = text;
            });

            document.querySelectorAll('[data-lang="en"]:not(.lang-btn)').forEach(el => {
                el.style.display = lang === 'en' ? '' : 'none';
            });
            document.querySelectorAll('[data-lang="zh"]:not(.lang-btn)').forEach(el => {
                el.style.display = lang === 'zh' ? '' : 'none';
            });
        }

        langEN.addEventListener('click', () => switchLang('en'));
        langZH.addEventListener('click', () => switchLang('zh'));

        if (currentLang !== 'en') {
            switchLang(currentLang);
        }
    </script>
```

- [ ] **Step 2: Verify in the browser**

Run `npx serve .`, open `http://localhost:3000/services.html`:
- Open the browser dev console — expected: **no JavaScript errors**.
- Scroll down — service rows fade/slide in. Scroll back up — they reverse out.
- Click the hamburger — fullscreen menu opens with the 5 links; click a link or outside to close.
- Click "中文" — all service copy, category labels, headings, and CTAs switch to Chinese; click "EN" to switch back.
- Click the logo — navigates to `index.html`.
- Scroll down a little — nav gets a solid black background.

Stop the server.

- [ ] **Step 3: Commit**

```bash
git add services.html
git commit -m "Trim services.html script to page-relevant behavior"
```

---

## Task 8: Replace the homepage services section with a teaser

Replace the full `<div id="services">…</div>` block in `index.html` with a compact teaser: the same header, a names-only list of all 6 services (each deep-linking into `services.html`), and a "View All Services" button.

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace the services block**

In `index.html`, replace the entire block that starts with `    <!-- Services -->` and `    <div id="services">` and ends with its matching `    </div>` (the block currently containing the `.svc-row` styles and the four `<!-- 0N -->` rows) with:

```html
    <!-- Services Teaser -->
    <div id="services">
      <div class="section-header" style="padding: 40px 48px 32px; border-bottom: 1px solid var(--line);">
        <div class="section-label" style="margin-bottom:18px"><span data-lang="en">Expertise</span><span data-lang="zh" style="display:none">专业服务</span></div>
        <div class="section-title"><span data-lang="en">What we <em>do.</em></span><span data-lang="zh" style="display:none">我们的<em>服务</em></span></div>
      </div>

      <style>
        #services .section-label { font-size:10px; font-weight:600; letter-spacing:0.24em; text-transform:uppercase; color:var(--gold); }
        #services .section-title { font-family:'Barlow Condensed',sans-serif; font-size:clamp(40px,5vw,72px); font-weight:700; text-transform:uppercase; letter-spacing:-0.01em; color:var(--white); line-height:0.95; }
        #services .section-title em { font-family:'Playfair Display',serif; font-style:italic; font-weight:400; color:var(--gold); text-transform:none; letter-spacing:0; }
        .svc-tease-item { display:flex; align-items:center; gap:24px; padding:24px 48px; border-bottom:1px solid var(--line); text-decoration:none; transition:background 0.25s ease, padding-left 0.25s ease; cursor:none; }
        .svc-tease-item:hover { background:rgba(255,255,255,0.02); padding-left:56px; }
        .svc-tease-num { font-family:'Barlow Condensed',sans-serif; font-size:13px; font-weight:600; letter-spacing:0.18em; color:var(--gold); min-width:28px; }
        .svc-tease-name { font-family:'Barlow Condensed',sans-serif; font-size:clamp(20px,2.4vw,30px); font-weight:700; text-transform:uppercase; letter-spacing:0.01em; color:var(--white); }
        .svc-tease-badge { font-family:'Barlow Condensed',sans-serif; font-size:10px; font-weight:600; letter-spacing:0.22em; text-transform:uppercase; color:var(--gold); border:1px solid rgba(201,168,76,0.35); padding:4px 12px; }
        .svc-tease-new { margin-left:auto; font-family:'Barlow Condensed',sans-serif; font-size:11px; font-weight:600; letter-spacing:0.18em; text-transform:uppercase; color:var(--gold); }
        .svc-tease-foot { padding:56px 48px; }
        @media (max-width:768px) {
          #services .section-header { padding:32px 20px 24px !important; }
          #services .section-title { font-size:32px !important; }
          #services .section-label { font-size:9px !important; }
          .svc-tease-item { padding:18px 20px; gap:14px; }
          .svc-tease-item:hover { padding-left:20px; }
          .svc-tease-name { font-size:20px; }
          .svc-tease-foot { padding:40px 20px; }
        }
      </style>

      <div class="svc-tease-list">
        <a href="services.html#service-01" class="svc-tease-item"><span class="svc-tease-num">01</span><span class="svc-tease-name"><span data-lang="en">Mega KOL &amp; Celebrity Campaigns</span><span data-lang="zh" style="display:none">顶级KOL与明星合作</span></span></a>
        <a href="services.html#service-02" class="svc-tease-item"><span class="svc-tease-num">02</span><span class="svc-tease-name"><span data-lang="en">Creator Management</span><span data-lang="zh" style="display:none">创作者管理</span></span></a>
        <a href="services.html#service-03" class="svc-tease-item"><span class="svc-tease-num">03</span><span class="svc-tease-name"><span data-lang="en">Chinese Market</span><span data-lang="zh" style="display:none">中国市场</span></span><span class="svc-tease-badge"><span data-lang="en">Coming Soon</span><span data-lang="zh" style="display:none">即将推出</span></span></a>
        <a href="services.html#service-04" class="svc-tease-item"><span class="svc-tease-num">04</span><span class="svc-tease-name"><span data-lang="en">Brand Activations</span><span data-lang="zh" style="display:none">品牌活动</span></span></a>
        <a href="services.html#service-05" class="svc-tease-item"><span class="svc-tease-num">05</span><span class="svc-tease-name"><span data-lang="en">AI Product Content</span><span data-lang="zh" style="display:none">AI产品内容</span></span><span class="svc-tease-new"><span data-lang="en">New</span><span data-lang="zh" style="display:none">新</span></span></a>
        <a href="services.html#service-06" class="svc-tease-item"><span class="svc-tease-num">06</span><span class="svc-tease-name"><span data-lang="en">Web Design &amp; SEO</span><span data-lang="zh" style="display:none">网站设计与SEO</span></span><span class="svc-tease-new"><span data-lang="en">New</span><span data-lang="zh" style="display:none">新</span></span></a>
      </div>

      <div class="svc-tease-foot">
        <a href="services.html" class="btn-primary"><span data-lang="en">View All Services →</span><span data-lang="zh" style="display:none">查看所有服务 →</span></a>
      </div>
    </div>
```

Note: the section keeps `id="services"` per the spec. The `.btn-primary` class is already defined in `index.html`'s stylesheet.

- [ ] **Step 2: Verify the old rows are gone and the teaser is present**

```bash
grep -cE 'class="svc-row' index.html
```

Expected: `0`

```bash
grep -cE 'class="svc-tease-item"' index.html
```

Expected: `6`

- [ ] **Step 3: Verify in the browser**

Run `npx serve .`, open `http://localhost:3000/index.html`. Scroll to the services section. Expected: dark "What we do." header, then 6 numbered rows (names only), row 03 with a "Coming Soon" badge, rows 05 and 06 with a "New" marker, and a "View All Services →" button. Hovering a row nudges it right. The language toggle switches all of it to Chinese. Stop the server.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Replace homepage services section with teaser"
```

---

## Task 9: Update the homepage hamburger "Services" link

The homepage menu's "Services" link still points to the in-page `#services` anchor. Point it at the new page.

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Replace the link**

Replace:

```html
            <li><a href="#services" class="menu-link" data-en="Services" data-zh="服务">Services</a></li>
```

with:

```html
            <li><a href="services.html" class="menu-link" data-en="Services" data-zh="服务">Services</a></li>
```

- [ ] **Step 2: Verify**

```bash
grep -nE 'href="#services"' index.html
```

Expected: no output.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "Point homepage Services menu link to services.html"
```

---

## Task 10: Add services.html to the sitemap

**Files:**
- Modify: `sitemap.xml`

- [ ] **Step 1: Add the URL entry**

Replace:

```xml
  <url>
    <loc>https://www.reedcreativeagency.com/about.html</loc>
    <priority>0.8</priority>
  </url>
```

with:

```xml
  <url>
    <loc>https://www.reedcreativeagency.com/services.html</loc>
    <priority>0.9</priority>
  </url>
  <url>
    <loc>https://www.reedcreativeagency.com/about.html</loc>
    <priority>0.8</priority>
  </url>
```

- [ ] **Step 2: Verify**

```bash
grep -c 'services.html' sitemap.xml
```

Expected: `1`

- [ ] **Step 3: Commit**

```bash
git add sitemap.xml
git commit -m "Add services.html to sitemap"
```

---

## Task 11: Final cross-site verification

Confirm the spec's success criteria and that no other page references a stale `#services` link. (During planning, `about.html`, `brands.html`, `celebrities.html`, and `contact.html` were checked and have no "Services" nav links and no menu overlay — this step re-confirms.)

**Files:** none modified (verification only)

- [ ] **Step 1: Confirm no stale services anchors site-wide**

```bash
grep -rn 'href="#services"' *.html
```

Expected: no output.

- [ ] **Step 2: Confirm every "Services" link points to services.html**

```bash
grep -rnE 'href="[^"]*services\.html' *.html
```

Expected: matches in `index.html` (menu link + teaser rows + button) and `services.html` (menu link). No broken or unexpected targets.

- [ ] **Step 3: End-to-end browser check**

Run `npx serve .`. Then:
- Open `http://localhost:3000/index.html`. Open the hamburger menu → click "Services" → lands on `services.html`.
- On the homepage teaser, click row "05 AI Product Content" → lands on `services.html` scrolled to the AI Product Content row (`#service-05`).
- Click "View All Services →" → lands on `services.html` at the top.
- On `services.html`: all 6 rows present, hamburger menu works, "← back" via logo works, China Market / Our Clients menu links go to `index.html#china` / `index.html#our-clients` and land on the right homepage sections.
- Toggle EN/中文 on both pages — all new content translates, and the choice persists across navigation (it is stored in `localStorage`).
- Check at mobile width (dev tools, ~375px): teaser rows and service rows stack and remain readable.
- Browser console shows no errors on either page.

Stop the server.

- [ ] **Step 4: Confirm the working tree is clean**

```bash
git status
```

Expected: nothing to commit, working tree clean (all task commits already made). `index-v2.html` remains untracked and untouched — that is expected and out of scope.

---

## Self-Review Notes

- **Spec coverage:** new `services.html` (Tasks 1–7), hamburger nav on it (copied + Task 4), dark header + 6 rows with anchor ids (Tasks 5–6), new services 05/06 with placeholder + image wiring (Task 6), homepage teaser with deep-links + "New"/"Coming Soon" markers + button (Task 8), homepage menu link (Task 9), sitemap (Task 10), bilingual content (Tasks 6 & 8), nav-link audit of other pages (Task 11). No prices anywhere. `index-v2.html` untouched.
- **No automated tests:** this is a static site with no test framework; verification is browser + grep, stated up front.
- **Type/name consistency:** anchor ids `service-01`…`service-06` are used identically in Tasks 5, 6, and 8. Class names `svc-tease-item` / `svc-tease-num` / `svc-tease-name` / `svc-tease-badge` / `svc-tease-new` are defined and used only within Task 8.
