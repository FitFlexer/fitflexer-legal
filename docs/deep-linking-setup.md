# Deep Linking Setup Guide

## Apple Developer Setup Required

1. **Add Associated Domains capability** to the Xcode project (`FitFlexer.xcodeproj`).
2. Add the entitlement: `applinks:fitflexer.com`
3. AASA `appIDs` is set to `4H7C7Q6529.Flex-Group.FitFlexer` (Team ID + the app's real bundle ID, `Flex-Group.FitFlexer`; the app is not `com.fitflexer.app`).
4. **Replace the App Store URL placeholder** (`id_PLACEHOLDER`) in `invite/index.html` with the actual App Store listing URL once the app is published.

## Current hosting note (2026-09-02)

This site is served at `https://fitflexer.github.io/fitflexer-legal/` (the repo moved to the FitFlexer org; the old `kylemathew2003.github.io` URL 404s). `fitflexer.com` has **no DNS records** yet, so neither the invite fallback page nor the AASA file is reachable at the domain the backend mints (`https://fitflexer.com/invite/{token}`). The App Store provisioning profile also lacks the Associated Domains capability — enable it on the App ID and regenerate before adding the entitlement.

## Domain Configuration Required

- `fitflexer.com` must serve the AASA file at `/.well-known/apple-app-site-association`
- The response `Content-Type` must be `application/json`
- HTTPS is required (no HTTP fallback)
- The file must NOT have a `.json` extension

### GitHub Pages Limitation

GitHub Pages does **not** serve dotfiles/dot-directories by default. The `/.well-known/` directory will not be accessible on `kylemathew2003.github.io/fitflexer-legal`.

**Options to work around this:**
- Use **CloudFront + S3** to host the AASA file at `fitflexer.com/.well-known/apple-app-site-association`
- Use a **Cloudflare Worker** or **AWS Lambda@Edge** to intercept and serve the file
- Use a **custom domain** with a hosting provider that supports `.well-known` (e.g., Netlify, Vercel)
- If `fitflexer.com` points to an API Gateway or load balancer, add a route to serve the AASA file directly

## Backend Environment Variables

| Variable | Description | Example |
|---|---|---|
| `INVITE_LINKS_TABLE` | DynamoDB table name for invite tokens | `prod-fitflexer-invite-links` |
| `INVITE_BASE_URL` | Base URL for generated invite links | `https://fitflexer.com/invite` |

## Infrastructure TODO

- [ ] Create DynamoDB table for invite links (PK: token, TTL on `ttl` attribute)
- [ ] Add `INVITE_LINKS_TABLE` env var to game-service Lambda
- [ ] Add `INVITE_BASE_URL` env var to game-service Lambda
- [ ] Add API Gateway route for public invite preview endpoint (no JWT auth required)
- [x] Replace `TEAM_ID` in AASA file with actual Apple Developer Team ID (2026-09-02)
- [ ] Replace `id_PLACEHOLDER` in invite fallback page with actual App Store URL
- [ ] Configure domain to serve `.well-known/apple-app-site-association` over HTTPS
