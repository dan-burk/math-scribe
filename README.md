# Math Scribe

Turns spoken or typed math into clean, rendered notation and graphs.

**Live:** https://danielburkhalter.dev/math-scribe/

| Folder | What | Deployed to |
|--------|------|-------------|
| `app/` | React + Vite frontend | GitHub Pages (auto, on push to `main`) |
| `backend/` | Node.js WebSocket server (Claude) | Google Cloud Run (manual `gcloud run deploy`) |

Auth is Firebase Auth (Google sign-in). See [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md).
