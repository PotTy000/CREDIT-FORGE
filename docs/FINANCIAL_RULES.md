# CreditForge — Financial Rules Specification

**Version:** 0.1 — Draft for Review  
**Phase:** 3 — Financial Rules Specification  
**Approved business baseline:** [BRD v1.0](./BRD.md), commit 0f7dd05dd65e21e53407ee611b3aaab0f37a4094  
**Coverage:** 3.1 Loan Principal & Repayment Schedule; remaining sections pending.

Financial Decision (FD) identifies a policy choice; Financial Rule (FR) specifies an invariant or calculation. Confirmed business decisions remain binding. New financial candidates are proposed until explicitly approved. No actual money, underwriting, bank integration or blockchain. Examples are illustrative, not financial advice or legal conclusions.

## 3.1 Loan Principal & Repayment Schedule

### 3.1.1 Scope and inherited rules

BD-017/BR-051–052: registered loan already disbursed, current, positive eligible outstanding principal, prior principal repayment allowed if remaining schedule compatible. BD-015: one issuance and one primary offering. BD-028: material changes before issuance require new approval. BD-013/019: Officer records actual external receipts, distinct Approver verifies. BD-005: Bank retains Junior and unsold Senior; Senior pari passu; Junior first loss. BD-020/027/029: verified overdue trading allowed with warnings, acknowledgement and risk revalidation; default/recovery/maturity/closure stop new orders. BR-018/038: Investor accrues from completed settlement. Do not change external borrower obligations.

### 3.1.2 Principal concepts and opening snapshot

| Symbol / concept | Definition |
|---|---|
| P_orig | Original disbursed loan principal |
| P_pre | Principal actually repaid before effective issuance |
| P_open | Verified outstanding principal at issuance |
| P_issued | Principal represented by issued rights |
| P_cash | Cumulative actual principal receipts applied to post-issuance rights |
| L_final | Cumulative recognized principal losses on those rights |
| P_rights | Remaining outstanding principal rights, excluding accrued interest |
| P_loan | Issuer-confirmed contractual borrower outstanding principal; not necessarily equal to P_rights after loss recognition or write-off |

For supported single-disbursement, no-capitalization loans without external principal adjustments: P_open = P_orig - P_pre. Verify against issuer snapshot; never infer external facts solely from CreditForge records. Inconsistent snapshot requires issuer review. Proposed full-basis issuance P_issued = P_open; selling less than all Senior does not change issued principal, since Bank retains Junior and unsold Senior. Eligibility, denomination and exact tranche quantities in 3.3. No previously repaid principal, capitalized unpaid interest or fees added silently.

Candidate conservation on issued economic rights: P_rights = P_issued - P_cash - L_final, with principal receipts and recognized losses nonnegative and never counted twice. P_rights >= 0; P_rights = remaining Senior rights + remaining Junior rights. Distinguish loan contractual balance from economic rights after final loss. Cash receipt confirmation and investor payout are separate events; an unpaid payout does not restore outstanding principal. Post-recovery excess or later recovery after recognized loss requires explicitly specified policy in 3.6.

Opening snapshot: original principal, prior principal repaid, verified outstanding, disbursement and effective issuance dates, maturity, remaining contractual schedule, borrower interest rate, most recent repayment, pre-issuance accrued/unpaid interest identified separately, external loan/receipt references, approval version. A principal or material schedule change before issuance invalidates approvals (BD-028). Pre-issuance cash and accrued claims are not silently included in investor rights. Allocation of receipts spanning issuance boundary belongs in 3.2/3.4/3.5.

Example: VND 6bn disbursed; VND 2bn principal repaid before issuance; P_open=4bn. Subject to eligibility, a full-basis issuance represents 4bn, not 6bn. Illustrative 80/20 split gives Senior 3.2bn and Junior 0.8bn; at nominal 100,000 VND, 32,000 and 8,000 units. These ratios and denomination remain unapproved.

### 3.1.3 Repayment schedule alternatives

| Model | Normal interest | Normal principal | Implications |
|---|---|---|---|
| Bullet | Monthly on outstanding principal | All at maturity | Stable principal per unit unless early repayment; simpler trading and reconciliation |
| Equal principal | Each period on reducing outstanding | Fixed principal instalments | Requires declining principal rights per unit, allocation among tranches, order repricing/disclosure, accrual boundary handling |
| Amortizing payment | Payment includes interest and principal | Variable principal component | Requires validated schedule and allocation, additional calculation and reconciliation |

Recommendation FD-001: admit remaining contractual schedules with monthly interest and bullet principal, regardless of historical principal repayments; reject incompatible schedules at registration, not modify external loan terms. Allow final stub interest period. Interest day count, due dates on non-business days and calendar conventions pending 3.2. Monthly schedule does not mean equal interest amounts. A single interest-only period before maturity can be supported if schedule matches approved convention.

Illustration: P_open 4bn, 6 months remaining. Bullet: principal due 0 during months 1–5, 4bn at month 6; interest separately due per contract. Equal-principal alternative: six principal instalments totalling 4bn (rounding remainder in final instalment), decreasing interest base. Choosing Bullet limits scheduled repayment structure, not eligibility of prior principal repayment.

### 3.1.4 Expected versus actual cash

Repayment schedule is planned obligations, never proof of receipt. Record schedule line date and expected principal/interest separately; actual receipt has unique external reference, effective receipt date, components, evidence and approval timestamp. Officer submits, distinct Approver approves (BD-019). Duplicate, invalid or unexplained allocation is rejected or routed to review. Actual interest payment may be full, partial, late or missing. Only accepted actual cash can trigger distribution. Unpaid amount remains an obligation under applicable financial rules, not investor wallet credit. Missing manual record triggers review; dual issuer confirmation required for overdue/default status. BD-020 remains ALLOW on verified overdue with warnings.

Principal balance update uses effective applied cash date, not payout date; exact retroactive receipt/cutoff behavior must be specified in 3.2/3.4 and detailed requirements. If late-entered evidence affects already settled transactions, preserve history and reconcile rather than silently rewriting completed trades.

### 3.1.5 Early principal repayment

Even a bullet schedule may permit early repayment under external contract. CreditForge cannot veto actual borrower repayment or discard received funds. Proposed support for full and partial unscheduled prepayment, distinct from scheduled amortization.

Full prepayment: Bank confirms actual receipt and principal component; stop new orders once principal fully repaid/early redemption confirmed; reconcile in-flight orders under BD-029, retain pending payout/refund obligations; accrue interest only on the eligible outstanding basis until the applicable cutoff; distribute principal and settle any remaining interest rights according to later waterfall. Do not automatically mark all payouts complete. Token retirement and closure are separate from cash payout (3.5/3.6).

Partial prepayment: compare (A) proportional reduction of remaining principal across all tranches and holders versus (B) Senior-first principal reduction. A preserves relative principal split and first-loss fraction; B reduces Senior before Junior, increases remaining Junior buffer fraction and changes bank economics. Neither is implied by junior first-loss rule alone, which concerns losses, not ordinary principal repayment. Proposed FD-003: proportional principal reduction across remaining Senior/Junior rights on normal principal receipts, not default recovery. Preserve equal treatment within Senior including bank-retained units.

Proposed FD-004: retain token unit count through partial normal principal repayments, reduce remaining principal per unit proportionally; original face value remains historical denomination, current principal per unit is the financial base and must be disclosed. Do not confuse nominal, remaining principal, asking price and market value. Full redemption/retirement policy in 3.5/3.6. Alternative pro-rata token retirement creates fractional-unit and lot rounding challenges.

Example: opening 4bn, Senior 3.2bn, Junior 0.8bn, 40,000 units at 100k original face. Partial principal receipt 1bn applied pro rata: Senior 0.8bn, Junior 0.2bn, remaining S=2.4bn and J=0.6bn; 40,000 units remain at 75k current principal each. If 50% Senior externally held, external Senior principal payout 0.4bn, Bank retained Senior 0.4bn and Junior 0.2bn. Sum =1bn; remaining rights 3bn. Future interest on remaining principal; no double payment of already returned principal. Allocation/rounding precision in 3.5/3.8. A holder who sells after principal reduction transfers remaining rights, not already-distributed principal. Price not automatically reset to remaining principal.

During a material principal change, pending orders need revalidation of disclosed current principal and consideration; do not silently complete at stale terms or cancel paid orders. Proposed to temporarily restrict new orders during processing of a material principal event until canonical balances and disclosure reconcile, then resume if otherwise eligible. This is not a blanket pause for overdue and is a proposed implementation control, not a confirmed new trading policy.

### 3.1.6 Financial rule register

| ID | Rule | Status |
|---|---|---|
| FR-001 | Issuance based on verified eligible outstanding principal; no prior repaid principal | Inherited BD-017 |
| FR-002 | Material opening snapshot change requires reapproval | Inherited BD-028 |
| FR-003 | Expected repayment is not receipt; only distinct approved external receipt drives allocation | Inherited BD-013/019 |
| FR-004 | Full-basis issuance represents opening principal; Bank initially owns all rights | Proposed clarification; FD-005 |
| FR-005 | Remaining issued principal rights reconcile to issued principal minus applied principal and recognized loss | Proposed invariant |
| FR-006 | Monthly interest and bullet principal remaining schedule only | Proposed FD-001 |
| FR-007 | Support confirmed full/partial unscheduled principal prepayment | Proposed FD-002 |
| FR-008 | Normal principal reduction pro rata across outstanding tranche and holder principal | Proposed FD-003 |
| FR-009 | Partial principal payment reduces current principal per unit; unit count unchanged | Proposed FD-004 |
| FR-010 | No new trades after full principal repayment; reconcile pending orders and unpaid obligations separately | Proposed detail; FD-002 |
| FR-011 | Actual principal applied and payout status separate; no double reduction or payout | Inherited BR-025/040–042; details pending |

### 3.1.7 Acceptance vectors (not executed)

| ID | Input | Expected |
|---|---|---|
| FT-001 | 6bn original, 2bn prior principal repaid, verified balance 4bn | Eligible basis 4bn |
| FT-002 | 6bn original, 2bn prior repaid, claimed balance 5bn, no adjustment evidence | Review; no issuance |
| FT-003 | 4bn approved, 1bn repaid before issuance | Reapprove new 3bn basis |
| FT-004 | Expected 4bn at maturity, no approved receipt | No cash distribution; verify status |
| FT-005 | Duplicate confirmed receipt reference | No second principal reduction/allocation |
| FT-006 | Proposed pro-rata early repayment example 4bn, 1bn received | 3bn remaining; Senior 2.4bn, Junior 0.6bn; 75k/unit with unchanged 40k units |
| FT-007 | Full prepayment, payout still pending | Zero remaining principal on accepted application; pending payable retained, no new trading under proposed policy |
| FT-008 | Payment approved after prior trade settled, effective date earlier | Reconciliation; no silent rewriting or duplicate disbursement |
| FT-009 | Verified overdue with positive principal and otherwise eligible listing | Trading remains allowed with risk acknowledgement (BD-020/027) |
| FT-010 | Receipt amount exceeds outstanding principal | Do not apply excess to principal; require classification/review |

### 3.1.8 Financial Review Gate F01 — proposed

| ID | Choice | Recommendation |
|---|---|---|
| FD-001 | Normal remaining repayment schedule | Monthly interest + bullet principal only |
| FD-002 | Early principal repayment | Support both full and partial actual prepayment; full repayment stops new trading, retain pending obligations |
| FD-003 | Normal principal allocation across tranches | Pro rata based on outstanding principal rights (not default loss/recovery priority) |
| FD-004 | Partial prepayment token handling | Maintain unit quantity; reduce remaining principal per unit; disclose before trading |
| FD-005 | Issuance principal basis | Full eligible outstanding principal, Bank retains unsold Senior and all Junior; do not confuse sold amount and issuance basis |

All five are proposed. FD-003/004 depend on FD-002 if partial prepayment permitted. Full-basis issuance is a proposed clarification of existing BRD's upper bound and bank retention, not a new confirmed decision. Early prepayment fee, interest cutoff, recovery ordering, precision, token retirement and exception handling remain unresolved. No real banking policy inferred.

## 3.2–3.8 Pending

- 3.2 Interest Calculation
- 3.3 Tranche & Tokenization Economics
- 3.4 Ownership & Accrued Interest
- 3.5 Repayment Waterfall
- 3.6 Default, Recovery & Loss Waterfall
- 3.7 Investment Performance
- 3.8 Financial Precision & Validation

## Revision history

| Version | Change |
|---|---|
| 0.1 | Start Phase 3 from approved BRD v1.0; draft 3.1 and Financial Review Gate F01 |
