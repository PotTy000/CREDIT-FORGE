# CreditForge — Business Decision Reference Audit

Date: 2026-11-27. Baseline: [BRD v1.0](./BRD.md), commit 0f7dd05dd65e21e53407ee611b3aaab0f37a4094. Research-first, adapt-second. Audit does not supersede confirmed decisions. Adopt = documented comparable mechanism; Adapt = analogous mechanism with material differences; Custom = project constraint or control without exact verified precedent; Reconsider = meaningful divergence or unresolved design. Sources below are official public documentation, some indexed extracts rather than full pages.

## Reference catalogue

- R1 [Mintos Notes creation](https://help.mintos.com/hc/en-us/articles/28939554356625-How-are-Notes-created), [final terms](https://help.mintos.com/hc/en-us/articles/5624165691921-What-are-Mintos-Notes-base-prospectuses-and-final-terms), [pooling](https://help.mintos.com/hc/en-us/articles/5619797246097-How-are-underlying-loans-pooled-together-in-a-Set-of-Notes), [retention](https://help.mintos.com/hc/en-us/articles/5602648341265-What-is-skin-in-the-game): loan-backed instruments and originator retention; separate issuers and pooled loans differ from CreditForge.
- R2 [Centrifuge legacy](https://docs.centrifuge.io/getting-started/legacy/), [roles](https://docs.centrifuge.io/developer/security/pool-access-levels/): tranche priority and distinct permissions; pooled structures and roles differ.
- R3 [Goldfinch V1 overview](https://docs.goldfinch.finance/goldfinch/goldfinch-v1/goldfinch-overview), [default](https://docs.goldfinch.finance/goldfinch/goldfinch-v1/protocol-mechanics/default-process): priority of due Senior obligations, junior capital, recovery; investor-funded loans differ.
- R4 [Mintos secondary sale](https://help.mintos.com/hc/en-us/articles/7221077647633-How-to-sell-loan-investments-on-the-Secondary-market), [overdue funds](https://help.mintos.com/hc/en-us/articles/31119284582929-What-are-overdue-funds), [interest allocation](https://help.mintos.com/hc/en-us/articles/360001361917-How-is-interest-divided-between-the-seller-and-buyer-of-an-investment-on-the-Secondary-Market): fixed-price sale, eligibility, ownership-period interest. Sale article indexed extract excludes late/overdue positions; direct open cache miss. Lender remittance delay distinct from borrower overdue. Interest article opened in full, describes seller receiving unpaid accrued interest for listing-to-sale interval and buyer accrual from purchase date.
- R5 [Maple defaults and impairments](https://docs.maple.finance/legal/syrupusdc-and-syrupusdt-defaults-and-impairments): distinction between impairment and default, cure and recovery.
- R6 [Adyen payout approval](https://docs.adyen.com/payouts/payout-service/pay-out-to-bank-accounts/approve-cancel-payouts), [payout webhooks](https://docs.adyen.com/payouts/payout-service/pay-out-to-bank-accounts/payout-webhooks/), [verified payout destination](https://docs.adyen.com/marketplaces/custom-payouts/on-demand-payouts): approval, verification and eligible retries; not a loan servicing workflow.
- R7 [Coinbase matching engine](https://docs.cdp.coinbase.com/exchange/concepts/matching-engine), [cancel order](https://docs.cdp.coinbase.com/api-reference/exchange-api/rest-api/orders/cancel-an-order): full matching-engine page confirms self-trade prevention and open remaining order quantity. Exchange order book differs from direct-payment fixed-price listings.
- R8 [Stripe direct charges](https://docs.stripe.com/connect/direct-charges), [checkout fulfillment](https://docs.stripe.com/payments/checkout/fulfillment), [delayed payment events](https://docs.stripe.com/payments/existing-customers?platform=web&ui=stripe-hosted): direct charges on connected account and asynchronous completion; not direct bank-to-bank settlement or proof of no custody.

## Decision-by-decision audit

| BD | Classification | Reference and recommendation |
|---|---|---|
| 001-R1 | Adapt | R1 marketplace and originator separation; single issuer and multiple mock banks project scope. Keep. |
| 002-R1 | Adapt | R1/R2 existing asset data and issuer workflow; eligibility review is not underwriting. Keep. |
| 003 | Adopt principle | R4 buyer accrual from purchase and seller stops at sale. Keep; use Mintos as Phase 3.4 starting point for earned unpaid interest, including explicit treatment of pre-listing and overdue claims. |
| 004 | Adapt | R1/R3 loan-backed investment and borrower pools; one loan per product simplifies pooling. Keep. |
| 005 | Adapt | R1/R2/R3 retention and first-loss priority; bank retaining all junior and unsold senior is CreditForge adaptation, not exact copied structure. Keep. |
| 006-R1 | Adapt | R2/R6 role separation and payment approval analogues; exact dual issuer plus independent marketplace approval unverified. Keep. |
| 007-R1 | Custom | Exclusive collateral project eligibility, avoids intercreditor priority complexity; not universal law. Keep. |
| 009 | Custom | Direct payment/no wallet project boundary; R8 analogy only. Keep. |
| 010 | Adapt | R1 loan originator external to investment marketplace; no borrower portal deliberate. Keep. |
| 011 | Custom | R8 connected-account charge differs from direct bank transfer; no proven equivalent for investor-to-investor sale. Keep with reconciliation risk. |
| 012 | Adapt | R6 payout mechanics; automatic issuer bank payout is project adaptation. Keep. |
| 013 | Custom | Manual external receipt entry due to no bank integration. Keep. |
| 014 | Adapt | R2 role separation; combined marketplace role limits scope. Keep. |
| 015 | Custom | One issuance/offering avoids top-ups and overlapping vintage rights. Keep. |
| 016 | Custom | One collateral per loan simplifies recovery. Keep. |
| 017 | Adopt principle | R1 final terms use transferred outstanding principal; exact eligible balance and full-basis issuance remain FD-005. Keep. |
| 018 | Adapt | R7 partial order filling; fixed-price listing and external payment reservations require adaptation. Keep. |
| 019 | Adapt | R6 approval principle, not exact loan receipt maker-checker. Keep. |
| 020 | Reconsider | R4 sale eligibility excludes late/overdue positions in indexed current guidance; lender remittance delay policy differs. Existing ALLOW remains confirmed; propose explicit owner review only if aligning more closely with Mintos. |
| 021 | Custom | R8 checkout/fulfillment analogue; no exact offering-close policy verified. Keep. |
| 022 | Adapt | R7 cancel open remainder analogy; protected external payment reservation is custom. Keep. |
| 023 | Adopt principle | R7 explicit self-trade prevention. Keep; does not prevent multi-account collusion. |
| 024 | Custom | R7 cancellation exists; no evidence requiring cancel/relist on price change for Mintos. Keep simple cancel/relist; no compelling reason to reopen. |
| 025 | Custom | R6 verified account destinations; immutable instruction snapshot is local integrity rule. Keep. |
| 026 | Adapt | R5 credit status distinction; dual approval custom. Keep. |
| 027 | Custom | Risk acknowledgement local disclosure control; not verified identical external workflow. Keep. |
| 028 | Custom | Material change reapproval local governance rule. Keep. |
| 029 | Custom | R8 asynchronous fulfillment analogy only; risk revalidation local rule. Keep. |
| 030 | Custom | 10-minute duration not industry standard; unknown payment protection local. Keep. |
| 031 | Adopt principle | R6 retry eligible failed payouts after resolving account issues; require definitive nonpayment and same obligation. Keep. |

Superseded BD-006, BD-007, BD-008 remain superseded. No new approved decision.

## Handoff

Retain current business baseline unless owner explicitly revises BD-020. BD-003 is supported by Mintos loan/Notes interest allocation; distinguish its bond clean/dirty pricing model and resolve pre-listing/overdue unpaid interest, cutoffs, partial cash and payout rights in 3.4. Do not conflate different products. FD-001–005 remain pending, with Goldfinch Senior-first candidate subject to full waterfall consistency. Research does not validate exact formulas or legal compliance. Prioritize reference mechanisms first and only request decisions on material divergences. No contract audit; source extracts insufficient to prove exact operational rules, and no new real bank integration assumed.
