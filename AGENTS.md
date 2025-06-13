## 🏗️ Project Overview
PrunePal guarantees **≥ 20 % free space** on media by  
1. Polling disk capacity (`packages/core/monitor`)  
2. Scoring delete candidates with OpenAI (`packages/core/ai`)  
3. Enforcing user-defined policies and deleting or un-monitoring titles (`packages/core/executor`)

## 🔧 Dev Environment
* **Node 22**, Yarn Berry workspaces, strict TypeScript  
* Common scripts  
  * `yarn start`
  * `yarn test`
  * `yarn lint` – ESLint + Prettier  
* CI: GitHub Actions → build Docker images → push to GHCR

## 🛡️ Hard Rules the Agent MUST Obey
* **Never** reduce `freePercent` below **20.0**.  
* Delete files only when **all** are true:  
  1. `AI_score ≤ 0.25`  
  2. `policy.level !== "dry-run"`  
  3. `snapshotComplete === true` (ZFS/Btrfs snapshot taken)  
* Unit, integration and e2e tests **must stay green** (`yarn test`).

## 🔄 Test & Run Locally
```bash
# Spin up complete stack
docker compose up -d
# Run fast tests
yarn test
# Manual dry-run
curl -X POST http://localhost:3000/api/prune?dry=true
````

## 🚫 Forbidden Paths

* `packages/ui/.next/` – build artefacts, do not edit
* `backups/` – Snapshots, **never** modify or delete
* Any file matching `*.media.bin` – production data

## 🖇️ Monorepo Layout

```
packages/
  core/        # monitor, ai, policy, executor
  integrations # *arr, Tautulli, Overseerr helpers
  ui/          # Next.js dashboard
  cli/         # small wrapper for cron / HA calls
  shared/      # Zod schemas & types
```

## 📜 Commit & PR Style

* Conventional Commits (`feat:`, `fix:`, `chore:` …)
* Run `yarn lint` & `yarn test` before pushing
* PR template: `.github/PULL_REQUEST_TEMPLATE.md` must be filled

## 🌐 External Services (staging creds only)

```
RADARR_URL=http://radarr:7878
SONARR_URL=http://sonarr:8989
TAUTULLI_URL=http://tautulli:8181
OPENAI_API_KEY=dummy-key-for-tests
```

Never store production keys in the repo.

## 💡 Examples the Agent Can Use

```
### Implement recycle-bin folder before deleting files
### Add unit test for @core/ai/score()
### Refactor executor.ts to support retries via BullMQ
```
