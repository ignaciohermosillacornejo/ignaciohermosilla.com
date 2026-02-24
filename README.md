# ignaciohermosilla.com

Personal blog built with [Hugo](https://gohugo.io/) and the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme. Deployed to [GitHub Pages](https://pages.github.com/).

## Local development

```bash
# Install Hugo (macOS)
brew install hugo

# Clone with submodules
git clone --recurse-submodules git@github.com:ignaciohermosillacornejo/ignaciohermosilla.com.git
cd ignaciohermosilla.com

# Serve locally with drafts
hugo server -D
```

Site runs at http://localhost:1313/

## Writing a new post

```bash
hugo new posts/my-post-title.md
```

Edit `content/posts/my-post-title.md`, set `draft: false` when ready, then push to `main`.

## Deployment

Pushes to `main` trigger a GitHub Actions workflow that builds and deploys to GitHub Pages automatically.
