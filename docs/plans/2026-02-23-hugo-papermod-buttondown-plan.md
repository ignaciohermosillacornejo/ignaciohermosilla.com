# Hugo + PaperMod + Buttondown Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Replace the Jekyll site at ignaciohermosilla.com with Hugo + PaperMod, adding Buttondown email subscriptions on the free tier.

**Architecture:** Hugo static site generator with PaperMod theme (git submodule). Buttondown embed form in footer for subscriber collection. GitHub Actions for CI/CD to GitHub Pages. Newsletter sending via Buttondown MCP server (free tier API, no RSS-to-email subscription needed).

**Tech Stack:** Hugo (extended), PaperMod theme, Buttondown API, GitHub Actions, GitHub Pages

**Working directory:** `/Users/nach/Projects/ignaciohermosilla.com/.claude/worktrees/cozy-finding-pearl`

**Design doc:** `docs/plans/2026-02-23-hugo-papermod-buttondown-design.md`

---

### Task 1: Install Hugo

**Step 1: Install Hugo via Homebrew**

Run: `brew install hugo`

**Step 2: Verify Hugo installation**

Run: `hugo version`
Expected: Output contains `extended` (e.g., `hugo v0.143.x+extended`)

**Step 3: Commit** — nothing to commit, system dependency

---

### Task 2: Remove Jekyll files

**Files to delete:**
- `_config.yml`
- `_includes/` (directory)
- `_layouts/` (directory)
- `_posts/` (directory)
- `_sass/` (directory)
- `style.scss`
- `index.html`
- `404.md`
- `about.md`
- `images/` (directory)
- `CNAME` (will be recreated at `static/CNAME`)
- `README.md` (Jekyll-specific readme)
- `LICENSE` (will keep if user wants, but it's a Jekyll-Now license)

**Keep:**
- `.git`
- `.claude/`
- `docs/` (our design doc)

**Step 1: Delete all Jekyll files and directories**

```bash
rm -rf _config.yml _includes/ _layouts/ _posts/ _sass/ style.scss index.html 404.md about.md images/ CNAME README.md LICENSE
```

**Step 2: Verify only .git, .claude, docs remain**

Run: `ls -la`
Expected: Only `.git`, `.claude/`, `docs/`, `.gitignore` remain

**Step 3: Commit**

```bash
git add -A
git commit -m "chore: remove Jekyll site files"
```

---

### Task 3: Create Hugo directory structure and main config

Since we can't use `hugo new site` in a non-empty directory, create the structure manually.

**Files to create:**
- `hugo.yaml`
- `archetypes/default.md`

**Directories to create:**
- `archetypes/`
- `assets/`
- `content/`
- `data/`
- `i18n/`
- `layouts/`
- `static/`
- `themes/`

**Step 1: Create Hugo directories**

```bash
mkdir -p archetypes assets/css/extended content/posts data i18n layouts/partials static themes
```

**Step 2: Create `hugo.yaml`**

```yaml
baseURL: "https://ignaciohermosilla.com/"
title: "Ignacio Hermosilla"
paginate: 10
theme: PaperMod

enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false
enableEmoji: true

minify:
  disableXML: true
  minifyOutput: true

caches:
  images:
    dir: ":cacheDir/images"

taxonomies:
  category: categories
  tag: tags
  series: series

mainsections: ["posts"]

menu:
  main:
    - identifier: archives
      name: Archive
      url: /archives/
      weight: 10
    - identifier: tags
      name: Tags
      url: /tags/
      weight: 20
    - identifier: search
      name: Search
      url: /search/
      weight: 30

markup:
  highlight:
    codeFences: true
    guessSyntax: true
    lineNos: false
    noClasses: false
    style: dracula
  goldmark:
    renderer:
      unsafe: true

outputs:
  home:
    - HTML
    - RSS
    - JSON

outputFormats:
  RSS:
    mediatype: "application/rss+xml"
    baseName: "index"

params:
  env: production
  title: "Ignacio Hermosilla"
  description: "Software Engineer"
  keywords: [software, engineering, immigration]
  author: "Ignacio Hermosilla"

  DateFormat: "January 2, 2006"

  defaultTheme: auto
  disableThemeToggle: false

  ShowReadingTime: true
  ShowShareButtons: true
  ShowPostNavLinks: true
  ShowBreadCrumbs: true
  ShowCodeCopyButtons: true
  ShowWordCount: false
  ShowRssButtonInSectionTermList: true
  ShowAllPagesInArchive: true
  ShowToc: true
  UseHugoToc: true
  disableSpecial1stPost: false
  disableScrollToTop: false
  comments: false
  hidemeta: false
  hideSummary: false

  homeInfoParams:
    Title: "Welcome"
    Content: "I write about software, immigration, and life in the US."

  socialIcons:
    - name: github
      url: "https://github.com/ignaciohermosillacornejo"
    - name: x
      url: "https://x.com/ignacio_hermosi"
    - name: linkedin
      url: "https://www.linkedin.com/in/ignaciohermosilla/"
    - name: threads
      url: "https://www.threads.com/@ignacio_hermosilla"
    - name: rss
      url: "/index.xml"

  editPost:
    URL: "https://github.com/ignaciohermosillacornejo/ignaciohermosilla.com/tree/main/content"
    Text: "Suggest Changes"
    appendFilePath: true

  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    location: 0
    distance: 1000
    threshold: 0.4
    minMatchCharLength: 0
    keys: ["title", "permalink", "summary", "content"]

  buttondownUsername: "ignaciohermosilla"
```

**Step 3: Create `archetypes/default.md`**

```markdown
---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
draft: true
tags: []
description: ""
showToc: true
TocOpen: false
---
```

**Step 4: Commit**

```bash
git add hugo.yaml archetypes/default.md
git commit -m "feat: add Hugo config and archetypes"
```

---

### Task 4: Add PaperMod theme as git submodule

**Step 1: Add PaperMod submodule**

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

**Step 2: Verify submodule**

Run: `ls themes/PaperMod/theme.toml 2>/dev/null || ls themes/PaperMod/hugo.toml 2>/dev/null || ls themes/PaperMod/config.yml 2>/dev/null || echo "check theme dir" && ls themes/PaperMod/`
Expected: Theme files exist

**Step 3: Commit**

```bash
git add .gitmodules themes/PaperMod
git commit -m "feat: add PaperMod theme as submodule"
```

---

### Task 5: Create content pages

**Files to create:**
- `content/archives.md`
- `content/search.md`
- `content/posts/hello-world.md`

**Step 1: Create `content/archives.md`**

```markdown
---
title: "Archive"
layout: "archives"
url: "/archives/"
summary: "archives"
---
```

**Step 2: Create `content/search.md`**

```markdown
---
title: "Search"
layout: "search"
placeholder: "Search posts..."
---
```

**Step 3: Create `content/posts/hello-world.md`**

```markdown
---
title: "Hello World"
date: 2026-02-23T12:00:00-08:00
draft: false
tags: ["meta"]
description: "First post on this blog."
showToc: false
---

Welcome to my blog. This is the first post.

Here's some code to prove syntax highlighting works:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, world!")
}
```

And some Python:

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

print(greet("world"))
```
```

**Step 4: Commit**

```bash
git add content/
git commit -m "feat: add content pages (archives, search, hello-world post)"
```

---

### Task 6: Create Buttondown subscribe form and custom CSS

**Files to create:**
- `layouts/partials/extend_footer.html`
- `assets/css/extended/custom.css`

**Step 1: Create `layouts/partials/extend_footer.html`**

```html
<!-- Buttondown Newsletter Subscribe Form -->
<div class="newsletter-form">
  <h3>Subscribe to this blog</h3>
  <p>Get new posts delivered to your inbox. No spam.</p>
  <form
    action="https://buttondown.com/api/emails/embed-subscribe/{{ .Site.Params.buttondownUsername }}"
    method="post"
    class="embeddable-buttondown-form"
  >
    <div class="newsletter-input-group">
      <input
        type="email"
        name="email"
        placeholder="your@email.com"
        required
      />
      <input type="hidden" value="1" name="embed" />
      <button type="submit">Subscribe</button>
    </div>
  </form>
</div>
```

**Step 2: Create `assets/css/extended/custom.css`**

```css
/* Newsletter subscribe form */
.newsletter-form {
  max-width: 600px;
  margin: 2rem auto;
  padding: 1.5rem;
  border: 1px solid var(--border);
  border-radius: 8px;
  text-align: center;
}

.newsletter-form h3 {
  margin: 0 0 0.5rem;
  font-size: 1.1rem;
}

.newsletter-form p {
  margin: 0 0 1rem;
  color: var(--secondary);
  font-size: 0.9rem;
}

.newsletter-input-group {
  display: flex;
  gap: 0.5rem;
  justify-content: center;
}

.newsletter-input-group input[type="email"] {
  flex: 1;
  max-width: 300px;
  padding: 0.5rem 0.75rem;
  border: 1px solid var(--border);
  border-radius: 4px;
  font-size: 0.95rem;
  background: var(--theme);
  color: var(--primary);
}

.newsletter-input-group button {
  padding: 0.5rem 1.25rem;
  background: var(--primary);
  color: var(--theme);
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.95rem;
  font-weight: 500;
}

.newsletter-input-group button:hover {
  opacity: 0.85;
}

@media (max-width: 480px) {
  .newsletter-input-group {
    flex-direction: column;
    align-items: stretch;
  }
  .newsletter-input-group input[type="email"] {
    max-width: 100%;
  }
}
```

**Step 3: Commit**

```bash
git add layouts/ assets/
git commit -m "feat: add Buttondown subscribe form and custom CSS"
```

---

### Task 7: Create GitHub Actions workflow and static files

**Files to create:**
- `.github/workflows/hugo.yaml`
- `static/CNAME`
- `.gitignore` (update existing)

**Step 1: Create `.github/workflows/hugo.yaml`**

```yaml
name: Build and deploy

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.143.1
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Install Hugo
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i ${{ runner.temp }}/hugo.deb

      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo build \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

**Step 2: Create `static/CNAME`**

```
ignaciohermosilla.com
```

**Step 3: Update `.gitignore`**

Replace existing `.gitignore` contents with:

```gitignore
# Hugo build output
/public/
/resources/_gen/
/.hugo_build.lock

# OS
.DS_Store
Thumbs.db

# Editor
.vscode/
.idea/
```

**Step 4: Commit**

```bash
git add .github/ static/CNAME .gitignore
git commit -m "feat: add GitHub Actions workflow, CNAME, and .gitignore"
```

---

### Task 8: Verify site builds and runs locally

**Step 1: Build the site**

Run: `hugo build --gc --minify`
Expected: No errors. Output shows pages generated.

**Step 2: Start dev server**

Run: `hugo server -D`
Expected: Server starts at http://localhost:1313/. Check:
- Homepage shows welcome text and social icons
- Dark/light mode toggle works
- Archive page at /archives/
- Search page at /search/
- Hello World post renders with syntax highlighting
- Buttondown form appears in footer
- RSS feed at /index.xml

**Step 3: Stop dev server** (Ctrl+C)

---

### Task 9: Create PR

**Step 1: Push branch to remote**

```bash
git push -u origin worktree-cozy-finding-pearl
```

**Step 2: Create pull request**

```bash
gh pr create \
  --title "Migrate from Jekyll to Hugo + PaperMod" \
  --body "$(cat <<'EOF'
## Summary
- Replace Jekyll site with Hugo + PaperMod theme
- Add Buttondown newsletter subscribe form in footer (free tier)
- Set up GitHub Actions for CI/CD deployment to GitHub Pages
- Add archive, search, and tags pages

## Manual steps after merge
1. Go to repo Settings > Pages > Source > select "GitHub Actions"
2. DNS records should already be configured (domain unchanged)
3. Set up `buttondown-mcp` in Claude Code for newsletter sending

## Test plan
- [ ] Run `hugo server` locally and verify site renders
- [ ] Verify dark/light mode toggle
- [ ] Verify Buttondown form submits
- [ ] After merge, verify GitHub Pages deployment succeeds
- [ ] Verify site loads at ignaciohermosilla.com

Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

---

### Post-deployment manual steps (not part of this plan)

1. **GitHub Pages source:** Go to repo Settings > Pages > Source > select "GitHub Actions"
2. **Buttondown MCP:** Install `buttondown-mcp` server in Claude Code settings with API key
3. **DNS:** Already configured — domain is unchanged, no DNS changes needed
