# Base to Starknet Secure Agent DeFi Demo (Sepolia)

On March 4, 2026 we ran a live Sepolia demo to show one narrative end to end:

1. Cross-chain context can be attached (signed Base attestation fixture).
2. Starknet agent actions can execute real DeFi operations.
3. Session-account controls can deny unsafe actions even when signed by an active agent stack.

## What We Ran

Two runners were used together:

- `examples/secure-defi-demo/run.ts` (execution path + attestation artifact path + Vesu actions)
- `examples/full-stack-swarm/run.ts` (proxy/session-key execution + policy and revocation probes)

## Evidence Highlights

Artifacts:

- `examples/secure-defi-demo/artifacts/secure-defi-demo-516a8d17-2af9-4170-bf30-3afcdc1136f2.json`
- `examples/secure-defi-demo/artifacts/secure-defi-demo-516a8d17-2af9-4170-bf30-3afcdc1136f2.md`
- `examples/secure-defi-demo/artifacts/base-attestation-demo.json`
- `examples/full-stack-swarm/artifacts/swarm-demo-20260304-080847.log`

Verified Starknet Sepolia transactions:

- Allowed transfer (`SUCCEEDED`):  
  `0x8dfd41b6b6a473bf53bb92a1ec086ed8287c9652b109c52dedd98a36d15e95`
- Vesu deposit (`SUCCEEDED`):  
  `0x2916384313cd7e6aefa4284d11e7e62d0019aec5858243eb44537d3a0ce334`
- Proxy-mode swap with session key (`SUCCEEDED`):  
  `0x55953168086ab15a4f9b04244107b0f8676b6f2e2b42cf2efe328ac2eb6ab69`
- Oversized invoke denied by spending policy (`REVERTED`):  
  `0x3900f732b2e9061350be30707ca7bcf48d16b346041c85ebbff3b90772a3609`  
  revert reason contains: `Spending: exceeds per-call`
- Session revocation tx (`SUCCEEDED`):  
  `0x43c34a21cf30e5b187ef1b2e4c56157cf3c7d1672ac5899b5b82caabb33e6e9`  
  follow-up action attempt was blocked by account validation (`validate` returned invalid).

## Security Claims Proven in This Run

1. Safe actions execute: transfer and Vesu deposit succeeded.
2. Unsafe over-limit action is blocked at account policy level.
3. Revoked session cannot continue acting successfully.
4. Runtime records machine-readable evidence (JSON + logs) for audit and postmortem.

## Suggested Screenshots

Use these screenshots for the post/thread:

1. Terminal start + tool discovery from `secure-defi-demo`.
2. Secure demo summary table from `secure-defi-demo-...md` showing `OK/Failed/Skipped`.
3. JSON snippet proving signed Base attestation verification (`base_attestation` step).
4. Successful transfer tx line in artifact (`allowed_transfer_execute.transactionHash`).
5. Successful Vesu deposit tx line (`vesu_deposit.tx.transactionHash`).
6. Swarm log snippet showing `Spending: exceeds per-call`.
7. Swarm JSON section with `deniedByPolicy: true`.
8. Swarm JSON/log section showing revoked-session block (`validate ... Got Retdata([0x0])`).
9. Explorer pages for one success tx and one reverted tx side by side.

## Notes for Publication

- Do not publish local `.env` contents or any private keys.
- Do not publish `state.json` files containing private/session keys.
- Share only tx hashes, artifact excerpts, and sanitized logs.

## Next PR Tracks for Full Security Claim

1. Harden strict-claim mode in demo CI (require denial + revocation probes on secure profile).
2. Add explicit account-policy introspection snapshot into artifact (limits + spent window before/after).
3. Add cross-chain identity binding evidence in artifact (ERC-8004 lookup + attestation linkage required in strict profile).
