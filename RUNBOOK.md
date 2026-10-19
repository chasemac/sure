# Sure on the Mac mini — runbook

Fork: `chasemac/sure` (forked from `we-promise/sure`)
Verified 2026-10-19:
- Prebuilt image `ghcr.io/we-promise/sure:stable` ships **linux/arm64** — runs natively on the M6, no emulation.
- Plaid link flow requests **730 days** of history in production (`MAX_HISTORY_DAYS` in `app/models/provider/plaid.rb`) — no patch needed. Just link banks once; history can't be extended later without re-linking.
- Plaid credentials can be entered in the app UI: **Settings → Providers → Plaid** (stored encrypted in the DB). Env vars (`PLAID_CLIENT_ID`, `PLAID_SECRET`, `PLAID_ENV`) also work and take precedence.
- `compose.override.yml` caps the stack at ~1.75 GB RAM total.

## First-time setup (on the Mac mini)

```bash
# 1. Install Docker Desktop for Mac (Apple Silicon) from docker.com, start it,
#    verify: docker run hello-world

# 2. App directory
mkdir -p ~/docker-apps/sure && cd ~/docker-apps/sure

# 3. Compose files from YOUR fork
curl -fsSL -o compose.yml https://raw.githubusercontent.com/chasemac/sure/main/compose.example.yml
curl -fsSL -o compose.override.yml https://raw.githubusercontent.com/chasemac/sure/main/compose.override.yml

# 4. Environment file
curl -fsSL -o .env https://raw.githubusercontent.com/chasemac/sure/main/.env.example
# Generate the app secret and put it in .env as SECRET_KEY_BASE:
openssl rand -hex 64

# 5. Start it
docker compose up -d
# First boot takes a few minutes (DB init + migrations). Then open:
open http://localhost:3000
```

## Plug in the Plaid key (in the app — no terminal needed)

1. Open http://localhost:3000 → create your account (first user = admin).
2. Go to **Settings → Providers → Plaid**.
3. Paste **Client ID**, **Secret**, set environment to **production**.
   (Get them from https://dashboard.plaid.com/team/keys after signup + Trial approval.)
4. Go to **Accounts → Add → Plaid**, pick your bank, link it.
   Sure requests 730 days of history on first link automatically.
5. Done — syncs run in the background via the worker.

## Daily use (on-demand, keeps the mini idle)

```bash
cd ~/docker-apps/sure
docker compose up -d    # start when you want the dashboard
docker compose down     # stop when done — data lives in Docker volumes, safe
```

## Notes

- Data persists in the `postgres-data` and `app-storage` Docker volumes across restarts.
- The CloudQuery sync pipeline (setup/README.md) is independent — it keeps its own
  Postgres copy as a second source of truth even when Sure is down.
- Backups: enable the `backup` profile in compose.yml if you want nightly DB dumps
  (needs rclone remote config in .env).
- Updating later: `docker compose pull && docker compose up -d` (image is :stable).
