# CreditForge — Financial Rules Specification

**Version:** 0.3 — Research-first interest calculation draft; F01 pending  
**Phase:** 3 — Financial Rules Specification  
**Approved business baseline:** [BRD v1.0](./BRD.md), commit 0f7dd05dd65e21e53407ee611b3aaab0f37a4094  
**Coverage:** 3.1 Loan Principal & Repayment Schedule; 3.2 Interest Calculation; remaining sections pending.

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

Partial prepayment: compare (A) proportional reduction of remaining principal across all tranches and holders versus (B) Senior-first principal reduction. A preserves relative principal split and first-loss fraction; B reduces Senior before Junior, increases remaining Junior buffer fraction and changes bank economics. Neither is implied by junior first-loss rule alone, which concerns losses, not ordinary principal repayment. Revised recommendation FD-003 after reference research: Senior-first normal principal reduction, with pro-rata allocation within each tranche, including Bank-retained Senior. Pro-rata across tranches remains an alternative. This is a proposed CreditForge rule, not a confirmed decision or exact reproduction of Goldfinch. Preserve equal treatment within Senior including bank-retained units.

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
| FR-008 | Normal principal reduction Senior-first across tranches, pro rata within each tranche | Proposed FD-003 |
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

### 3.1.8 Financial Review Gate F01 — proposed, revised after research

| ID | Choice | Recommendation |
|---|---|---|
| FD-001 | Normal remaining repayment schedule | Monthly interest + bullet principal only |
| FD-002 | Early principal repayment | Support both full and partial actual prepayment; full repayment stops new trading, retain pending obligations |
| FD-003 | Normal principal allocation across tranches | Senior First across tranches, pro rata within each tranche; revised recommendation; separate default recovery rules |
| FD-004 | Partial prepayment token handling | Maintain unit quantity; reduce remaining principal per unit; disclose before trading |
| FD-005 | Issuance principal basis | Full eligible outstanding principal, Bank retains unsold Senior and all Junior; do not confuse sold amount and issuance basis |

All five are proposed. FD-003/004 depend on FD-002 if partial prepayment permitted. Full-basis issuance is a proposed clarification of existing BRD's upper bound and bank retention, not a new confirmed decision. Early prepayment fee, interest cutoff, recovery ordering, precision, token retirement and exception handling remain unresolved. No real banking policy inferred.

### 3.1.9 External reference research — FD-001–005 (2026-11-27)

| FD | Official references and observed mechanism | CreditForge recommendation |
|---|---|---|
| FD-001 | [Maple Borrower Introduction](https://docs.maple.finance/maple-for-borrowers/introduction) describes periodic interest and principal at maturity. [Mintos amortization methods](https://help.mintos.com/hc/en-us/articles/7218968059281-What-amortization-methods-do-loans-have-on-Mintos) distinguishes full, partial, interest-only and bullet amortization. Mintos uses “bullet” for principal AND interest at maturity, so our term is more precisely monthly interest-only with bullet principal. | Retain monthly interest plus bullet principal only for scheduled payments. |
| FD-002 | [Maple Borrower Hub](https://maple.finance/borrower-hub) describes paydown through accepted refinance terms; [Goldfinch V1 Callable Deals](https://docs.goldfinch.finance/goldfinch/goldfinch-v1/guides/participating-in-callable-deals) discusses early principal payments; [Mintos Note repayments](https://help.mintos.com/hc/en-us/articles/5721195584145-How-do-Note-repayments-work) illustrates underlying cashflow pass-through. | Retain full and partial prepayment; do not import refinance or callable lender mechanics. |
| FD-003 | [Goldfinch V1 Borrowers](https://docs.goldfinch.finance/goldfinch/goldfinch-v1/protocol-mechanics/borrowers) prioritizes due Senior interest/principal before Junior. Mintos Notes illustrates proportional allocation within an investment structure, not priority between subordinated tranches. | Revise recommendation to Senior-first principal, pro rata within tranche. Exact ordinary cash waterfall and overdue interest priority pending 3.5. |
| FD-004 | [Mintos sink factor](https://help.mintos.com/hc/en-us/articles/5608575484049-What-is-the-sink-factor-of-a-Set-of-Notes) measures outstanding principal/original amount; [Mintos interest](https://help.mintos.com/hc/en-us/articles/25040259965457-How-is-interest-for-investments-in-loans-calculated) uses remaining invested capital. [Centrifuge legacy](https://docs.centrifuge.io/getting-started/legacy/) distinguishes asset rights and tranche shares; its pool NAV mechanics differ. | Retain unit counts for partial repayments, use separate Principal Factor per tranche. No inference about Mintos token counts. |
| FD-005 | [Mintos skin in the game](https://help.mintos.com/hc/en-us/articles/5602648341265-What-is-skin-in-the-game) retains part of underlying loan at originator; [Notes creation](https://help.mintos.com/hc/en-us/articles/28939554356625-How-are-Notes-created) describes receivable acquisition and indirect structures. | Retain full eligible outstanding principal representation internally, Bank retains Junior/unsold Senior. Mintos retention is not evidence of first-loss or full-basis issuance. |

Evidence: full official Maple introduction, loan management, borrower hub, Mintos amortization, repayment, interest, sink factor, skin-in-the-game and Notes creation, Centrifuge legacy pages retrieved. Goldfinch V1 borrower direct fetch failed unsupported Markdown; official indexed extracts support priority and Callable Deals prepayment statements. No contract audit; no claims that all products have identical rules.

### 3.1.10 Revised candidate formulas and examples

Let x be the confirmed principal component applied to normal outstanding rights, 0 <= x <= S+J. Senior repayment=min(x,S); Junior repayment=x-Senior repayment. Reduce each tranche by its own repayment; allocate within each tranche pro rata to remaining rights, including Bank. This is separate from loss allocation and does not decide priority between interest and principal in mixed receipts. For a 6bn loan (S=4.8bn,J=1.2bn) and 2bn normal principal receipt, Senior-first leaves S=2.8bn,J=1.2bn; Junior buffer 30%. Pro-rata leaves S=3.2bn,J=0.8bn; buffer 20%. If final net principal recovery subsequently equals 3bn (no other flows), 1bn loss: Senior-first Junior loss 1bn, Senior zero; pro-rata Junior loss 0.8bn, Senior 0.2bn. Total loss identical. Earlier Senior principal repayment reduces future Senior interest; Bank retains Junior exposure longer. Do not imply better investor outcomes in every scenario.

For each tranche T, proposed Principal Factor f_T = remaining principal rights / initial issued principal of T. With unchanged units N_T and original face F, current principal per unit = remaining principal rights / N_T = F × f_T. Use separate factors for Senior and Junior; do not round factor before allocation. If Senior becomes fully repaid while Junior remains outstanding, stop new trades of zero-principal Senior, close its offering/listings to new orders and resolve existing orders separately; preserve unpaid claims and payouts. This is a proposed extension needing review under FD-004, not an approved policy. Token retirement and final loss effects remain open. Example with 48k Senior units and 12k Junior units, 2bn Senior-first repayment: Senior remaining per unit 58,333.333... VND, Junior 100,000 VND. Exact rounding Phase 3.8.

Previous pro-rata examples/FT-006 remain alternative comparison vectors, not the recommended Senior-first policy. Add FT-011: 4bn opening (3.2bn Senior, 0.8bn Junior), 1bn normal principal receipt -> Senior 2.2bn, Junior 0.8bn, total 3bn; 32k Senior units at 68,750 VND remaining and 8k Junior at 100,000 VND. FT-012: Senior exhausted by principal repayment while Junior positive -> no new Senior trades under proposed zero-principal rule, preserve all accrued claims/payables. Both tests are proposed, not executed.

All FD-001–005 remain unconfirmed; research does not change BRD v1.0 or BD-020. FD-003 recommendation revised, FD-004 refined, other recommendations retained. 

## 3.2 Interest Calculation — research-first candidate

### 3.2.1 Verified reference mechanisms

Reviewed 2026-11-27; distinguish specific products, not universal conventions.

| Source | Verified mechanism | CreditForge adaptation |
|---|---|---|
| [Mintos loan interest](https://help.mintos.com/hc/en-us/articles/25040259965457-How-is-interest-for-investments-in-loans-calculated) (full page) | Daily simple accrual using actual days and denominator 360; subsequent interest uses remaining investment after principal repayment. | Adopt reducing principal and simple daily accrual; adapt denominator to 365 for simulated Vietnamese bank context. |
| [Vietnamese Deposit Insurance explanation of Circular 14](https://www.div.gov.vn/thong-nhat-cach-tinh-lai-huy-dong-va-cho-vay-tao-su-cong-bang-trung-thuc-hon-), [government summary](https://binhphuoc.gov.vn/vi/laws/detail/VE-QUY-DINH-PHUONG-PHAP-TINH-LAI-TRONG-HOAT-DONG-NHAN-TIEN-GUI-CAP-TIN-DUNG-GIUA-TO-CHUC-TIN-DUNG-VOI-KHACH-HANG-DO-NHA-NUOC-VIET-NAM-BAN-HANH-106/) (indexed text) | Annual rate on 365-day basis, actual balance and days. | Candidate ACT/365 Fixed for borrower and token coupons; only accept compatible mock contract terms. Not a legal compliance conclusion or claim the circular governs token rights. |
| [Goldfinch V1 Borrowers](https://docs.goldfinch.finance/goldfinch/goldfinch-v1/protocol-mechanics/borrowers) (indexed text) | Due Senior obligations precede due Junior obligations. | Reference for F01 Senior-first candidate and 3.5; does not prove identical ordering for every early principal receipt. |
| [Goldfinch Callable Deals](https://docs.goldfinch.finance/goldfinch/goldfinch-v1/guides/participating-in-callable-deals) (full page) | Separate callable product uses pro-rata waterfall and defers interest-base reduction for early principal repayment until scheduled principal due date. | Do not import callable loan product mechanics or lender recall; use ordinary reducing balance candidate instead. |
| [Maple borrower introduction](https://docs.maple.finance/maple-for-borrowers) (indexed text) | Recurring interest, principal at term end. | F01 schedule reference. |
| [Mintos secondary interest](https://help.mintos.com/hc/en-us/articles/360001361917-How-is-interest-divided-between-the-seller-and-buyer-of-an-investment-on-the-Secondary-Market) (full search extract) | Buyer accrues from purchase; seller stops at sale, with unpaid interest during listing-to-sale period paid when underlying repayment arrives. | Support BD-003; complete claim rights including pre-listing arrears in 3.4, do not assume source covers all cases. |

F01 remains unconfirmed. Research-first policy: use coherent reference mechanisms where compatible, document adaptations; do not equate Goldfinch callable product with two-tranche Borrower Pools.

### 3.2.2 Candidate interest basis

FD-006 proposed: fixed annual borrower rate and fixed annual Senior coupon; simple ACT/365 Fixed, no compounding of unpaid interest, no additional penalty/default coupon in MVP candidate. No claim of altering external loan contract; admission requires compatible terms, any incompatible receipt/charges classified separately and reviewed, not discarded. Distinguish contractual Borrower principal and issued rights after impairment or loss.

For constant balance B, annual decimal rate r and elapsed actual calendar days d: I=B*r*d/365. For changing balances, sum over nonoverlapping intervals. Denominator stays 365 in leap years. Monthly due schedule determines payment dates, not an assumption of 30 days per month. Rates on 360 and 365 basis are not interchangeable at the same nominal APR. Junior earns eligible residual cash under later waterfall rather than independently guaranteed coupon.

Borrower accrual uses contractual outstanding principal and borrower rate. Senior accrual uses remaining Senior rights and Senior coupon; holder accrual uses that holder's share of remaining Senior rights. Purchase price is not the interest base. Principal repayment is not interest. Actual receipt, accrual, approved distribution and confirmed payout remain distinct. No negative interest base. Stop ordinary accrual on repaid principal; default/maturity and unpaid interest after those events require 3.6 rules. Do not recognize forecast interest as received income.

### 3.2.3 Time boundary and effective facts

FD-007 proposed: use Vietnam business calendar dates (Asia/Ho_Chi_Minh), actual elapsed days, consistent half-open intervals for accrual; for principal, effective applied receipt date is boundary subject to approved simulated contract day-count convention. Use a consistent allocation cutoff between old and new holders, never credit a buyer for time before completed settlement (BD-003). Exact intraday settlement/date allocation, same-day multiple transfers, pre-issuance accrued claims, receipt backdating, business-day due date adjustment and historical unpaid interest must be resolved in 3.4 and 3.5. Do not silently round settlement back to start of day. If daily cutoff cannot faithfully represent ownership, use finer time segments or explicit eligible settlement policy; review before implementation. Late-approved effective facts trigger reconciliation, not retroactive destructive edits.

### 3.2.4 Worked examples (unrounded until presentation)

Example 1: 100m VND at 8% annual for 30 days: ACT/365 = 657534.246575... VND; ACT/360 = 666666.666667... VND. Difference arises solely from denominator at same annual rate.

Example 2: 6bn borrower balance at 12%, 30 days unchanged: 59,178,082.19178... VND. Senior 4.8bn at 8%: 31,561,643.83562... VND. If actual receipt equals fully paid borrower interest and eligible obligations as assumed, residual 27,616,438.35616... VND before costs; not guaranteed net Bank profit. This example does not approve waterfall priority.

Example 3: Senior First candidate, Senior 4.8bn, Junior 1.2bn, 2bn normal principal paid after 15 elapsed days in a 30-day period. Borrower base 6bn then 4bn: interest = (6bn*15+4bn*15)*0.12/365 = 49,315,068.49315... VND. Senior base 4.8bn then 2.8bn: interest = (4.8bn*15+2.8bn*15)*0.08/365 = 24,986,301.36986... VND. Assumes principal reduction effective at interval boundary, no default, full-basis issue, F01 proposed choices. Pro-rata alternative gives different Senior base, not different Borrower base.

Example 4: one Senior token with remaining principal 60k, purchased at 55k, coupon 8%, 30 elapsed days: accrued 394.5205479... VND; neither 100k original face nor 55k acquisition price is accrual base. Paid amount depends on actual receipts.

### 3.2.5 Proposed financial rules and test vectors

| Rule | Candidate |
|---|---|
| FR-012 | Simple actual-days /365 interest, fixed annual rate, no compounding; FD-006 pending |
| FR-013 | Split principal/accrual intervals at approved effective changes; no gaps or double counting |
| FR-014 | Senior interest on remaining principal rights, not purchase price or original face after repayment |
| FR-015 | Junior residual depends on available cash and approved waterfall, not separate guaranteed coupon |
| FR-016 | Expected/accrued/allocated/paid amounts distinct; no actual payout based solely on schedule |
| FR-017 | Calendar and effective-time convention explicit; no buyer accrual before settlement; FD-007 detailed cutoff pending |

| Test | Input | Expected (pre-rounding) |
|---|---|---|
| FT-013 | 100m, 8%, 30 days | 657534.246575... |
| FT-014 | 6bn borrower, 12%, 15 days before and after 2bn principal reduction | 49315068.49315... |
| FT-015 | Senior 4.8bn then 2.8bn, 8%, 15 days each | 24986301.36986... (F01 Senior-first candidate) |
| FT-016 | Current principal 60k, acquisition price 55k, 8%, 30 days | 394.5205479... |
| FT-017 | Zero days or zero remaining principal | zero ordinary accrual for that interval |
| FT-018 | 366 actual days, unchanged balance | use 366/365, not silently denominator 366 |
| FT-019 | Receipt not approved or payout fails | no fabricated cash; distinguish entitlement and confirmed payout |

All tests are specifications, not executed software tests. Exact monetary precision in 3.8.

### 3.2.6 Consolidated review approach

No additional gate solely for this section. F01 (FD-001–005) remains pending; FD-006 (simple ACT/365 Fixed, fixed rates, no compounding) and FD-007 (calendar/effective time boundaries, detailed ownership cutoff deferred) are draft recommendations. Present one consolidated review after 3.3 or earlier if owner requests. No confirmed FD or BRD decision altered. FD-006 penalty exclusion is only a candidate; default contractual charges treatment must be explicit in 3.6.

## 3.3–3.8 Pending

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
| 0.2 | Verify external reference mechanisms; revise FD-003 recommendation to Senior First; refine FD-004 with per-tranche Principal Factor; F01 still pending |
| 0.3 | Research Mintos daily accrual, Vietnamese 365-day convention and Goldfinch product differences; draft 3.2 and FD-006/007; F01 still pending |
