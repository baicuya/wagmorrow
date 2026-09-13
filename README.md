# Wagmorrow public site

Static English-first marketing, privacy, and support pages for the first iOS release.

## Local preview

From the repository root:

```sh
python3 -m http.server 4173 --directory website
```

Then open `http://127.0.0.1:4173/`, `/privacy/`, and `/support/`.

## Deployment

The production server is available through the SSH alias `jxd-aliyun-ecs-root`.
Stage the current site without changing any active Caddy routes:

```sh
./scripts/deploy_public_site.sh
```

This syncs the static files to `/srv/wagmorrow/site`. After the domain points to
the server, add a dedicated Caddy site block:

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

The site can be copied to any static host. Before the App Store build is uploaded:

1. Point the chosen HTTPS domain at the static host.
2. Confirm `/privacy/` and `/support/` are public without authentication.
3. Set `WAGMORROW_PUBLIC_ORIGIN` in `project.yml` to the final `https://` origin and regenerate the Xcode project.
4. Add the privacy, support, and marketing URLs to App Store Connect.
5. Rebuild and test every in-app link.

No application server, database, secrets, or build step is required for this version.
