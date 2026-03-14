# HTML & Web Development Interview Questions & Answers 🚀

> **2026 Edition** | Beginner → Advanced  
> Covers: Browser Internals · Modern APIs · Accessibility · Security · Performance

---

## 📖 How to Use This Guide

Questions are organized from basics to advanced. All topics are commonly tested in 2026 interviews.  
Pay special attention to **browser internals** — the most common knowledge gap among candidates.

| Tag | Meaning |
|-----|---------|
| `[Beginner]` | No prior knowledge needed |
| `[Intermediate]` | Assumes basic HTML/CSS/JS familiarity |
| `[Advanced]` | Deep browser engine, performance & security knowledge |
| `[2026 Trend]` | Newly standardized or emerging features |

---

## Table of Contents

1. [How the Web & Browser Work](#-section-1-how-the-web--browser-work)
2. [HTML Fundamentals](#-section-2-html-fundamentals)
3. [Forms & User Input](#-section-3-forms--user-input)
4. [Accessibility (a11y)](#️-section-4-accessibility-a11y)
5. [HTML5 & Modern Browser APIs](#-section-5-html5--modern-browser-apis)
6. [Performance & Core Web Vitals](#-section-6-performance--core-web-vitals)
7. [Web Security](#-section-7-web-security)
8. [Modern HTML in 2026](#-section-8-modern-html-in-2026)
9. [HTML + CSS Integration](#-section-9-html--css-integration)
10. [HTML + JavaScript Integration](#️-section-10-html--javascript-integration)
11. [Best Practices & Code Quality](#-section-11-best-practices--code-quality)
12. [Quick Reference: Semantic Elements](#-quick-reference-semantic-elements)
13. [Resources](#-resources)

---

## 🌐 Section 1: How the Web & Browser Work

> Most candidates can write HTML but can't explain what the browser does with it. This section covers the most common knowledge gap in 2026 interviews.

---

### Q1. What happens step-by-step when you type a URL and press Enter? `[Advanced]`

**Answer:**

1. Browser checks its **DNS cache**
2. OS performs **DNS resolution** → recursive resolver → root → TLD → authoritative nameserver
3. **TCP 3-way handshake** with the resolved server IP
4. **TLS handshake** (for HTTPS — TLS 1.3 is the current standard)
5. Browser sends **HTTP GET** request
6. Server returns **HTML**
7. Browser parses HTML, builds the **DOM**
8. Sub-resources (CSS, JS, images) are **fetched** — speculatively parsed ahead if the browser's preload scanner finds them
9. **CSSOM** is built from CSS
10. **Render Tree** is constructed (DOM + CSSOM)
11. **Layout** — positions and sizes calculated
12. **Paint** — pixels drawn
13. **Compositing** — layers assembled by GPU → page visible

> 💡 **Note:** This single question tests DNS, TCP/IP, TLS, HTTP, and rendering all at once. Know it cold.

---

### Q2. What is the DOM and how is it different from HTML source code? `[Beginner]`

**Answer:**

The **DOM (Document Object Model)** is a live, in-memory tree that the browser builds from HTML. JavaScript can modify it dynamically.

- The **HTML source file** is static text on disk
- After `document.createElement()` runs, the DOM changes but the source file does not
- DevTools shows the **live DOM**, not the original source

---

### Q3. Explain the browser rendering pipeline. `[Advanced]`

**Answer:**

| Step | Name | Description |
|------|------|-------------|
| 1 | **DOM** | HTML parsed into a node tree |
| 2 | **CSSOM** | CSS parsed into a style tree |
| 3 | **Render Tree** | DOM + CSSOM combined — visible nodes only |
| 4 | **Layout (Reflow)** | Exact pixel positions and sizes calculated |
| 5 | **Paint** | Pixels drawn into layers |
| 6 | **Composite** | GPU assembles layers into final screen image |

Understanding this pipeline is essential for diagnosing performance problems. Steps 4–6 can be selectively bypassed by using `transform` and `opacity` for animations, which go straight to compositing.

---

### Q4. What is reflow vs repaint, and which is more expensive? `[Advanced]`

**Answer:**

| Type | Trigger | Cost |
|------|---------|------|
| **Reflow (Layout)** | Changing `width`, `height`, `font-size`, `top`, `left` | Very expensive — cascades through the tree |
| **Repaint** | Changing `color`, `background`, `box-shadow` | Cheaper — no geometry recalculation |
| **Compositing** | `transform`, `opacity` | Cheapest — GPU only, no CPU involvement |

> 💡 **Rule:** Animate with `transform`/`opacity`, never with `width`/`height`/`top`/`left`. Reading layout properties (e.g., `offsetHeight`) immediately after a DOM write causes **forced synchronous layouts** — a performance anti-pattern sometimes called "layout thrashing."

---

### Q5. What is the Critical Rendering Path and how do you optimize it? `[Advanced]`

**Answer:**

The Critical Rendering Path is the sequence of steps to show the first pixel on screen.

**Optimization techniques:**
- Inline critical CSS above the fold
- Use `async` or `defer` on all JavaScript
- Preload hero images and key fonts with `<link rel="preload">`
- Eliminate render-blocking resources
- Serve assets via HTTP/2 or HTTP/3
- Use `preconnect` for third-party origins
- Use the **Speculation Rules API** to prerender likely next pages for near-instant navigations

---

### Q6. What is HTTP/1.1 vs HTTP/2 vs HTTP/3? `[Intermediate]`

**Answer:**

| Version | Transport | Key Feature |
|---------|-----------|-------------|
| **HTTP/1.1** | TCP (multiple connections) | Max ~6 parallel connections per domain |
| **HTTP/2** | TCP (single multiplexed connection) | Many requests simultaneously, no domain sharding needed |
| **HTTP/3** | QUIC (UDP-based) | Faster handshakes, resilient to packet loss, no head-of-line blocking |

> 💡 HTTP/2 removes the need for aggressive JS/CSS bundling. HTTP/3 is especially beneficial on mobile and lossy networks. In 2026, HTTP/3 adoption is at ~30% of all web traffic.

---

### Q7. What is HTTPS and TLS, and why must every site use HTTPS? `[Beginner]`

**Answer:**

**HTTPS = HTTP over TLS**. TLS encrypts data in transit, authenticates the server via certificates, and prevents man-in-the-middle attacks.

Modern browsers:
- Mark HTTP sites as **"Not Secure"**
- Block mixed content (HTTP resources on HTTPS pages)
- Restrict APIs (**Service Workers, Geolocation, Camera, Clipboard**) to HTTPS origins only
- In 2026, Chrome is moving toward **blocking HTTP entirely** for new navigations as the next step in its HTTPS-first roadmap

> TLS 1.3 is the current standard. TLS 1.0/1.1 are deprecated and blocked by all major browsers.

---

### Q8. Explain browser caching: Cache-Control, ETags, and Service Workers. `[Advanced]`

**Answer:**

- **Cache-Control headers** tell browsers how long to cache resources (`max-age=31536000` = 1 year). Use with **content-hashed filenames** for zero-downtime deploys.
- **ETags** are content fingerprints — browser sends the ETag back, server replies `304 Not Modified` if unchanged (saves bandwidth)
- **Service Workers** enable programmatic caching via the Cache API, making offline-first apps possible — far more powerful and flexible than HTTP cache. They also enable push notifications, background sync, and the foundation for PWAs.

---

## 📄 Section 2: HTML Fundamentals

---

### Q9. What is HTML and what version are we on in 2026? `[Beginner]`

**Answer:**

HTML (HyperText Markup Language) is the structure language of the web. There is **no HTML6** — the standard is the **HTML Living Standard**, maintained continuously by WHATWG. HTML5 (2014) introduced most modern features and remains the foundation. Browsers implement the living standard, which evolves regularly through new elements, attributes, and API integration.

> ⚠️ Be skeptical of any article claiming "HTML6 is here" — this is not an official versioned standard. The Living Standard is the authoritative specification.

---

### Q10. What is the DOCTYPE declaration? `[Beginner]`

**Answer:**

`<!DOCTYPE html>` tells the browser to use **standards mode**, not quirks mode. Without it, browsers may use legacy 1990s rendering behavior. Always place it as the very first line.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Page Title</title>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

---

### Q11. What are semantic HTML elements and why do they matter? `[Intermediate]`

**Answer:**

Semantic elements describe the *purpose* of content, not just its appearance.

| Benefit | How |
|---------|-----|
| **Accessibility** | Screen readers use landmarks to navigate |
| **SEO** | Search engines understand page structure |
| **Maintainability** | Code is self-documenting |

```html
<!-- ❌ Non-semantic -->
<div class="header"><div class="nav">...</div></div>

<!-- ✅ Semantic -->
<header><nav>...</nav></header>
<main><article>...</article></main>
<footer>...</footer>
```

---

### Q12. What is the difference between `<div>`, `<section>`, and `<article>`? `[Intermediate]`

**Answer:**

| Element | Semantic Meaning | Use When |
|---------|-----------------|----------|
| `<div>` | None — generic container | Last resort for layout grouping |
| `<section>` | Thematic grouping with a heading | Part of a larger whole |
| `<article>` | Fully self-contained content | Blog post, news item, comment — could be syndicated independently |

---

### Q13. What are `data-*` attributes? `[Intermediate]`

**Answer:**

Custom attributes that store metadata on elements. Accessible in JS via `element.dataset.propertyName` (camelCase).

```html
<button data-product-id="42" data-action="add-cart">Buy Now</button>

<script>
  btn.addEventListener('click', () => {
    console.log(btn.dataset.productId); // "42"
    console.log(btn.dataset.action);    // "add-cart"
  });
</script>
```

---

### Q14. What are HTML entities? `[Beginner]`

**Answer:**

Entities represent characters with special HTML meaning or that can't be typed directly.

| Entity | Character | When to use |
|--------|-----------|-------------|
| `&lt;` | `<` | Angle brackets in text content |
| `&gt;` | `>` | Angle brackets in text content |
| `&amp;` | `&` | Ampersand in text content |
| `&nbsp;` | non-breaking space | Prevent line break between words |
| `&copy;` | © | Copyright symbol |

---

### Q15. What is the viewport meta tag and why is it critical? `[Beginner]`

**Answer:**

Without it, mobile browsers render at ~980px desktop width and zoom out — making text tiny and unreadable.

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

> 💡 **Omitting this is one of the most common beginner mistakes. Required for ALL responsive sites.**

---

## 📝 Section 3: Forms & User Input

---

### Q16. What new input types did HTML5 introduce? `[Beginner]`

**Answer:**

`email`, `url`, `number`, `range`, `date`, `time`, `datetime-local`, `month`, `week`, `search`, `tel`, `color`

Benefits — all without JavaScript:
- **Native validation** (email format, number ranges)
- **Mobile-optimized keyboards** (numeric keypad for `type="number"`)
- **Better accessibility**

```html
<input type="email" required placeholder="you@example.com">
<input type="date" min="2024-01-01">
<input type="range" min="0" max="100" step="5">
```

---

### Q17. What is the difference between GET and POST in HTML forms? `[Beginner]`

**Answer:**

| Method | Data Location | Use For |
|--------|--------------|---------|
| **GET** | URL query parameters | Searches, filters, bookmarkable actions |
| **POST** | Request body | Passwords, file uploads, state-changing actions |

> ⚠️ **Never send sensitive data via GET** — it appears in browser history, server logs, and Referer headers.

---

### Q18. How do you make a form accessible? `[Intermediate]`

**Answer:**

```html
<form>
  <fieldset>
    <legend>Account Details</legend>

    <!-- ✅ Explicit label association -->
    <label for="email">Email address</label>
    <input type="email" id="email" name="email"
           required aria-describedby="email-hint">
    <p id="email-hint">We'll never share your email.</p>

    <!-- ✅ Error message near the input -->
    <span role="alert" aria-live="polite">Please enter a valid email.</span>
  </fieldset>
</form>
```

**Checklist:**
- [ ] Every input has a `<label>` (via `for`/`id` or wrapping)
- [ ] Related inputs grouped with `<fieldset>` + `<legend>`
- [ ] Hints use `aria-describedby`
- [ ] Error messages are near the input, not just color-coded
- [ ] Keyboard navigable with visible focus states

---

### Q19. What is native vs custom form validation? `[Intermediate]`

**Answer:**

**Native** — browser-built-in, accessible by default:
```html
<input type="email" required minlength="5" pattern="[a-z]+@[a-z]+\.[a-z]{2,}">
```

**Custom** — via Constraint Validation API:
```javascript
input.addEventListener('input', () => {
  if (input.value.includes('admin')) {
    input.setCustomValidity('Username "admin" is not allowed');
  } else {
    input.setCustomValidity(''); // clear error
  }
});
```

> 💡 In 2026: libraries like **Zod** and **Valibot** are popular for schema validation in JS frameworks, but always layer on top of native HTML validation.

---

### Q20. What is the new customizable `<select>` element? `[2026 Trend]`

**Answer:**

For decades, `<select>` was one of the least styleable native HTML elements. The new **customizable select** (reached Baseline Newly Available status in 2025) allows full CSS control while preserving built-in accessibility, keyboard navigation, and form integration:

```css
/* Opt in to the new customizable select */
select,
::picker(select) {
  appearance: base-select;
}

/* Now style freely */
select {
  border: 2px solid oklch(60% 0.2 250);
  border-radius: 0.5rem;
  padding: 0.5rem;
}

::picker(select) {
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  box-shadow: 0 4px 16px rgba(0,0,0,0.1);
}
```

This is built on top of the Popover API and CSS Anchor Positioning internally, and offers a progressive-enhancement approach — non-supporting browsers fall back to the native select automatically.

---

## ♿ Section 4: Accessibility (a11y)

---

### Q21. What is web accessibility and why is it legally required? `[Intermediate]`

**Answer:**

Accessibility ensures websites work for people with visual, motor, auditory, or cognitive disabilities.

- **Legally:** ADA (US), EN 301 549 (EU), and similar laws require accessible digital products. Over 4,000 ADA web accessibility lawsuits were filed in the US in 2023 alone.
- **Scale:** ~1.3 billion people globally have some form of disability
- **UX benefit:** Captions help in noisy environments. Keyboard navigation helps power users. Good contrast helps everyone in sunlight.

---

### Q22. What is ARIA and when should you use it? `[Intermediate]`

**Answer:**

ARIA (Accessible Rich Internet Applications) adds semantic context that HTML alone can't express.

> 🔑 **First Rule of ARIA: Don't use ARIA if native HTML can do it.**

```html
<!-- ❌ Bad — ARIA used unnecessarily -->
<div role="button" tabindex="0" onclick="submit()">Submit</div>

<!-- ✅ Good — use native element -->
<button type="submit">Submit</button>
```

**When ARIA is appropriate:**
- Custom widgets (comboboxes, tabs, sliders)
- Dynamic content updates (`aria-live`)
- Supplementing native semantics

---

### Q23. What are WCAG guidelines? `[Intermediate]`

**Answer:**

**WCAG 2.2** (2023) is the current standard. Three conformance levels:

| Level | Description |
|-------|-------------|
| **A** | Minimum — must fix these |
| **AA** | Legal standard in most countries — target this |
| **AAA** | Aspirational — hard to achieve across all content |

**POUR Principles:** Perceivable, Operable, Understandable, Robust

> ✨ **2026 note:** WCAG 3.0 is under active development and is expected to replace the pass/fail model with a graduated scoring system. It is not yet finalized but awareness of its direction is a strong interview signal.

---

### Q24. What are the rules for good `alt` text? `[Beginner]`

**Answer:**

| Image type | Alt text |
|------------|----------|
| Informative | Describe content and function |
| Decorative | `alt=""` — screen readers skip it |
| Functional (inside a link) | Describe the action, not the image |
| Chart/graph | Summarize the data |

```html
<!-- ✅ Good -->
<img src="checkout.png" alt="Shopping cart with 3 items">
<img src="divider.png" alt="">  <!-- decorative -->

<!-- ❌ Bad -->
<img src="product.jpg" alt="image">
<img src="product.jpg">  <!-- missing entirely -->
```

---

### Q25. What is keyboard navigation and why does it matter? `[Intermediate]`

**Answer:**

All interactive elements must be reachable and operable via keyboard alone.

**Requirements:**
- Logical tab order — use `tabindex="0"`, avoid positive values
- Visible focus indicator — **never** `outline: none` without replacement; use `:focus-visible` for accessible, non-intrusive styles
- Custom widgets must implement ARIA keyboard patterns (arrow keys for menus)
- Modals must **trap focus** until closed

> In 2026, native `<dialog>` and the Popover API handle focus trapping and `Escape` key behavior automatically — prefer them over custom implementations.

---

## 🚀 Section 5: HTML5 & Modern Browser APIs

---

### Q26. What is the Intersection Observer API? `[Intermediate]`

**Answer:**

Efficiently detects when elements enter or leave the viewport — no scroll event listeners needed.

```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      loadImage(entry.target);
      observer.unobserve(entry.target); // stop watching after load
    }
  });
}, { rootMargin: '200px' }); // load 200px before visible

document.querySelectorAll('img[data-src]').forEach(img => observer.observe(img));
```

> 💡 **This is the correct 2026 approach.** Never use `scroll` + `getBoundingClientRect()` in production.

---

### Q27. What are Web Workers? `[Advanced]`

**Answer:**

Web Workers run JavaScript in background threads, separate from the main thread.

**Why it matters:** The main thread handles all rendering and user interaction. Long computations freeze the UI.

```javascript
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data: largeArray });
worker.onmessage = (e) => console.log('Result:', e.data);

// worker.js
self.onmessage = (e) => {
  const result = heavyComputation(e.data.data);
  self.postMessage(result);
};
```

> In 2026: `SharedArrayBuffer` + `Atomics` enable shared memory between workers for high-performance apps. **OffscreenCanvas** allows canvas rendering in a worker, offloading WebGL and 2D canvas work from the main thread entirely.

---

### Q28. What are Service Workers and Progressive Web Apps (PWAs)? `[Advanced]`

**Answer:**

A **Service Worker** is a background script acting as a programmable network proxy. It enables:
- **Offline functionality** (Cache API)
- **Push notifications**
- **Background sync**

**PWAs** = Service Worker + Web App Manifest = installable, app-like experiences on any device.

```javascript
// Register a service worker
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
}

// sw.js — cache-first strategy
self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then(cached => cached || fetch(event.request))
  );
});
```

> In 2026: iOS Safari now supports Push API and install prompts — PWAs are truly cross-platform. The **File System Access API** and **Web Share API** further close the gap with native apps.

---

### Q29. What is WebAssembly (Wasm)? `[Advanced]`

**Answer:**

A binary instruction format that runs at near-native speed in browsers. Languages like C, C++, Rust, and Go compile to Wasm.

**Use cases:**
- Game engines (Unity, Unreal)
- Video/image processing (FFmpeg in-browser)
- Scientific computation
- Blockchain clients

> In 2026: **WASI** (WebAssembly System Interface) extends Wasm outside browsers. The **Component Model** standardizes module interop. **WasmGC** (garbage collection for Wasm) is now broadly supported, enabling managed languages like Kotlin and Dart to compile to Wasm efficiently.

---

### Q30. What is localStorage vs sessionStorage vs IndexedDB? `[Intermediate]`

**Answer:**

| Storage | Persistence | Size | Async | Best For |
|---------|------------|------|-------|----------|
| `localStorage` | Until cleared | ~5MB | ❌ Sync | Small settings/preferences |
| `sessionStorage` | Until tab closes | ~5MB | ❌ Sync | Temporary session data |
| `IndexedDB` | Until cleared | GBs | ✅ Async | Large data, offline-first apps |

> ⚠️ **Never store passwords, tokens, or sensitive data in any of these.** All are accessible to JavaScript — an XSS vulnerability exposes everything. Use `HttpOnly` cookies for auth tokens.

---

### Q31. What is the File System Access API? `[2026 Trend]`

**Answer:**

The File System Access API allows web apps to read from and write to the user's local file system with explicit permission — enabling browser-native code editors, image editors, and document tools.

```javascript
// Open a file picker and read a file
const [fileHandle] = await window.showOpenFilePicker();
const file = await fileHandle.getFile();
const contents = await file.text();

// Save back to disk
const writable = await fileHandle.createWritable();
await writable.write(updatedContents);
await writable.close();
```

Topped the positive sentiment chart in the State of HTML 2025 survey. Available in Chrome and Edge; Firefox support is in development.

---

## ⚡ Section 6: Performance & Core Web Vitals

---

### Q32. What are Core Web Vitals? `[Intermediate]`

**Answer:**

Google's real-user metrics that directly affect search rankings:

| Metric | Measures | Good Score |
|--------|----------|------------|
| **LCP** (Largest Contentful Paint) | Loading performance | < 2.5s |
| **INP** (Interaction to Next Paint) | Responsiveness *(replaced FID in March 2024)* | < 200ms |
| **CLS** (Cumulative Layout Shift) | Visual stability | < 0.1 |

> ✨ **2026 note:** INP is now the official responsiveness metric. It measures the full event duration (from input to next paint) rather than just input latency. Optimizing INP usually means reducing long tasks on the main thread, using `scheduler.yield()`, and breaking up heavy JavaScript.

---

### Q33. How do you implement lazy loading? `[Beginner]`

**Answer:**

```html
<!-- ✅ Native lazy loading — universally supported in 2026 -->
<img src="photo.jpg" loading="lazy" alt="Product" width="800" height="600">
<iframe src="map.html" loading="lazy"></iframe>
```

> 💡 Always add `width` and `height` attributes to prevent **CLS** (layout shift before the image loads). Use `fetchpriority="high"` on the LCP image to signal it should load as soon as possible.

---

### Q34. What image formats should you use in 2026? `[Intermediate]`

**Answer:**

| Format | Size vs JPEG | Support | Use For |
|--------|-------------|---------|---------|
| **AVIF** | ~50% smaller | All modern browsers | Photos, illustrations |
| **WebP** | ~30% smaller | Universal | Photos, illustrations (fallback) |
| **SVG** | Scalable | Universal | Icons, logos, diagrams |
| **JPEG** | Baseline | Universal | Fallback only |

```html
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Description" width="800" height="400"
       loading="lazy" fetchpriority="auto">
</picture>
```

---

### Q35. What are resource hints? `[Advanced]`

**Answer:**

```html
<!-- Fetch this critical resource immediately (LCP image, key font) -->
<link rel="preload" href="hero.jpg" as="image" fetchpriority="high">
<link rel="preload" href="font.woff2" as="font" crossorigin>

<!-- Fetch for likely next navigation (idle time) -->
<link rel="prefetch" href="/next-page.html">

<!-- Open TCP+TLS connection to third-party origin early -->
<link rel="preconnect" href="https://fonts.googleapis.com">

<!-- Resolve DNS only (cheaper than preconnect) -->
<link rel="dns-prefetch" href="https://analytics.example.com">
```

> ⚠️ Use `preload` sparingly — over-fetching wastes bandwidth and can hurt LCP by competing with the actual critical resources.

---

### Q36. What is the Speculation Rules API? `[2026 Trend]`

**Answer:**

The Speculation Rules API lets you tell Chromium-based browsers which pages to prefetch or fully prerender in the background. Prerendered pages load nearly instantly when the user navigates to them.

```html
<script type="speculationrules">
{
  "prerender": [{
    "where": { "href_matches": "/*" },
    "eagerness": "moderate"
  }]
}
</script>
```

**Eagerness levels:**
- `immediate` — speculate as soon as possible on page load
- `moderate` — speculate on hover (desktop) or proximity scroll (mobile)
- `conservative` — speculate on mousedown/touchstart (minimal waste)

> Currently supported in Chromium-based browsers (Chrome, Edge, Opera). Safari and Firefox are working on support. Non-supporting browsers silently ignore the script tag, making it safe to deploy as progressive enhancement. Always exclude state-changing URLs (e.g., `/logout`) from speculation rules.

---

## 🔒 Section 7: Web Security

---

### Q37. What is XSS and how do you prevent it? `[Advanced]`

**Answer:**

**XSS (Cross-Site Scripting):** attacker injects malicious scripts executed in a victim's browser.

| Type | Source |
|------|--------|
| Stored | Malicious script saved in database |
| Reflected | Injected via URL parameter |
| DOM-based | Via client-side JavaScript |

**Prevention:**
```javascript
// ❌ Dangerous — executes any HTML including scripts
element.innerHTML = userInput;

// ✅ Safe — treats input as plain text
element.textContent = userInput;

// ✅ When HTML is required — sanitize first
import DOMPurify from 'dompurify';
element.innerHTML = DOMPurify.sanitize(userInput);
```

Also: Use a strict **Content Security Policy**, set `HttpOnly` on sensitive cookies. The new browser-native **Sanitizer API** is emerging as a built-in alternative to DOMPurify.

---

### Q38. What is Content Security Policy (CSP)? `[Advanced]`

**Answer:**

An HTTP header specifying which resource origins the browser may load — prevents injected external scripts from executing.

```http
Content-Security-Policy: default-src 'self'; script-src 'self' cdn.example.com; style-src 'self'
```

**Nonce-based CSP (gold standard in 2026):**
```html
<!-- Server generates a random nonce per request -->
<script nonce="rAnd0m123">/* legitimate code */</script>
```
```http
Content-Security-Policy: script-src 'nonce-rAnd0m123'
```

---

### Q39. What is CSRF and how do you prevent it? `[Advanced]`

**Answer:**

**CSRF (Cross-Site Request Forgery):** tricks an authenticated user's browser into making unintended requests.

**Prevention:**
1. **CSRF tokens** on all state-changing requests
2. **`SameSite=Lax` or `Strict`** cookie attribute — now the browser default, significantly reduces CSRF risk
3. **Validate `Origin`/`Referer` headers** server-side

---

### Q40. What is CORS? `[Advanced]`

**Answer:**

**CORS (Cross-Origin Resource Sharing):** browser security mechanism restricting cross-origin requests.

```
Page on example.com → fetch from api.other.com
                      ↓
Browser blocks unless api.other.com responds with:
Access-Control-Allow-Origin: https://example.com
```

> ⚠️ CORS is **enforced by browsers only** — it doesn't protect server-to-server calls. Browsers send a preflight `OPTIONS` request for non-simple requests (e.g., requests with custom headers or a JSON body). Responding with `Access-Control-Allow-Origin: *` disables CORS protection for that endpoint.

---

### Q41. What is Subresource Integrity (SRI)? `[Advanced]`

**Answer:**

SRI lets you lock a `<script>` or `<link>` to a specific content hash. If a CDN serves a tampered file, the browser rejects it.

```html
<script
  src="https://cdn.example.com/lib.min.js"
  integrity="sha384-abc123..."
  crossorigin="anonymous">
</script>
```

> Essential when loading third-party scripts from CDNs. Pairs with a strict CSP for defense-in-depth.

---

## 🔮 Section 8: Modern HTML in 2026

---

### Q42. What is the Popover API? `[2026 Trend]`

**Answer:**

The Popover API reached **Baseline Newly Available** status in 2025 and is stable in all major browsers. It provides native HTML for tooltips, dropdowns, and floating overlays — no JS positioning library needed.

```html
<button popovertarget="menu">Open Menu</button>

<div id="menu" popover>
  <ul>
    <li>Profile</li>
    <li>Settings</li>
    <li>Logout</li>
  </ul>
</div>
```

Browser handles automatically: top-layer rendering (no z-index battles), focus management, light-dismiss (click outside), keyboard (`Escape` to close), and accessibility. Combine with CSS Anchor Positioning to place the popover relative to the trigger button.

---

### Q43. What is the native `<dialog>` element? `[2026 Trend]`

**Answer:**

Provides a modal/non-modal dialog with built-in focus trapping, backdrop overlay, and accessibility.

```html
<dialog id="confirm">
  <h2>Are you sure?</h2>
  <button onclick="document.getElementById('confirm').close()">Cancel</button>
  <button>Confirm</button>
</dialog>

<button onclick="document.getElementById('confirm').showModal()">Delete</button>
```

```css
dialog::backdrop {
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
}
```

> Universally supported — the correct way to build modals in 2026. No ARIA hacks needed. Use `.showModal()` for accessible modal behavior and `.show()` for non-modal dialogs.

---

### Q44. What is the View Transitions API? `[2026 Trend]`

**Answer:**

Enables smooth animated transitions between DOM states or full-page navigations — no animation library needed. Same-document View Transitions are **Baseline** (broadly supported). Cross-document transitions use `@view-transition { navigation: auto; }`.

```javascript
// Animate between two states
document.startViewTransition(() => {
  updateDOMToNewState();
});
```

```css
/* Customize the animation */
::view-transition-old(root) {
  animation: 300ms ease-out fade-out;
}
::view-transition-new(root) {
  animation: 300ms ease-in fade-in;
}

/* Named element transition */
.hero { view-transition-name: hero; } /* Must be unique per page */
```

---

### Q45. What is structured data (JSON-LD)? `[Intermediate]`

**Answer:**

Embeds machine-readable schema.org data for search engine Rich Results (star ratings, FAQ dropdowns, product prices in search).

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is HTML?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "HTML is the standard markup language for web pages."
      }
    }
  ]
}
</script>
```

---

### Q46. What emerging browser features are most important in 2026? `[2026 Trend]`

**Answer:**

| Feature | Status | What it does |
|---------|--------|-------------|
| **CSS Anchor Positioning** | Baseline (Chromium + Safari) | Position popovers/tooltips relative to any element natively |
| **Speculation Rules API** | Chrome/Edge (Chromium) | Prerender next pages for near-instant navigation |
| **Scroll-driven Animations** | Baseline | CSS animations tied to scroll position — no JS |
| **Customizable `<select>`** | Baseline Newly Available | Fully styleable native select element |
| **Declarative Shadow DOM** | Baseline | Server-rendered Web Components |
| **WebGPU** | Chrome, Edge, Firefox | Next-gen GPU compute and graphics in the browser |
| **`scheduler.yield()`** | Chrome/Edge | Yield to main thread for better INP without `setTimeout` hacks |
| **CSS `if()` function** | Chrome/Edge | Inline conditional CSS values based on media/style/support queries |

---

## 🎨 Section 9: HTML + CSS Integration

---

### Q47. What are the three ways to apply CSS? `[Beginner]`

**Answer:**

```html
<!-- 1. External stylesheet — always preferred for real projects -->
<link rel="stylesheet" href="styles.css">

<!-- 2. Internal <style> block — useful for page-specific critical CSS -->
<style>
  body { margin: 0; }
</style>

<!-- 3. Inline styles — only for dynamic styles set by JS -->
<p style="color: red;">Text</p>
```

---

### Q48. What is the CSS Box Model? `[Beginner]`

**Answer:**

Every element is a box: `content → padding → border → margin`

```css
/* Default: width/height only includes content */
box-sizing: content-box;

/* Better: width/height includes padding and border */
box-sizing: border-box;

/* ✅ Always set this globally in new projects */
*, *::before, *::after {
  box-sizing: border-box;
}
```

---

### Q49. What are CSS Custom Properties (variables)? `[Intermediate]`

**Answer:**

Native CSS variables that cascade, can be overridden per component, and are accessible in JavaScript. In 2026, they are the foundation of design token systems and dynamic theming.

```css
:root {
  --color-primary: oklch(55% 0.2 250);
  --spacing-md: 1rem;
}

/* Dark mode override */
@media (prefers-color-scheme: dark) {
  :root {
    --color-primary: oklch(75% 0.15 250);
  }
}

.button {
  background: var(--color-primary);
  padding: var(--spacing-md);
}
```

---

### Q50. What is the difference between Flexbox and Grid? `[Intermediate]`

**Answer:**

| Layout | Dimensions | Best For |
|--------|-----------|----------|
| **Flexbox** | One-dimensional (row OR column) | Navigation bars, card rows, centering content |
| **Grid** | Two-dimensional (rows AND columns) | Page layouts, complex dashboards |

> 💡 They're complementary: use **Grid for macro layout**, **Flexbox for component-level layout**.

---

### Q51. What are CSS Container Queries and why are they important? `[2026 Trend]`

**Answer:**

Container Queries let components style themselves based on their **container's size**, not the viewport — enabling truly reusable responsive components.

```css
/* Component adapts to its container, not screen size */
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 600px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 2fr;
  }
}
```

> In 2026, **container style queries** extend this further — components can adapt based on CSS custom property values on their container, enabling powerful context-aware theming.

---

## ⚙️ Section 10: HTML + JavaScript Integration

---

### Q52. What is the difference between `async` and `defer`? `[Intermediate]`

**Answer:**

Both load scripts without blocking HTML parsing.

| Attribute | Execution | Order | Use For |
|-----------|-----------|-------|---------|
| `async` | As soon as downloaded | Any order | Independent scripts (analytics) |
| `defer` | After HTML fully parsed | Document order | DOM-dependent scripts |

```html
<script src="analytics.js" async></script>    <!-- independent -->
<script src="app.js" defer></script>           <!-- depends on DOM -->
```

> In 2026: most scripts should use `defer`. Avoid bare `<script>` in `<head>` without `async`/`defer` as it blocks HTML parsing.

---

### Q53. What is event delegation? `[Intermediate]`

**Answer:**

Attach one event listener to a common ancestor instead of many listeners on individual children.

```javascript
// ❌ Bad — attaches 1000 listeners
document.querySelectorAll('.item').forEach(item => {
  item.addEventListener('click', handleClick);
});

// ✅ Good — one listener handles all items, including dynamically added ones
document.querySelector('.list').addEventListener('click', (e) => {
  const item = e.target.closest('.item');
  if (item) handleClick(item);
});
```

**Benefits:** Works for dynamically added elements, reduces memory usage, simpler code.

---

### Q54. What is the difference between `innerHTML`, `textContent`, and `innerText`? `[Intermediate]`

**Answer:**

| Property | Parses HTML | Safe from XSS | Triggers Reflow |
|----------|------------|---------------|-----------------|
| `innerHTML` | ✅ Yes | ❌ No | Partial |
| `textContent` | ❌ No | ✅ Yes | No |
| `innerText` | ❌ No | ✅ Yes | ✅ Yes (slow) |

> 💡 **Use `textContent` for text-only operations.** Use `DOMPurify.sanitize()` + `innerHTML` when HTML from user input is required. The emerging **Sanitizer API** will offer a native browser alternative to DOMPurify.

---

### Q55. What is the Fetch API? `[Intermediate]`

**Answer:**

Modern Promise-based HTTP client, replacing the older callback-based `XMLHttpRequest`.

```javascript
// ✅ Modern fetch with async/await
async function getData(url) {
  const response = await fetch(url);

  // ⚠️ Fetch doesn't reject on HTTP errors — check response.ok
  if (!response.ok) {
    throw new Error(`HTTP error: ${response.status}`);
  }

  return response.json();
}
```

---

### Q56. What are Web Components? `[Advanced]`

**Answer:**

Browser-native APIs for creating reusable, encapsulated custom HTML elements.

```javascript
class MyCard extends HTMLElement {
  constructor() {
    super();
    // Shadow DOM provides true CSS encapsulation
    const shadow = this.attachShadow({ mode: 'open' });
    shadow.innerHTML = `
      <style>h2 { color: navy; }</style>
      <h2><slot name="title"></slot></h2>
      <slot></slot>
    `;
  }
}

customElements.define('my-card', MyCard);
```

```html
<my-card>
  <span slot="title">Card Title</span>
  <p>Card content here</p>
</my-card>
```

> In 2026: **Declarative Shadow DOM** (`<template shadowrootmode="open">`) allows Web Components to be server-rendered without JavaScript, finally giving them a proper SSR story. Web Components power Adobe Spectrum, Google Material Web, and Salesforce Lightning design systems.

---

### Q57. What is `scheduler.yield()` and why does it matter for INP? `[2026 Trend]`

**Answer:**

`scheduler.yield()` is a new browser API that allows JavaScript to yield control back to the browser mid-task, giving it a chance to process user interactions before resuming. This directly improves **INP** (Interaction to Next Paint) by breaking long tasks into smaller chunks without the awkward `setTimeout(fn, 0)` hack.

```javascript
async function processLargeData(items) {
  for (let i = 0; i < items.length; i++) {
    processItem(items[i]);

    // Every 50 items, yield to the browser for better responsiveness
    if (i % 50 === 0) {
      await scheduler.yield();
    }
  }
}
```

Available in Chrome/Edge. A `scheduler.postTask()` API also exists for prioritized task scheduling.

---

## ✅ Section 11: Best Practices & Code Quality

---

### Q58. What is progressive enhancement? `[Intermediate]`

**Answer:**

Start with a solid, accessible HTML baseline that works everywhere, then layer CSS and JS on top.

```
HTML baseline (works everywhere)
  ↓
+ CSS (visual enhancement)
  ↓
+ JavaScript (interactive enhancement)
  ↓
+ Modern APIs (cutting-edge enhancement with @supports / feature detection)
```

> More robust than graceful degradation — the page works even if CSS or JS fails to load.

---

### Q59. How do you structure HTML for SEO? `[Intermediate]`

**Answer:**

- One `<h1>` per page describing the main topic
- Logical heading hierarchy (`h1 → h2 → h3`, never skip levels)
- Descriptive `<title>` (50–60 chars) and `meta description`
- Semantic HTML (search engines read structure, not just divs)
- Image `alt` text
- Canonical URLs (`<link rel="canonical">`)
- Structured data (JSON-LD schema.org markup)
- Fast load times (Core Web Vitals — especially LCP and INP)

---

### Q60. What are the most common HTML mistakes to avoid? `[Beginner]`

**Answer:**

| ❌ Anti-pattern | ✅ Fix |
|----------------|--------|
| Missing `alt` on images | Always add meaningful alt text |
| `<div>` for everything | Use semantic elements |
| Missing `<label>` on inputs | Associate every input with a label |
| `<table>` for page layout | Use CSS Grid/Flexbox |
| Inline styles everywhere | Use external stylesheets |
| Missing viewport meta tag | Add `<meta name="viewport">` |
| No `width`/`height` on images | Add dimensions to prevent CLS |
| Multiple `<h1>` elements | One `<h1>` per page |
| Links that say "click here" | Use descriptive link text |
| `outline: none` on focus | Keep visible focus styles (use `:focus-visible`) |
| `target="_blank"` without `rel="noopener"` | Browsers now apply this automatically (Chrome 2021+) but add it for older targets |

---

### Q61. What should every HTML `<head>` contain? `[Beginner]`

**Answer:**

```html
<head>
  <!-- Required -->
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Descriptive Page Title (50-60 chars) | Site Name</title>

  <!-- SEO -->
  <meta name="description" content="Concise description under 160 chars.">
  <link rel="canonical" href="https://example.com/page">

  <!-- Social sharing (Open Graph) -->
  <meta property="og:title" content="Page Title">
  <meta property="og:description" content="Description">
  <meta property="og:image" content="https://example.com/preview.jpg">

  <!-- Performance -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preload" href="hero.jpg" as="image" fetchpriority="high">

  <!-- Styles -->
  <link rel="stylesheet" href="styles.css">
</head>
```

---

## 📊 Quick Reference: Semantic Elements

| Element | Purpose | Use When |
|---------|---------|----------|
| `<header>` | Page/section header | Top of page or section with logo, nav, title |
| `<nav>` | Navigation links | Main nav menus, breadcrumbs, pagination |
| `<main>` | Primary content | **One per page** — central content area |
| `<article>` | Self-contained piece | Blog post, news item, comment, widget |
| `<section>` | Thematic grouping | Groups related content with a heading |
| `<aside>` | Tangential content | Sidebars, pull quotes, related links |
| `<footer>` | Footer info | Copyright, contact, secondary links |
| `<figure>` | Media + caption | Images, diagrams, code examples with labels |
| `<figcaption>` | Caption for figure | Description inside a `<figure>` |
| `<time>` | Date or time | Publishing dates, event times (machine-readable) |
| `<dialog>` | Modal/dialog | Alerts, confirmations, forms in overlays |
| `<details>` | Disclosure widget | FAQs, expandable sections — no JS needed |
| `<summary>` | Toggle for details | Visible label for a `<details>` widget |
| `<mark>` | Highlighted text | Search result highlights, key terms |
| `<address>` | Contact info | Author or organization contact details |
| `<search>` | Search landmark ✨ | Wraps search forms — new semantic element; replaces `role="search"` |

---

## 📚 Resources

### Official References
- [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/HTML) — definitive HTML/CSS/JS reference
- [HTML Living Standard](https://html.spec.whatwg.org/) — the actual specification
- [Can I Use](https://caniuse.com/) — browser support tables for every feature
- [web.dev Baseline](https://web.dev/baseline) — cross-browser feature readiness signal

### Performance & Best Practices
- [web.dev](https://web.dev/) by Google — performance, accessibility, modern web
- [Chrome DevTools](https://developer.chrome.com/docs/devtools/) — learn Performance, Accessibility, and Network panels
- [Lighthouse](https://developer.chrome.com/docs/lighthouse/) — automated audits built into DevTools
- [Chrome for Developers Blog](https://developer.chrome.com/blog) — new web UI features including I/O 2025 recap

### Accessibility
- [WebAIM](https://webaim.org/) — accessibility testing tools and learning
- [WCAG 2.2](https://www.w3.org/TR/WCAG22/) — the official guidelines
- [a11y Project](https://www.a11yproject.com/) — practical accessibility patterns

### Security
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — top web security risks
- [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) — MDN CSP reference

### What to Watch (2026 Horizon)
- **Interop 2026** — annual cross-browser initiative to land highly-requested features; results announced early 2026
- **WCAG 3.0** — graduated scoring model in development; not yet finalized
- **Sanitizer API** — native browser alternative to DOMPurify for safe HTML insertion
- **Interest Invoker API** — CSS-native hover-delay for tooltips (complements Anchor Positioning + Popover)
- **`<model>` element** — embeds 3D models (AR/VR) natively in HTML
- **Customizable `<select>` widespread adoption** — the first native form control in years to be fully styleable

---

<p align="center">
  <strong>Good luck with your interviews! 🚀</strong><br>
  <em>Understand the browser deeply, not just the syntax — that's what separates great engineers.</em>
</p>
