# Math Scribe

Turns spoken or typed math into clean, rendered notation and graphs.

**Live:** https://danielburkhalter.dev/math-scribe/

| Folder | What | Deployed to |
|--------|------|-------------|
| `app/` | React + Vite frontend | GitHub Pages (auto, on push to `main`) |
| `backend/` | Node.js WebSocket server (Claude) | Google Cloud Run (manual `gcloud run deploy`) |

Auth is Firebase Auth (Google sign-in). See [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md).

## History

Moved here from the [`vibe-code`](https://github.com/dan-burk/vibe-code/tree/math-scribe) repo on October 12, 2026. At the same time, the frontend moved from Firebase Hosting (`math-scribe-3a4b6.web.app`, which now redirects) to GitHub Pages.
