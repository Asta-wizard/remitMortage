# Architecture: Remittance-Backed Mortgage Protocol

## Core Principle: Separate Verification from Settlement

The architecture splits into two layers: a **Verification Layer** that establishes a borrower's remittance-sending history on Stellar as proof of financial capacity, and a **Settlement Layer** (Stellar/Soroban) that handles the savings escrow, lending pool, milestone disbursements, and repayments.

---

## 1. Verification Layer (Off-Chain)

**Wallet ownership proof**: The borrower signs a message with each wallet they used to send remittances, proving control of those addresses.

**Transaction history lookup**: The backend queries Stellar's network directly — via Horizon (Stellar's API layer) — for the borrower's outgoing payment history in USDC, filtered to transfers sent to a specific recipient address (e.g., a family member's Stellar wallet).

**Pattern analysis**: A scoring service analyzes the verified transaction history for consistency — recurring amounts, regular intervals, sustained over a meaningful period, and signs that the recipient address belongs to a real independent party (receives from multiple sources, shows its own activity).

**Output**: An eligibility result (pass/fail plus supporting data). A hash of this verification report can be anchored on Stellar (stored in a Soroban contract) for auditability, without needing the full multichain dataset to live on-chain.

---

## 2. Settlement Layer (Stellar / Soroban)

### Escrow Contract — 30% Down-Payment Savings

A Soroban smart contract holds borrower contributions over the 6–12 month savings period, accepting USDC. It tracks individual balances within a pooled structure and releases the accumulated 30% into the construction/disbursement flow once the target is met — or refunds the borrower if they withdraw early, per the protocol's exit policy.

### Yield on Escrowed Funds

Pooled escrow funds can be deposited into a Soroban-based lending protocol (e.g., Blend Capital, pending verification that it's suitable and currently active) to earn passive yield while accumulating.

### Lending Pool Contract — 70% Capital 

Holds capital from depositors/investors, structured with tranches (senior/junior) if pursuing that model — senior tranches offering lower, more protected returns, junior tranches absorbing first losses for higher yield.

### Milestone Disbursement Contract

Releases funds from the lending pool to whitelisted real estate companies, contractors, and suppliers as construction milestones are completed (e.g., foundation, structure, roofing, finishing). Each release requires an IPFS-hashed photo/video submission of the milestone, referenced on-chain, plus governance approval.

### Governance / Multisig

Stellar accounts support **native multi-signature** — a single account can require multiple signatures (with configurable weights and thresholds) before authorizing a transaction. The milestone-approval account can be set up as a multisig account directly, with committee members as signers — no custom contract needed for the core approval mechanism. For more conditional logic (e.g., requiring both signature thresholds *and* a valid evidence hash before release), a thin Soroban contract wraps this logic on top of the multisig account.

### Stablecoin Settlement

All contributions, disbursements, and repayments move in USDC Stellar, benefiting from sub-cent transaction fees and 3–5 second settlement finality.

---

## 3. Evidence Storage

IPFS (via Pinata or a similar pinning service) stores milestone photos/videos and supplier receipts. The protocol records the resulting content hashes on-chain as references for governance review.

---

## 4. Frontend & Wallet Integration

A Next.js frontend integrates Stellar wallets (Freighter) for the settlement side — savings contributions, viewing loan status, repayments. For the verification step, the frontend also needs to support connecting whichever non-Stellar wallets the borrower used to send remittances (e.g., an Ethereum or Solana wallet), so they can sign a proof-of-ownership message during onboarding.

---

## 5. Backend / Orchestration

A Node.js/TypeScript backend coordinates the system:

- Stellar SDK (js-stellar-sdk) for querying Horizon/payment history and interacting with       Soroban contracts
- PostgreSQL for off-chain application and applicant records
- IPFS pinning service integration for evidence uploads

---

## End-to-End Flow Summary

1. Borrower proves ownership of remittance-sending wallet(s) and submits transaction references
2. Backend queries their Stellar payment history for outgoing transfers to a specified recipient address (e.g., a family member's Stellar wallet)
3. Borrower contributes toward 30% down payment over 6–12 months into the Soroban escrow contract (USDC), earning yield via a Soroban lending protocol
4. Once 30% is reached, the lending pool disburses the remaining 70% in milestone-based tranches to whitelisted suppliers/contractors, gated by photo evidence (IPFS) and multisig approval
5. Borrower repays the 70% loan over time in USDC, settling instantly on Stellar
