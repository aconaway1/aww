# Aaron's Worthless Words

Personal technical blog, built with [Hugo](https://gohugo.io/) and the [hugo-primer-blog](https://go.ngs.io/hugo-primer-blog) theme. Deployed via [Cloudflare Pages](https://pages.cloudflare.com/).

## Development

```bash
hugo server
```

The site is available at http://localhost:1313.

## Deployment

Pushing to `main` triggers an automatic build and deploy on Cloudflare Pages.

## Structure

```
content/posts/YYYY/MM/slug/   # Blog posts (page bundles)
content/pages/                # Static pages
static/                       # Static assets (favicon, etc.)
hugo.toml                     # Site configuration
```
