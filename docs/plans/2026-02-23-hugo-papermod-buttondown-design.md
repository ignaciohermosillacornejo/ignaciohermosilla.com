# Hugo + PaperMod + Buttondown Blog Design

## Overview

Replace the current Jekyll site at ignaciohermosilla.com with Hugo + PaperMod theme, adding Buttondown email subscriptions on the free tier.

## Architecture

- **Hugo + PaperMod** -- static site generator with clean, fast theme
- **Buttondown embed form** -- footer subscribe form, collects subscribers (free tier)
- **Buttondown MCP server** -- Claude creates drafts and sends newsletters via API (replaces $9/month RSS-to-email)
- **GitHub Actions** -- auto-builds and deploys on push to main
- **Same repo** -- ignaciohermosillacornejo/ignaciohermosilla.com, same domain

## Site Config

| Setting | Value |
|---------|-------|
| Title | Ignacio Hermosilla |
| Description | Software Engineer |
| Domain | ignaciohermosilla.com |
| Theme | auto (dark/light toggle) |
| Welcome text | Welcome. I write about software, immigration, and life in the US. |
| Buttondown username | ignaciohermosilla |

## Social Links

- GitHub: https://github.com/ignaciohermosillacornejo
- X: https://x.com/ignacio_hermosi
- LinkedIn: https://www.linkedin.com/in/ignaciohermosilla/
- Threads: https://www.threads.com/@ignacio_hermosilla
- RSS: /index.xml

## Pages

- Homepage -- "home-info" mode with welcome text and social icons
- Archive -- list of all posts at /archives/
- Search -- Fuse.js search at /search/
- Tags -- auto-generated tag pages at /tags/

## Newsletter Workflow

1. Embed subscribe form in footer (free tier, no JS)
2. Configure buttondown-mcp server in Claude Code settings
3. User publishes post and asks Claude to send newsletter
4. Claude creates Buttondown draft, user reviews and sends

## Files to Create

1. hugo.yaml -- main config
2. .gitignore -- Hugo build artifacts
3. .github/workflows/hugo.yaml -- deploy workflow
4. content/archives.md -- archive page
5. content/search.md -- search page
6. content/posts/hello-world.md -- example first post
7. archetypes/default.md -- post template
8. layouts/partials/extend_footer.html -- Buttondown form
9. assets/css/extended/custom.css -- form styling
10. static/CNAME -- custom domain

## What Gets Deleted

All Jekyll files: _config.yml, _includes/, _layouts/, _posts/, _sass/, style.scss, index.html, 404.md, about.md, images/

## Deployment

- Push to new branch, create PR against main
- After merge, GitHub Actions builds and deploys
- Manual step: change GitHub Pages source to "GitHub Actions" in repo settings
