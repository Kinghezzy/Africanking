# Africanking API

The API is a standalone Next.js service. Its route handlers and request-ID middleware live here; server utilities remain in `apps/web/lib` and are resolved through the API TypeScript aliases.

## Local development

1. Copy `.env.example` to `.env.local` and provide the required server values.
2. Run `corepack pnpm --filter @be-energy/api dev` to start the API on port 3001.
3. Run `corepack pnpm --filter @be-energy/web dev` for the frontend on port 3000. The frontend proxies `/api/*` to `http://localhost:3001` by default.

To proxy to another API origin, set `API_URL` in the frontend environment. Use the API origin only, without a trailing slash or `/api` path.

## Deployment

Deploy this directory as a separate Next.js project with the API environment variables from `.env.example`. Set `API_URL` in the frontend deployment to the deployed API origin. The browser continues to use same-origin `/api/*` requests through the frontend proxy, including the HTTP-only session cookie.

Run API route tests with `corepack pnpm --filter @be-energy/api test:api`.

## Stellar configuration validation

At startup (`instrumentation.ts`) the API checks the network passphrase, the RPC/Horizon endpoints, the contract IDs and the deployment manifest (`apps/contracts/deployments/<network>.json`) against each other.

- Invalid configuration on `PUBLIC` fails startup. Set `STELLAR_CONFIG_ON_ERROR=read-only` to keep a read-only API running instead.
- On other networks, invalid configuration disables write routes (mint/retire return `503 WRITES_DISABLED`).
- `GET /api/health/config` returns a secret-free summary and a `sha256:` configuration fingerprint. Use it to compare environments after a deploy.

Rollout: set `STELLAR_DEPLOYMENT_MANIFEST` in every API environment **before** deploying this version. Without it, testnet only gets a warning, but `PUBLIC` refuses to start.

## Stellar transaction service

All contract writes (`/api/mint`, `/api/certificates/retire`) go through `server/stellar/transaction-service.ts`. It handles simulation, fee and resource assembly, submission, bounded polling, and error classification in one place.

| Code | HTTP | Meaning | On-chain? |
|---|---|---|---|
| `SIMULATION_FAILED` | 422 | Contract rejected the call in simulation | No |
| `SUBMISSION_REJECTED` | 502 | RPC returned `ERROR` (result code in `details`) | No |
| `TRY_AGAIN_LATER` / `RPC_UNAVAILABLE` | 503 | Retryable, nothing submitted | No |
| `FAILED` | 502 | Included in a ledger but failed | Yes (fee charged) |
| `TIMEOUT` / `UNKNOWN_STATUS` | 504 / 502 | Submitted, outcome unknown. Reconcile by `tx_hash` | Maybe |

Ambiguous network errors during submission re-send the **same signed envelope**, never a rebuilt transaction, so a retry cannot double-mint. Every failure is written to `audit_log` with its structured reason and hash.

Rollout note: legacy readings now keep `status = 'pending'` with `tx_hash` set while an outcome is unknown, and `/api/mint` answers `409 UNRECONCILED_TRANSACTION` until an operator reconciles them. Only non-retryable failures that never reached the ledger still mark a reading `failed`.
