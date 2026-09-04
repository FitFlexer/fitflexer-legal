# fitflexer-legal

The public site at **https://fitflexer.com** — landing page plus the legal
documents.

| File | URL |
|---|---|
| `index.html` | https://fitflexer.com/ |
| `privacy-policy.html` | https://fitflexer.com/privacy-policy.html |
| `terms-of-service.html` | https://fitflexer.com/terms-of-service.html |
| `eula.html` | https://fitflexer.com/eula.html |

## How it ships

Push to `main` → `.github/workflows/deploy.yml` → OIDC into the prod AWS account
→ `aws s3 sync` to `fitflexer-prod-website` → CloudFront invalidation → the
workflow curls every page and fails if any is not 200.

There is no manual step. Editing a page and merging is the whole process.

## What this repo does NOT own

Infrastructure lives in **FitFlexerInfra** (`modules/static-site`,
`stacks/shared/website`): the S3 bucket, CloudFront, WAF, ACM certificate, DNS,
cache policy and security headers.

Two paths on the site are also Terraform's, because they are generated from
infrastructure variables:

- `/.well-known/apple-app-site-association` — Universal Links, built from the
  app's Team ID + bundle ID
- `/invite/index.html` — invite fallback, built from the App Store URL

They are deliberately **not** in this repo. The deploy excludes both prefixes,
and the deploy role is explicitly denied write access to them in IAM, so a
mistyped `--exclude` cannot delete them. If you need to change either one,
change it in FitFlexerInfra.

## Hosting

Served from AWS, not GitHub Pages. Pages was retired because Google rejects
`github.io` as a privacy-policy host for OAuth consent-screen verification, and
because the branding domain has to be one we own.

## Local preview

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Assets: `assets/logo-mark.png` is the app mark with its background removed;
`logo.svg` is the older wordmark still used by the legal pages' headers.
