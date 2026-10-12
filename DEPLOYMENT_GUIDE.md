# Deployment Guide

## Architecture Overview

| Component | Service | Cost |
|-----------|---------|------|
| Frontend (React) | GitHub Pages (`danielburkhalter.dev/math-scribe`) | Free |
| Backend (Node.js + WebSocket) | Google Cloud Run | Pay-as-you-go |
| Auth | Firebase Auth | Free tier |

---

# Part 1: Quick Deploy

Use these commands when your environment is already set up.

## Step 1: Deploy Backend

```powershell
# Set the correct project
gcloud config set project math-scribe-3a4b6

# Deploy from backend directory
cd backend
npm run build
gcloud run deploy math-scribe-backend `
  --source . `
  --region us-central1 `
  --allow-unauthenticated `
  --set-env-vars "ANTHROPIC_API_KEY=<your-api-key>,CLAUDE_MODEL=claude-sonnet-5,CLAUDE_MAX_TOKENS=8000"
```

`CLAUDE_MODEL` and `CLAUDE_MAX_TOKENS` are required — the backend exits at startup if either is missing.

### Changing the model (no rebuild)

```powershell
gcloud run services update math-scribe-backend --region us-central1 `
  --update-env-vars "CLAUDE_MODEL=claude-opus-5"
```

**Backend URL:** https://math-scribe-backend-bae5znb3dq-uc.a.run.app

## Step 2: Deploy Frontend

Push to `main`. The `Deploy frontend to GitHub Pages` workflow (`.github/workflows/deploy-pages.yml`) builds `app/` and publishes it whenever anything under `app/` changes. You can also run it manually from the Actions tab.

Build-time config comes from **repo variables** (Settings → Secrets and variables → Actions → Variables), not from `.env.production`:

- `VITE_WS_URL`: backend WebSocket URL, e.g. `wss://math-scribe-backend-bae5znb3dq-uc.a.run.app/ws` (`wss://`, ending in `/ws`)
- `VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_AUTH_DOMAIN`, `VITE_FIREBASE_PROJECT_ID`, `VITE_FIREBASE_STORAGE_BUCKET`, `VITE_FIREBASE_MESSAGING_SENDER_ID`, `VITE_FIREBASE_APP_ID`

Update one with `gh variable set VITE_WS_URL --body "wss://..."`, then re-run the workflow.

The app is served under `/math-scribe/`, set by `base` in `app/vite.config.ts`. Reference files in `app/public/` through `import.meta.env.BASE_URL`, not with a leading `/`.

**Frontend URL:** https://danielburkhalter.dev/math-scribe/

## Step 3: Firebase Auth domain

Firebase Auth (Google sign-in) is still used. `danielburkhalter.dev` must be listed under Firebase Console → Authentication → Settings → Authorized domains.

## Step 4: Check Logs
gcloud run services logs read math-scribe-backend --region us-central1 --limit 50
---

# Part 2: First-Time Setup & Troubleshooting

## Prerequisites

### Install Required Tools

```powershell
# Install Google Cloud CLI
# Download from: https://cloud.google.com/sdk/docs/install

gcloud auth login
```

## Initial Setup Steps

### 1. Set Google Cloud Project

```powershell
gcloud config set project math-scribe-3a4b6
```

Verify the correct project:
```powershell
gcloud config get-value project
```

### 2. Enable Billing

Cloud Run requires billing to be enabled (has a generous free tier):

https://console.cloud.google.com/billing/linkedaccount?project=math-scribe-3a4b6

### 3. Enable Required APIs

When deploying, the CLI will prompt you to enable these APIs. Say "yes" to all:
- `artifactregistry.googleapis.com`
- `cloudbuild.googleapis.com`
- `run.googleapis.com`

### 4. Fix IAM Permissions (if needed)

If you encounter permission errors during deployment, run these commands:

```powershell
gcloud projects add-iam-policy-binding math-scribe-3a4b6 `
  --member="serviceAccount:639915616844-compute@developer.gserviceaccount.com" `
  --role="roles/storage.objectViewer"

gcloud projects add-iam-policy-binding math-scribe-3a4b6 `
  --member="serviceAccount:639915616844-compute@developer.gserviceaccount.com" `
  --role="roles/logging.logWriter"

gcloud projects add-iam-policy-binding math-scribe-3a4b6 `
  --member="serviceAccount:639915616844-compute@developer.gserviceaccount.com" `
  --role="roles/artifactregistry.writer"
```

## Troubleshooting

### Wrong Google Cloud Project

Check which project you're using:
```powershell
gcloud config get-value project
```

Switch to the correct project:
```powershell
gcloud config set project math-scribe-3a4b6
```

Common causes:
- Missing dependencies in package.json
- TypeScript compilation errors
- Missing environment variables



View real-time logs:
```powershell
gcloud run services logs read math-scribe-backend --region us-central1 --limit 50
```

### PowerShell Multi-line Commands

Use backticks (\`) to continue commands across lines:
```powershell
gcloud run deploy math-scribe-backend `
  --source . `
  --region us-central1
```

Or put everything on one line:
```powershell
gcloud run deploy math-scribe-backend --source . --region us-central1 --allow-unauthenticated
```

### Getting the Backend URL

If you need to retrieve the deployed backend URL:
```powershell
gcloud run services describe math-scribe-backend --region us-central1 --format="value(status.url)"
```

## Useful Links

- **Frontend:** https://danielburkhalter.dev/math-scribe/
- **Backend:** https://math-scribe-backend-bae5znb3dq-uc.a.run.app
- **Firebase Console:** https://console.firebase.google.com/project/math-scribe-3a4b6
- **Cloud Run Console:** https://console.cloud.google.com/run?project=math-scribe-3a4b6
