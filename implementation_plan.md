# Remittance-Backed Mortgage Protocol — Incremental Build Plan

Based on [ARCHITECTURE.md](file:///home/dp/Documents/remitMortage/ARCHITECTURE.md)

## Contracts to Build (in order)

From the architecture, there are **4 on-chain contracts** to build:

| # | Contract | Purpose |
|---|----------|---------|
| 1 | **Escrow** | Holds borrower's 30% down-payment savings (USDC) |
| 2 | **Lending Pool** | Holds investor capital for the 70% loan |
| 3 | **Milestone Disbursement** | Releases loan funds to contractors per milestone |
| 4 | **Verification Registry** | Anchors off-chain verification report hashes on-chain |

## Incremental Commits

### Phase 1 — Escrow Contract (Foundation)

| Commit | What to build | Commit message |
|--------|---------------|----------------|
| **1** | Workspace Cargo.toml + escrow contract skeleton (`Cargo.toml`, empty `lib.rs` with contract struct) | `feat(contracts): scaffold Soroban workspace and escrow contract skeleton` |
| **2** | Escrow data types — `BorrowerRecord`, `EscrowConfig`, storage keys, error enum | `feat(escrow): add data types, storage keys, and error definitions` |
| **3** | `initialize()` — admin sets USDC token, savings target, timeframe | `feat(escrow): implement initialize with config storage` |
| **4** | `deposit()` — borrower deposits USDC, updates their balance | `feat(escrow): implement deposit function with balance tracking` |
| **5** | `get_balance()` + `get_config()` — read-only query functions | `feat(escrow): add balance and config query functions` |
| **6** | `withdraw()` — early withdrawal with refund policy | `feat(escrow): implement early withdrawal with refund policy` |
| **7** | `release()` — release accumulated funds once 30% target is met | `feat(escrow): implement fund release on savings target completion` |
| **8** | Unit tests for escrow contract | `test(escrow): add comprehensive unit tests` |

### Phase 2 — Lending Pool Contract

| Commit | What to build | Commit message |
|--------|---------------|----------------|
| **9** | Lending pool contract skeleton + data types (tranches, investor records) | `feat(contracts): scaffold lending pool contract with data types` |
| **10** | `initialize()` + `deposit()` — investors deposit capital into pool | `feat(lending-pool): implement initialization and investor deposits` |
| **11** | `request_loan()` + `approve_loan()` — borrower loan lifecycle | `feat(lending-pool): implement loan request and approval flow` |
| **12** | `disburse()` — release funds from pool (called by milestone contract) | `feat(lending-pool): implement fund disbursement` |
| **13** | `repay()` — borrower repays loan in USDC | `feat(lending-pool): implement loan repayment` |
| **14** | Tests for lending pool | `test(lending-pool): add unit tests for pool operations` |

### Phase 3 — Milestone Disbursement Contract

| Commit | What to build | Commit message |
|--------|---------------|----------------|
| **15** | Milestone contract skeleton + data types (milestones, evidence) | `feat(contracts): scaffold milestone disbursement contract with types` |
| **16** | `create_project()` + `add_milestone()` — define construction milestones | `feat(milestone): implement project creation and milestone definition` |
| **17** | `submit_evidence()` — contractor submits IPFS hash as proof | `feat(milestone): implement evidence submission with IPFS hash` |
| **18** | `approve_milestone()` + `release_funds()` — multisig approval triggers disbursement | `feat(milestone): implement milestone approval and fund release` |
| **19** | Tests for milestone contract | `test(milestone): add unit tests for disbursement flow` |

### Phase 4 — Verification Registry Contract

| Commit | What to build | Commit message |
|--------|---------------|----------------|
| **20** | Verification registry skeleton + types | `feat(contracts): scaffold verification registry contract` |
| **21** | `register_verification()` + `get_verification()` — store and query verification hashes | `feat(verification): implement verification registration and queries` |
| **22** | Tests for verification registry | `test(verification): add unit tests for registry operations` |

---

## Starting Now: Commit 1

Scaffold the Soroban workspace and escrow contract skeleton.

> [!NOTE]
> Each commit is self-contained and compilable. You can `git add . && git commit` after each step.
