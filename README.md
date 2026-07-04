# anupiyan.github.io

Personal homepage + blog, built with Jekyll for GitHub Pages.

## What's in here

```
_config.yml         site settings (title, description, permalink structure)
_layouts/
  default.html       shared shell: nav, <head>, footer — wraps every page
  post.html           layout for individual blog posts
_posts/
  2026-07-03-welcome.md   starter blog post
assets/
  css/style.css       all site styling
  img/profile.jpg      your hero photo
  img/avatar.jpg       square-cropped version (spare, for future use e.g. favicon/author avatar)
index.html            homepage (hero, about, focus areas, experience, awards, blog teaser)
blog.html             blog listing page (lists every post in _posts/)
```

## Before you push — update these

In `_layouts/default.html`, swap the placeholder links for your real ones:

```html
<a href="https://scholar.google.com" ...>Google Scholar</a>
<a href="https://github.com/anupiyan" ...>GitHub</a>
<a href="https://linkedin.com" ...>LinkedIn</a>
<a href="mailto:hello@anupiyan.dev">Email</a>
```

## How to publish

1. Copy every file/folder in this package into the root of your existing
   `anupiyan.github.io` repo, replacing what's there now.
2. Commit and push:
   ```
   git add .
   git commit -m "Redesign homepage, add blog"
   git push
   ```
3. GitHub builds Jekyll automatically — no build step on your end. Give it
   a minute or two, then check `https://anupiyan.github.io/`.

## Writing a new blog post

Add a new file to `_posts/` named `YYYY-MM-DD-your-title.md`:

```markdown
---
title: "Your Post Title"
date: 2026-08-01
excerpt: "One sentence used as the preview text on the homepage and blog listing."
---

Your content here, in Markdown.
```

That's it — it'll automatically show up on `/blog/` and in the "Latest from
the blog" section on the homepage (which shows the 3 most recent posts).

## Local preview (optional)

If you want to preview changes before pushing:

```
gem install bundler jekyll
bundle init
bundle add jekyll
bundle exec jekyll serve
```

Then open `http://localhost:4000`.
