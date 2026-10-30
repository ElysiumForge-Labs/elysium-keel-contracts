# elysium-keel-contracts

**Keel** Foundry contracts for token launches on **Kinetiq's Elysium testnet**: constant-product absorb curve, permissionless graduation, and settlement onto a Hyperliquid Core spot book (Elysium → HyperEVM bridge leg + keeper).

Part of [ElysiumForge Labs](https://github.com/ElysiumForge-Labs). Companion UI: [elysium-keel-app](https://github.com/ElysiumForge-Labs/elysium-keel-app).

> **Independent research repository. Testnet only.** Not affiliated with, endorsed by, or sponsored by Kinetiq or Hyperliquid. `SPEC.md` is the source of truth for the mechanism. Contracts have not been independently audited for mainnet use. HIP-1 listing on testnet is budget-blocked; see `docs/STATUS.md`.

## Live on Elysium testnet (chain 99801)

| Contract | Address |
| --- | --- |
| Factory | [`0x8547e759715b1bbd67291e06395E0C5FfeA4de13`](https://elysium.kinetiq.xyz/testnet-explorer/address/0x8547e759715b1bbd67291e06395E0C5FfeA4de13) |
| Settlement | [`0x2cde65C326E61cD9619F4f4f08D20Eac0015559C`](https://elysium.kinetiq.xyz/testnet-explorer/address/0x2cde65C326E61cD9619F4f4f08D20Eac0015559C) |
| Bridge adapter | [`0x9BAA610A43B8f62F0d014aF4EEFB0dF44AF87e37`](https://elysium.kinetiq.xyz/testnet-explorer/address/0x9BAA610A43B8f62F0d014aF4EEFB0dF44AF87e37) |

Graduation target on this deployment is 0.4 HYPE, with 0.1 HYPE reserved for the ticker.

## Core contracts

| Contract | Role |
| --- | --- |
| `CorePadToken` | Plain ERC-20 (solady); 1B × 1e18 minted once to its pool |
| `CorePadFactory` | Permissionless `launch`; unique symbols; reserved HyperCore tickers refused |
| `LaunchPool` | Virtual x·y curve, 1% fee to treasury, launch guard, `graduate` / `reopen` |
| `Settlement` | Tickets: open → dispatch → confirm (or abort after `rescueDelay`) |
| `ElysiumBridgeAdapter` | Tokens via mirror bridge; HYPE via `ArbSys.withdrawEth` |

## Trust model (summary)

- Graduation is on-chain; HyperCore HIP-1 listing is signed by a keeper EOA (`coreSettler`) until a CoreWriter path exists — the keeper has custody after bridge claim.
- Elysium → HyperEVM withdrawals are optimistic (challenge period before Outbox claim).
- Ticker cost is funded by the curve (`tickerBudget`); keeper never tops up.

See `SPEC.md`, `docs/BRIDGE_NOTES.md`, and `docs/STATUS.md` for the full mechanism, bridge notes, and live testnet status.

## Development

```bash
# Foundry — populate lib/ via git submodules when building
forge test
forge build
```

Solidity `0.8.28`, EVM cancun, Foundry profiles in `foundry.toml`.
