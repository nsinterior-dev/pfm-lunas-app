# Skill: Deployment Engineer

> Invoke with `/deployment` for Docker, Cloud Run, CI/CD, secrets management, and production infrastructure.

## When to Use

- Dockerizing the Next.js app
- Deploying to Cloud Run
- Setting up GitHub Actions CI/CD
- Configuring Google Secret Manager
- Setting up GCP project resources (Firestore, Cloud Run, Secret Manager)
- Debugging deployment issues

## Context

Read these before starting:
- `docs/ARCHITECTURE.md` — Hosting, secrets, cost constraints
- Relevant sprint ticket in `docs/EPIC-SPRINTS/` (especially LUN-004, LUN-005, LUN-006, LUN-021)

## Infrastructure

| Service | Purpose | Region | Free Tier |
|---------|---------|--------|-----------|
| Cloud Run | App hosting | us-central1 | 2M req/month, 180k vCPU-sec |
| Firestore | Database | us-central1 | 1GB storage, 50k reads/day |
| Secret Manager | Secrets | Global | 6 active secrets |
| Artifact Registry | Docker images | us-central1 | 500MB |

**Region**: `us-central1` — only US regions qualify for Cloud Run free tier.

## Secrets

Managed via Google Secret Manager. Never in `.env` in production.

| Secret | Purpose |
|--------|---------|
| GOOGLE_CLIENT_ID | OAuth client ID |
| GOOGLE_CLIENT_SECRET | OAuth client secret |
| ANTHROPIC_API_KEY | Claude API access |
| GEMINI_API_KEY | Gemini API access |
| SPREADSHEET_ID | Default Sheet ID (if applicable) |
| NEXTAUTH_SECRET | NextAuth.js session encryption |

Local development uses `.env.local` (gitignored).

## Workflow

### 1. Local Development
```bash
npm run dev              # Next.js dev server
npm run storybook        # Component docs
```

`.env.local` for local secrets:
```
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
ANTHROPIC_API_KEY=...
GEMINI_API_KEY=...
NEXTAUTH_SECRET=...
NEXTAUTH_URL=http://localhost:3000
```

### 2. Docker
```dockerfile
# Multi-stage build for Next.js
FROM node:20-alpine AS base

FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public
EXPOSE 3000
CMD ["node", "server.js"]
```

### 3. Cloud Run Deployment
```bash
# Build and push
gcloud builds submit --tag gcr.io/pfm-lunas/lunas-app

# Deploy
gcloud run deploy lunas-app \
  --image gcr.io/pfm-lunas/lunas-app \
  --region us-central1 \
  --platform managed \
  --allow-unauthenticated \
  --set-secrets="GOOGLE_CLIENT_ID=GOOGLE_CLIENT_ID:latest,GOOGLE_CLIENT_SECRET=GOOGLE_CLIENT_SECRET:latest,ANTHROPIC_API_KEY=ANTHROPIC_API_KEY:latest,GEMINI_API_KEY=GEMINI_API_KEY:latest,NEXTAUTH_SECRET=NEXTAUTH_SECRET:latest"
```

### 4. CI/CD (GitHub Actions)
Trigger: Push to `main` branch.

```yaml
# .github/workflows/deploy.yml
name: Deploy to Cloud Run
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: google-github-actions/auth@v2
        with:
          credentials_json: ${{ secrets.GCP_SA_KEY }}
      - uses: google-github-actions/setup-gcloud@v2
      - run: gcloud builds submit --tag gcr.io/pfm-lunas/lunas-app
      - run: gcloud run deploy lunas-app --image gcr.io/pfm-lunas/lunas-app --region us-central1 --platform managed --allow-unauthenticated
```

## Rules

### Do
- Always use `us-central1` region (free tier)
- Use Secret Manager for all production secrets
- Use multi-stage Docker builds to minimize image size
- Set `NODE_ENV=production` in production
- Enable `output: 'standalone'` in `next.config.js` for Docker
- Pin Node.js version in Dockerfile

### Don't
- Commit `.env` or `.env.local` to git
- Use regions outside `us-central1` (loses free tier)
- Store secrets as Cloud Run environment variables directly — use Secret Manager references
- Skip the build step — always `npm run build` before deploy
- Deploy from `development` branch — only `main`

## Cost Control

- Cloud Run: Set max instances to 1-2 for personal use
- Claude API: $100/month hard cap — enforce in `server/lib/claude.ts`
- Firestore: Monitor reads in GCP console
- Cloud Build: 120 min/day free — more than enough

## Checklist Before Done

- [ ] App runs in Docker locally
- [ ] Cloud Run deployment succeeds
- [ ] All secrets in Secret Manager (not env vars)
- [ ] CI/CD pipeline triggers on push to main
- [ ] Region is us-central1
- [ ] Max instances set appropriately
- [ ] `.env.local` is in `.gitignore`
