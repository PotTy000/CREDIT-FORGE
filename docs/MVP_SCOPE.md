# CreditForge — MVP Scope & Prioritization

**Version:** 0.1 — Phase 5.1–5.3 proposed
**Baseline:** [Requirements Specification v0.11](./REQUIREMENTS_SPECIFICATION.md) (blob 56a2de6e2f5dd6b9d6147e8472e43175052bc213); BRD v1.3; Financial Rules v1.1.
**Status:** proposed scope, not implementation acceptance. Owner continuation authorizes planning from the consolidated draft, not blanket approval of all RD details. Team, deadline, available hours, hardware, budget and rubric unknown.

## 5.1 MVP definition
A centralized TypeScript simulated secured-loan investment marketplace demonstrating one complete economic lifecycle: existing funded source loan, versioned issuer and marketplace approval, Senior/Junior issuance, primary purchase, partial secondary trade, source-backed automated repayment, interest/principal allocation and payouts, public repayment status, verified default, recovery and loss, reconciled portfolio. Separate scenario for healthy repayment/full payoff where feasible; no requirement to default and fully repay the same loan. Bank retains Junior and unsold Senior pari passu with external Senior. No real funds, bank integration, blockchain, borrower portal, underwriting, origination, wallet, collateral auction or real security/legal assurance. No Business Calendar or holiday API. All authored application/test code TypeScript.

Success means the selected flows work end-to-end with actual simulated cash and conserved rights, negative-path tests, reproducible setup and observable evidence. UI mockups and counts of requirements are not proof of implementation.

## 5.2 Prioritization principles
Use MoSCoW: Must = necessary for coherent safe MVP; Should = high-value enhancement after Must; Could = optional if capacity; Won't = excluded from MVP. Prioritize dependency and financial correctness over feature breadth. Never defer safety invariants for retained flows: role/object authorization, maker-checker, immutable quotes, deadline, lock hierarchy, actual cash verification, economic deduplication, unknown-state protection, traceability, exact entitlement and loss accounting. A deferred advanced exception path must fail safely and remain visible; never claim unsupported resolution. Preserve all approved BD/FD; priority is delivery scope, not policy replacement. No arbitrary estimate of hours or guaranteed grade without constraints.

## 5.3 Proposed module scope
| Module | Priority | Scope | Requirement anchors | Relative complexity |
|---|---|---|---|---|
| Identity and authorization | Must | Login, fixed roles, distinct maker/checker, verified simulated accounts, service identities, audit; seed staff, basic UI | F001–020 | Medium |
| Registration and issuance | Must | Existing loan selection, canonical collateral, eligibility, immutable approvals, one issuance, sanitized disclosure | F021–040 | High |
| Primary market | Must | Manual quantity, server price, Quote+lock, accept one Order, direct payment, settlement, safe expiry and exception | F041–060, F121–126 | High |
| Secondary market | Must | Fixed-price partial fill, Listing Lock/Quote Lock, direct seller payment, former-owner claims, cancellation/relisting | F061–080, F121–126 | Very high |
| Mock banking and payment | Must | Prefunded accounts, atomic transfers, authoritative repayment classification and lookup, callback/reconciliation, no fabricated cash | F081–105 | High |
| Financial engine and payout | Must | Exact ACT/365 and ownership cutoff, Senior priority, receipt-backed distribution, claims/payables, safe payout retry | F106–120 | Very high |
| Credit risk and recovery | Must | Verified overdue/cure/default, public history, trading restrictions, actual recovery and costs, final loss, closure review | F127–150 | High |
| Investor portfolio | Must | Holdings and locks, actual cash, claims/payables, nominal-basis P&L and simple ROI, historical rights | F151–159, F162 | Medium |
| Staff operations and reconciliation | Must | Minimal role-scoped lists/status, exception detail, evidence-based actions, audit and conservation checks | F163–165, F167–168, F170 | High |
| Advanced analytics and XIRR | Should | Standard XIRR with FD-026 eligibility and reasoned N/A; additional charts optional; never substitute false return | F160–161, RD-039 | Medium |
| CSV and in-app notifications | Could | Scoped export and deduplicated notices; not source of truth; no separate email/SMS service required | F166/169 | Medium |
| External actual financial services and trading enhancements | Won't | Real KYC, actual money, smart contracts, wallet, origination, bidding/auction/order book, auto-invest, external calendar, real enforcement | BRD exclusions | Not estimated |

F denotes REQ-F. These ranges are coverage anchors, not individual priority assignments: e.g., F145 late recovery automation and advanced correction scope still require explicit triage; F002/F005 email workflow may use restricted local simulated delivery or be deferred with safe access boundaries. F114 terminal fractions is a policy blocker for any retained closure requiring rounding, not optional correctness. F040/F144 collateral release safety remains mandatory if reuse implemented; unsupported reuse denied. F084 payoff and F119 correction/excess scope need precise compatible terms before inclusion. No NF safety requirement removed; performance targets rebaseline only with recorded environment. Dashboard statistics can be minimal; actionable exception and reconciliation functionality cannot be omitted.

## Next
5.4 requirement-level Must/Should/Could/Won't assignment and dependency closure; 5.5 delivery milestones and feasibility using actual team/deadline/hours; 5.6 demo scenarios and acceptance gates; 5.7 consolidated scope review. No code, estimates of calendar duration, tests or benchmark results claimed. Review unresolved H-01–H-09 as relevant before implementation. No BRD/FD changes.
