# blog.moldtraining.us

Blog for [moldtraining.us](https://moldtraining.us). Static HTML/CSS/JS, no build step, deployed to GitHub Pages.

## Structure

```
index.html            — blog home (post list, search, sort, pagination)
assets/css/style.css   — all styling
assets/js/main.js      — home page post list rendering (search/sort/pagination)
assets/img/pmmt-logo.png — site logo / favicon
assets/img/posts/      — featured images for posts
posts/                 — generated post pages (one .html per post)
posts.json             — generated post index consumed by assets/js/main.js
scripts/convert.js     — converts an uploaded document into a post
uploads/               — drop a .docx/.txt/.zip here to publish a new post
```

## Publishing a new post

Add a file to the `uploads/` folder on the `main` branch (via the GitHub web UI's "Add file" or a normal commit/push) in one of these formats:

- **`.docx`** — a Word document. The first heading or line becomes the post title; the rest becomes the body.
- **`.txt`** — plain text, same rule for the title.
- **`.zip`** — a zip containing one `.docx`/`.txt` plus (optionally) one image file (`.jpg`/`.png`/`.webp`/`.gif`) to use as the post's featured image.

Pushing to `uploads/*.docx`, `uploads/*.txt`, or `uploads/*.zip` triggers the **Convert Word Docs to Blog Posts** GitHub Action, which:

1. Converts the document into `posts/<slug>.html` using the site's styling.
2. Saves any featured image into `assets/img/posts/`.
3. Adds an entry to `posts.json`.
4. Moves the original file into `uploads/processed/`.
5. Commits and pushes the result back to `main`.

That push then triggers the **Deploy Blog to GitHub Pages** workflow, which republishes the whole site automatically. No manual steps beyond uploading the document.

### Formatting notes

The converter recognizes a few common shapes so posts don't need manual cleanup:

- Real Word headings/bold/lists/tables convert directly.
- Plain paragraphs written in ALL-CAPS-style "fake bold" Unicode (e.g. text copied from an AI tool) are treated as section headings.
- Lines starting with `•`, `-`, or `*` become bullet lists.
- A "Sources" or "References" heading followed by a name and a URL on the next line becomes a linked source list.

## Local preview

```bash
npx serve .
```

Or open `index.html` directly in a browser (post pages/`posts.json` will load correctly since there's no build step).

To test document conversion locally:

```bash
npm install
cp your-post.docx uploads/
npm run convert
```

## Deployment

Deployed via GitHub Pages using the Actions-based deployment (`.github/workflows/deploy.yml`) — every push to `main` republishes the site. A `CNAME` file at the repo root is set to `blog.moldtraining.us` so the custom domain keeps working once DNS is pointed here (see below).

### Custom domain setup (one-time)

1. In the repo's **Settings → Pages**, confirm the custom domain shows `blog.moldtraining.us` (it's picked up automatically from the `CNAME` file after the first deploy).
2. At the DNS provider for `moldtraining.us`, add a `CNAME` record:
   - Host: `blog`
   - Value: `<github-username-or-org>.github.io`
3. Wait for DNS to propagate, then enable **Enforce HTTPS** in the Pages settings once GitHub issues the certificate.
