# Africanking

> **Renewable energy cooperatives deserve proof. Africanking puts it on-chain.**

[![Live Demo](https://img.shields.io/badge/🌐_Live_Demo-be--energy--six.vercel.app-00537A?style=for-the-badge)](https://be-energy-six.vercel.app)
[![Network](https://img.shields.io/badge/Stellar-Testnet-FFD500?style=for-the-badge&logo=stellar)](https://stellar.org)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue?style=for-the-badge)](LICENSE)

---

> ### ⚠️ Estado actual (pivot en curso — leer antes de lo demás)
> Africanking está pivoteando el modelo de producto. El flujo de "comprador externo / certificado vendido a ESG" y cualquier mercado secundario descritos más abajo **no son el rumbo actual** — se descartaron junto con el marketplace P2P. La nueva dirección: software de gestión de créditos de generación compartida para cooperativas eléctricas (foco Argentina, referencia: Exa Energia en Brasil). El resto de este README todavía no está actualizado al pivot — se está reescribiendo por partes. No asumir que lo de abajo describe el producto que se está construyendo hoy.

---

## 🏆 Recognition

| Award | Event |
|-------|-------|
| 🥇 **Featured Project** | [Stellar Buenos Aires Hackathon 2025](https://dorahacks.io/buidl/36793) |
| 🏅 **Innovation Certificate** | Stellar Jury — Buenos Aires 2025 |
| 🌍 **Selected** | [ClimateLaunchpad 2026](https://climatelaunchpad.org/) — world's largest green startup competition, powered by Climate-KIC & Chrysalis LEAP |

---

## The Problem

Renewable energy cooperatives generate clean energy every day — but **they can't prove it**.

- No standardized way to record generation
- No verifiable certificate that travels with the energy claim
- No access to ESG markets, carbon offsetting programs, or institutional buyers
- Manual, paper-based processes that nobody trusts

Meanwhile, companies and funds with ESG commitments are actively looking for verifiable renewable energy claims — and can't find them.

**The gap between cooperatives and buyers is a trust gap. Africanking closes it.**

---

## The Solution

Africanking is a cooperative management dashboard + on-chain certification infrastructure on **Stellar**.

Every kWh registered by a cooperative becomes a **proto-certificate**: a verifiable, on-chain claim tied to a real meter, a real cooperative, and a real time period.

```
Smart meter sends reading → POST /api/meters/readings
         ↓
Africanking validates & mints token on Stellar (1 token = 1 kWh)
         ↓
Certificate is assigned to cooperative members
         ↓
External buyer (company / ESG fund) purchases certificate
         ↓
Buyer retires certificate on-chain (verifiable burn)
         ↓
Permanent, auditable proof on Stellar blockchain
```

No intermediaries. No PDFs. No trust required — the chain is the proof.

---

## How It Works — Full Platform Flow

### For Cooperatives

```
1. Register cooperative on Africanking
2. Add smart meters (physical IoT devices)
3. Meters send generation readings automatically via API
4. Africanking mints proto-certificates on Stellar
5. Manage members, view generation stats, track certificates
```

### Authentication

Africanking supports two access methods — no setup required to try:

**Email + Password** (for admins, buyers, internal team)
```
/login → Supabase auth → JWT issued → Stellar wallet auto-assigned → /dashboard
```
> Try it: `demo@beenergy.coop` / `Demo2026!`

**Stellar Wallet** (for cooperatives with Freighter / xBull / Lobstr)
```
Connect wallet → Server issues challenge → User signs with private key
→ Signature verified on-chain → JWT issued → /dashboard
```

Both methods issue the same JWT and give full platform access. Wallet users can additionally sign on-chain transactions.

---

## Stellar Integration — What's Actually On-Chain

This is not a wrapper. Stellar is load-bearing infrastructure:

| What | How |
|------|-----|
| **Certificate issuance** | Mint SEP-41 tokens directly on Stellar Testnet |
| **Certificate retirement** | On-chain burn, permanently auditable |
| **Member allocation** | `energy_distribution` contract splits tokens by participation % |
| **Governance** | `community_governance` contract for cooperative proposals |
| **Wallet auth** | Challenge-response signature verification (no gas, no tx) |
| **Token standard** | SEP-41 (fungible) with OpenZeppelin Stellar Pausable + Upgradeable |

### Deployed Contracts — Stellar Testnet

| Contract | Address | Purpose |
|----------|---------|---------|
| `energy_token` | [`CCYOVOFD...MRPBA6`](https://stellar.expert/explorer/testnet/contract/CCYOVOFDJ5BVBSI6HADLWETTUF3BU423MEAWBSBWV2X5UVNKSJMRPBA6) | SEP-41 token — 1 token = 1 kWh certified |
| `energy_distribution` | [`CBTDPLFN...NX2UDZ`](https://stellar.expert/explorer/testnet/contract/CBTDPLFNFGWVOD4HXDKW4EH5L3D2YGOY5CWTFCJM5TEWFL4VQTNX2UDZ) | Distributes certificates to members |
| `community_governance` | [`CCH2EXXN...BJD6YI`](https://stellar.expert/explorer/testnet/contract/CCH2EXXNSDW2BAKBIPFAG6CCZS6LV4VJFUP2CZZCW5LEY4JOAXBJD6YI) | Cooperative on-chain governance |

Built with **OpenZeppelin Stellar Contracts v0.5.1** + **Soroban SDK 23.1.0** — 65 tests passing.

---

## Customer Discovery

We validated the problem directly with:

- **Energy cooperatives** in Argentina — confirmed the pain of not being able to sell renewable attributes
- **ESG compliance teams** — confirmed demand for verifiable, auditable certificates
- **Climate program coordinators** — confirmed interest in blockchain-based proof over PDFs

Key insight: the bottleneck isn't production — cooperatives generate plenty. The bottleneck is **verifiable proof**. Africanking solves exactly that.

---

## Live Demo

**Platform:** https://be-energy-six.vercel.app
**Network:** Stellar Testnet
**Demo login:** `demo@beenergy.coop` / `Demo2026!`

**[Watch Demo Video →](https://www.youtube.com/watch?v=c5avxNUI18Y)**

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Blockchain | Stellar Testnet (Soroban smart contracts) |
| Smart Contracts | Rust + OpenZeppelin Stellar v0.5.1 |
| Token Standard | SEP-41 fungible token |
| Frontend | Next.js 16 + React 19 + TypeScript |
| Styling | Tailwind CSS v4 + shadcn/ui |
| Auth | Supabase + JWT + Stellar wallet signature |
| Wallet Support | Freighter, xBull, Lobstr (via Stellar Wallets Kit) |
| Backend | Standalone Next.js API service + Supabase |
| Deployment | Vercel |
| Monorepo | Turborepo + pnpm |

---

## Monorepo Structure

```
Africanking/
├── apps/
│   ├── contracts/           # Soroban smart contracts (Rust)
│   │   ├── energy_token/         # SEP-41 certificate token
│   │   ├── energy_distribution/  # Member allocation logic
│   │   └── community_governance/ # DAO-style proposals
│   ├── api/                  # Standalone Next.js API service
│   └── web/                  # Next.js dashboard
│       ├── app/             # App Router pages
│       ├── components/      # UI components
│       └── lib/             # Auth, wallet, Stellar utils
├── packages/
│   └── stellar/             # Shared wallet & config utilities
└── tooling/
    └── issues/              # GitHub issue templates
```

---

## Quick Start

```bash
git clone https://github.com/BuenDia-Builders/Africanking.git
cd Africanking
pnpm install
# Set required backend values in apps/api/.env.local using apps/api/.env.example
pnpm dev
```

Frontend: `http://localhost:3000`

API: `http://localhost:3001` (the frontend proxies `/api/*` to the API service)

**Build & test contracts:**
```bash
cd apps/contracts
stellar contract build
cargo test
```

---

## Backup, Restore & Disaster Recovery

Operational runbook for the cooperative-management platform. Generation credits are assigned to cooperative members; this is **not** an external certificate marketplace and makes **no** I-REC/ESG certification claim. Stellar is the immutable evidence layer; the relational store is the operational system of record.

### RPO / RTO targets

| Data class | RPO | RTO | Notes |
|------------|-----|-----|-------|
| Relational DB (cooperatives, members, meters, readings, credit allocations) | ≤ 15 min | ≤ 2 h | Point-in-time recovery from continuous WAL archiving |
| Migrations (`apps/api` schema) | 0 (versioned in git) | ≤ 30 min | Replayed from the exact commit/tag of the restored snapshot |
| Audit records (append-only) | ≤ 15 min | ≤ 2 h | Restored with the DB; never edited in place |
| Contract manifests (deployed contract IDs, wasm hashes, network) | 0 (versioned in git) | ≤ 15 min | Recovered from repo tag, cross-checked on-chain |

### Backup scope

- **Database:** automated daily full snapshot + continuous WAL/PITR archiving; retention 30 days.
- **Migrations:** the schema is defined by versioned migrations in the repo — the backup records the deployed commit SHA so migrations can be replayed deterministically.
- **Audit records:** append-only tables included in the same snapshot; treated as immutable evidence and never rewritten during restore.
- **Contract manifests:** `energy_token`, `energy_distribution`, `community_governance` contract IDs, wasm hashes, and network are stored in the repo and re-verified against Stellar after restore.

### Restore order

1. Provision an isolated restore target (never restore over production).
2. Restore the database snapshot to the target and replay WAL to the chosen recovery point.
3. Check out the recorded commit SHA and apply migrations up to that point; confirm `migrate status` is clean.
4. Restore audit records (already part of the snapshot) and verify append-only integrity.
5. Load the contract manifests from the repo tag and confirm contract IDs/wasm hashes match the deployed contracts.
6. Run post-restore consistency checks (below) before any traffic is pointed at the target.

### Reconciliation against immutable Stellar evidence

Stellar is the source of truth for on-chain facts; the DB is reconciled *to* it, never the reverse.

- For each credit allocation / retirement in the restored DB, fetch the corresponding on-chain transaction and compare amount, member, cooperative, and timestamp.
- Any DB record with no on-chain counterpart is flagged as a **data gap** (candidate for re-issue or manual review) — never silently deleted.
- Any on-chain event missing from the DB is replayed from Stellar into the restored store.
- Contract IDs and wasm hashes must match the manifest; a mismatch aborts the restore.

### Post-restore consistency checks (automated)

Run after every restore and on a schedule against production:

- Row counts and checksums for cooperatives, members, meters, readings, allocations, audit tables vs. the snapshot baseline.
- Every allocation/retirement has a matching Stellar transaction (no orphans in either direction).
- Sum of member allocations per cooperative equals the minted total for that period.
- Migration version equals the recorded commit SHA's latest migration.
- Contract manifest IDs/hashes match the live network.

### Staged restore rehearsal

Rehearsals run on an isolated target using a sanitized snapshot (PII redacted). Record the following each run:

| Metric | Target |
|--------|--------|
| Time to restore DB + replay WAL | ≤ 2 h |
| Time to apply migrations | ≤ 30 min |
| Time to reconcile against Stellar | ≤ 1 h |
| Data gaps found (DB↔chain mismatches) | 0 unexplained |
| Consistency checks passing | 100% |

Sanitized rehearsal evidence (timings, gap counts, check results — no PII, no secrets) is attached to the tracking issue for each rehearsal. Any unexplained data gap or failed consistency check blocks sign-off and is documented as a migration/rollout requirement before the next release.

---

## Roadmap

| Level | What | Status |
|-------|------|--------|
| 1 — Internal registry | Token = verifiable production record | ✅ Live on Testnet |
| 2 — Verified certification | IoT oracles + independent meter validation | 🔄 Next |
| 3 — Recognized standard | Integration with I-REC, Energy Web, TIGR | 🔮 Future |

---

## Planned Stellar Ecosystem Integrations

Africanking is designed to p