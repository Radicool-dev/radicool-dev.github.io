## Stack

- [Hugo](https://gohugo.io) — static site generator
- [LofiCode](https://github.com/madebydia/loficode-hugo-theme) — theme, with local overrides
- GitHub Pages — hosting, deployed automatically via GitHub Actions

## Running locally

First you need to install hugo:

```bash
sudo apt install hugo
```

Then:

```bash
git clone https://github.com/Radicool-dev/radicool-dev.github.io.git
cd radicool-dev.github.io
hugo server -D
```

The site is served at http://localhost:1313 with live reload. The `-D` flag includes posts marked `draft: true`.

## Deployment

Pushing to `main` triggers the workflow in `.github/workflows/`, which builds the site with Hugo and publishes it to GitHub Pages. The generated `public/` directory is not committed.
