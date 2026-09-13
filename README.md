# Wagmorrow public site

Static English-first marketing, privacy, and support pages for the first iOS release.

## Local preview

From the repository root:

```sh
python3 -m http.server 4173 --directory website
```

Then open `http://127.0.0.1:4173/`, `/privacy.html`, `/terms.html`, and `/support.html`.

## GitHub Pages deployment

The production site is published from the public repository
[`baicuya/wagmorrow`](https://github.com/baicuya/wagmorrow) with GitHub Pages:

- Home: `https://baicuya.github.io/wagmorrow/`
- Privacy: `https://baicuya.github.io/wagmorrow/privacy.html`
- Terms: `https://baicuya.github.io/wagmorrow/terms.html`
- Support: `https://baicuya.github.io/wagmorrow/support.html`

Publish the current `website/` directory with:

```sh
./scripts/deploy_github_pages.sh
```

The app reads the root address from `WAGMORROW_PUBLIC_ORIGIN` in `project.yml`.
GitHub Pages is the production host for the free first release, so no custom
domain, application server, database, secrets, or site build step is required.

## Optional server mirror

The production server is available through the SSH alias `jxd-aliyun-ecs-root`.
Stage the current site without changing any active Caddy routes:

```sh
./scripts/deploy_public_site.sh
```

This syncs the static files to `/srv/wagmorrow/site`. If a custom domain is added
later, add a dedicated Caddy site block:

```caddyfile
YOUR_DOMAIN {
    root * /srv/wagmorrow/site
    encode zstd gzip
    file_server

    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "SAMEORIGIN"
        Referrer-Policy "strict-origin-when-cross-origin"
        Permissions-Policy "camera=(), microphone=(), geolocation=()"
        -Server
    }
}
```

The server mirror is not part of the current App Store release path.
