# How to publish a new article on Launch Wire

Playbook for adding a new published-campaign article to this repo. Attach this
file when asking an agent to publish a new article — it has everything needed
to do it correctly in one pass, without re-deriving conventions from scratch.

## Inputs to collect first

- The article content (usually a markdown draft — press release, listicle,
  comparison post, etc).
- A cover image, usually given as a URL (e.g. `https://r2.seomode.co/img/...`).
- Optionally a `report.pdf` — sometimes provided separately/after the fact.
- Client name, category (e.g. Finance, Education), and the month/year to show
  as "Last updated".

## Steps

1. **Pick a slug.** Kebab-case, descriptive (brand + topic), no build tooling
   involved — the folder name *is* the URL path.
   `published/<slug>/`

2. **Create the folder and add assets.**
   ```
   mkdir -p published/<slug>
   curl -sL -o published/<slug>/cover.<ext> "<image-url>"
   ```
   Keep whatever format the source image is in (`.webp`, `.png`, etc) — don't
   transcode. If a PDF report is provided, save it as
   `published/<slug>/report.pdf` (only add the PDF download button in the HTML
   if this file actually exists).

3. **Write `published/<slug>/index.html`** using the template below. Use an
   existing article (e.g. `published/best-ai-courses-for-kids-2026/index.html`)
   as a live reference if anything is ambiguous.

4. **Verify.** Local `file://` preview will NOT load the CSS — the stylesheet
   links are root-absolute (`/styles.css`) so they resolve incorrectly under
   `file://`. This is expected, not a bug. To actually see styling, either run
   a local static server from the repo root (`npx live-server`) or check the
   page on the production domain after pushing.

5. **Commit and push to `main`** (this repo has no `master` branch).
   ```
   git add published/<slug>/
   git commit -m "Add <slug> article"
   git push origin main
   ```

6. **Do not list it anywhere.** `published/index.html` is intentionally a
   private "no access" gate page (see commit "Hide published campaigns
   listing behind a private notice") — individual articles are unlisted,
   direct-link-only. Don't add the new article to a sitemap, nav, or the
   published index.

## HTML template

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{HEADLINE}}</title>
<meta name="description" content="{{ONE_TO_TWO_SENTENCE_SUMMARY}}">
<link rel="icon" type="image/svg+xml" href="/favicon.svg">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Fraunces:opsz,wght@9..144,500;9..144,600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="/styles.css">
<link rel="stylesheet" href="/published/article.css">
</head>
<body>

<header class="pub-header">
  <div class="container pub-header-inner">
    <a href="/" class="logo">
      <span class="logo-mark" aria-hidden="true"></span>
      Launch Wire
    </a>
    <a href="/published/" class="pub-back">&larr; Published campaigns</a>
  </div>
</header>

<main>
  <section class="article-hero">
    <div class="container">
      <p class="pub-badge">Published via Launch Wire</p>
      <h1>{{HEADLINE}}</h1>
      <div class="article-meta">
        <span>Client: {{CLIENT}}</span>
        <span>Category: {{CATEGORY}}</span>
        <span>Last updated: {{MONTH_YEAR}}</span>
      </div>
      <!-- Only include this line if report.pdf exists in the folder -->
      <a href="report.pdf" target="_blank" rel="noopener" class="btn btn-primary btn-small pdf-download">Download PDF report &#8599;</a>
    </div>
  </section>

  <article class="article-body">
    <div class="article-cover" style="margin: 0 0 48px; text-align: center;">
      <img src="cover.{{ext}}" alt="{{HEADLINE}}" width="{{W}}" height="{{H}}" loading="eager" style="width: 100%; max-width: 480px; height: auto; display: inline-block; border-radius: 14px; border: 1px solid #E4E1D9;">
    </div>

    <!-- Article body content goes here: convert the source markdown to HTML.
         Use h2/h3 for headings, p for paragraphs, ul/ol for lists.
         Wrap comparison tables in <div class="table-wrap"><table>...</table></div>.
         For ranked list items with pros/cons, use:
         <div class="pros-cons">
           <div><h4>Pros</h4><ul>...</ul></div>
           <div><h4>Cons</h4><ul>...</ul></div>
         </div>
    -->

    <div class="article-cta" style="margin: 48px 0; padding: 28px; border-radius: 14px; background: #F3F1EC; border: 1px solid #E4E1D9; text-align: center;">
      <p style="margin: 0 0 16px; color: #4A5160;">Want coverage like this distributed for your brand?</p>
      <a href="/#pricing" class="btn btn-primary" style="display: inline-block; background: #14181F; color: #ffffff !important; text-decoration: none !important; padding: 14px 26px; border-radius: 999px; font-weight: 600; font-family: Inter, -apple-system, sans-serif;">Distribute Your Release — $999</a>
    </div>

  </article>
</main>

<footer class="site-footer">
  <div class="container footer-inner">
    <div class="footer-brand">
      <a href="/" class="logo">
        <span class="logo-mark" aria-hidden="true"></span>
        Launch Wire
      </a>
      <p>Press release distribution for search, sentiment, and AI answers.</p>
    </div>
    <nav class="footer-nav" aria-label="Footer">
      <a href="/#how-it-works">How it works</a>
      <a href="/#why">Why Launch Wire</a>
      <a href="/#pricing">Pricing</a>
      <a href="/#faq">FAQ</a>
      <a href="mailto:hi@launchwire.org">Contact</a>
    </nav>
    <p class="footer-copy">© 2026 Launch Wire. All rights reserved.</p>
  </div>
</footer>

</body>
</html>
```

## Gotchas learned the hard way

- **Stylesheet paths must be root-absolute** (`/styles.css`,
  `/published/article.css`), never relative (`styles.css`). Since every
  article lives one directory deeper than the site root, a relative path
  resolves to the wrong location and 404s in production.
- **Keep the inline `style="..."` fallbacks** on the cover image and CTA
  button shown in the template above, even though `article.css` also styles
  them. Past commits (`Inline-style the CTA button as a guaranteed fallback`,
  `Constrain article cover image width`) added these specifically because the
  class-only styling wasn't reliably winning in production — don't strip them
  out for being "redundant."
- **The PDF button is conditional.** Only add the
  `<a href="report.pdf" ...>` line if `report.pdf` actually exists in the
  article's folder. If it's added later, just insert that one line — no other
  changes needed.
- **Image dimensions**: set `width`/`height` on the `<img>` to the source
  image's actual pixel dimensions (avoids layout shift); the `max-width: 480px`
  inline style controls the real display size regardless.
