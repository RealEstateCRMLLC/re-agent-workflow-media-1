# re-agent-workflow-media-1

Automated real estate marketing media pipeline. It receives CRM webhooks and routes property media through a chain of marketing steps: image transformation (day/night variations via Magnific AI), branding (Canva), landing pages (Lofty), and context-aware social captions posted to Facebook, LinkedIn, and Instagram.

## Status

**In development; quiet since 2026-10-21.** Version 2.16.0 (per `VERSION.md` and `package.json`). The last commit (2026-10-21) removed all mock data so integration failures surface honestly instead of fabricating assets. No commits since that date — the repo is not abandoned, but it has had no activity in the weeks since.

## How it works

When a `POST /webhook/crm` payload arrives:

1. `FolderDetectionService` verifies the media source.
2. `AssetStorageService` and `MagnificAiService` generate property assets.
3. `SocialCopyService` generates brand-safe marketing captions (prefers DeepSeek when `DEEPSEEK_API_KEY` is set).
4. `ApprovalWorkflowService` logs audit records.
5. `SocialPublishingService` pushes the post asynchronously.

If integrations fail or keys are missing, the service degrades gracefully (in-memory fallback) rather than crashing.

## Stack

TypeScript + Express 5, PostgreSQL (`pg`), Redis, Socket.IO. Jest tests. Docker Compose available for a Postgres-backed run. Runs on port 3004 (see `start-3004.bat`).

## What's working / what's not

- Webhook intake, media pipeline services, caption generation, and approval audit logging are implemented, with a test suite (`npm test`).
- External integrations (Magnific AI, Canva, Lofty, social posting) depend on API keys configured via `.env` — no evidence in the repo that the full chain has run end-to-end against live services. The 2026-10-21 mock-data removal means missing keys now fail loudly instead of silently producing fake output.
- Housekeeping issues: `hypernexus.db` (a ~532 KB SQLite database) is committed to the repo and should not be; `package.json` still points at the pre-transfer repo URL (`candlestixxx/re-agent-workflow-media-1`).

## Relationship to RealEstateCRM

This is a satellite service of [RealEstateCRM](https://github.com/RealEstateCRMLLC/RealEstateCRM) — the marketing-media pipeline that consumes CRM webhooks and produces listing marketing assets. It is a separate service, not part of the main CRM codebase.

## Running it

1. Copy `.env.example` to `.env` and fill in API keys.
2. `npm install`
3. `npm run build`
4. `npm test`
5. `npm start` (or `docker-compose up --build` for Postgres)

## Links

- Main CRM: https://github.com/RealEstateCRMLLC/RealEstateCRM
- Org: https://github.com/RealEstateCRMLLC
