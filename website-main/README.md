# Vaibhav Kodiyan

Personal portfolio site. Plain, semantic HTML/CSS — no build step, no JavaScript required to
read the content, deploys to GitHub Pages on push to `main`.

## Structure

```
site/
  index.html     the whole site (one page: Home / Work / About / Contact)
  styles.css     one stylesheet, one accent color, an 8px spacing scale
  favicon.png
  robots.txt
  sitemap.xml
  resume.pdf     not included — see TODO below
```

## Before you deploy

- [ ] **Add `site/resume.pdf`.** The nav and Contact section both link to `resume.pdf`; that
  link 404s until the file exists. Drop a real PDF resume in `site/` with that exact filename.
- [ ] **Confirm the canonical URL.** `site/index.html`, `robots.txt`, and `sitemap.xml` all
  assume this repo deploys to `https://vaibhav-kodiyan.github.io/vaibhav-kodiyan/` (a project
  page under the current repo name). If you rename the repo to `vaibhav-kodiyan.github.io` or
  attach a custom domain, update the `canonical`/`og:*` tags in `index.html` and the URLs in
  `robots.txt` and `sitemap.xml` to match.
- [ ] Optional: replace `og:image` with a dedicated 1200×630 social preview image — it
  currently points at the favicon, which works but isn't sized for link previews.

## Deployment

GitHub Actions (`.github/workflows/static.yml`) copies everything in `site/` to Pages on every
push to `main`. No build step — it's a straight copy.

## Local preview

```bash
cd site && python3 -m http.server 8000
```
