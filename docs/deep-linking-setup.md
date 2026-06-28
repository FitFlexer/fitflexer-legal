# Deep Linking Setup Guide

## Apple Developer Setup Required

1. **Add Associated Domains capability** to the Xcode project (`FitFlexer.xcodeproj`).
2. Add the entitlement: `applinks:fitflexer.com`
3. **Replace `TEAM_ID`** in `.well-known/apple-app-site-association` with your actual Apple Developer Team ID.
   - Find it at: https://developer.apple.com/account → Membership Details → Team ID
   - The `appIDs` value should be: `<TEAM_ID>.com.fitflexer.app`
4. **Replace the App Store URL placeholder** (`id_PLACEHOLDER`) in `invite/index.html` with the actual App Store listing URL once the app is published.

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
- [ ] Replace `TEAM_ID` in AASA file with actual Apple Developer Team ID
- [ ] Replace `id_PLACEHOLDER` in invite fallback page with actual App Store URL
- [ ] Configure domain to serve `.well-known/apple-app-site-association` over HTTPS
