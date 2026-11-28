# CreditForge — MVP Scope & Prioritization

**Version:** 1.0 — Phase 5 working scope baseline; design handoff
**Baseline:** [Requirements Specification v0.11](./REQUIREMENTS_SPECIFICATION.md) (blob 56a2de6e2f5dd6b9d6147e8472e43175052bc213); BRD v1.3; Financial Rules v1.1.
**Status:** working scope accepted provisionally by owner; not implementation acceptance or blanket approval of all RD details. Solo developer; target two months. Exact deadline, available hours, hardware, budget and rubric unknown.

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

## 5.4 Requirement-level prioritization (proposed)

### Constraints
Owner confirmed one developer, target two months, implementation order Database -> Backend -> Frontend -> final testing/deployment. Testing throughout; database-first is sequencing, not a frozen schema or chosen ORM. Do not finalize weekly implementation plan or architecture here. Hours, deadline date, grading rubric and hardware remain unknown. The current prioritization is ambitious, not a demonstrated feasible estimate. Must is a minimum coherent scope candidate, not promise to deliver all within target.

### Assignment
Every requirement in v0.11 is assigned exactly once below. Must means minimum behavior, including all safety constraints of the retained workflow; simplify presentation rather than semantics. Should means defer only with stated safe limitation; Could is optional; Won't covers excluded capabilities outside the existing 200 requirements, no existing requirement deleted. Conditional requirements are not silently declared implemented. All policies remain binding for supported operations.
| Type | Must | Should | Could | Won't | Total |
|---|---:|---:|---:|---:|---:|
| Functional | 162 | 6 | 2 | 0 | 170 |
| Nonfunctional | 26 | 4 | 0 | 0 | 30 |
| Total | 188 | 10 | 2 | 0 | 200 |

### Simplification and dependency closure
- F001–004: basic signup, local simulated email verification (restricted test delivery, no public bypass), login/logout; staff provisioning F006–007 minimal fixed roles and disable/revoke. F005 password recovery Should; if absent no password reset endpoint or insecure admin bypass. Seeded accounts support demo; actual email provider not required.
- F021–039: existing source and collateral selection, basic proposal preview and approval history, no forecast engine, no external valuation; retain eligibility/revalidation and exclusivity. F040/F144 Should as one linked release/reuse capability; if deferred permanently block reuse of prior assigned collateral within MVP, preserve evidence and historical links, do not claim release or support reuse. This is a proposed delivery limitation, not changing BD-007-R1.
- F041–080/F121–126: shared Quote, payment, settlement and exception mechanisms across markets; simple filters and tables, one class per order; fixed-price listing, partial fill, cancellation/relisting. F056/F078 refunds Must: minimal original-payee authorized transfer, verify actual status, no forced debit. F074 receiving account changes Must with versioned future instructions. Advanced correction automation not required, but F097/098/119 minimum evidence-backed correction and safe unresolved cases are Must; unsupported adjustment types held, no fabricated resolution.
- F081–120: source fixtures, authoritative repayment lookup and notification, funded mock accounts, full payoff preview, ordinary and partial/early eligible payments, precise accrual, payout. F114 remains Must with H-01 approval blocker, not silently deferred; if terminal fractional adjustment unresolved block final financial closure and display remaining obligation. H-02 price rounding and H-03 early/excess/charges must be settled for supported paths; no future coupon fabricated. No arbitrary balance top-ups. Minimal staff retry/reconcile workflow instead of generalized operations engine.
- F127–150: keep verified overdue/cure/default, public repayment/processing, recovery cost evidence, loss and closure. F145 late recovery after final loss Should; if unavailable retain frozen beneficiaries, capture actual external receipt/evidence as unresolved, no allocation, loss reversal, revived trading or false financial closure; if cash posts it must remain accounted for separately. No actual collateral auction. F040/F144 release deferred together only with reuse blocked.
- F151–159/F162–165/F167–168/F170: basic portfolio, dated cash, claims/payables, P&L/ROI, minimal filtered staff lists, status counts, exception detail and audit. F160–161 XIRR Should as inseparable pair; if omitted no XIRR figure, no substitute rate, and disclose feature not supported. F166 notification and F169 CSV Could; current status and histories remain Must, privacy/export safeguards mandatory if export added.
- NF001–020/NF025–030: retain security, integrity, recovery, backup/restore, observability, bounded resource usage, basic accessibility, reproducible setup and critical automated tests. Proposed numeric cap/coverage/recovery targets not implicitly approved; calibrate in later design. NF021–024 Should as quantitative benchmark profile only; no skipping correctness concurrency tests NF011 or reporting measured performance honestly. At minimum measure basic latency and report limitations; do not claim SLA.
- Dependency summary: identity and source records -> issuance and holdings -> funded bank transfer + shared Quote/Order/settlement -> secondary ownership intervals -> servicing/financial engine and payout -> risk/recovery -> portfolio/operations; cross-cutting invariants and tests throughout. These are business dependencies, not calendar schedule. Database/backend/frontend ordering applies across implementation.

### Complete requirement priority register
Descriptions below retain original trigger/requirement titles; normative details and AC remain in Requirements Specification v0.11. No new functional behavior inferred from shorthand.
| ID | Priority | Requirement / trigger |
|---|---|---|
| REQ-F-001 | Must | Visitor registers with valid unused identifier |
| REQ-F-002 | Must | User proves control of registration email |
| REQ-F-003 | Must | Active user signs in |
| REQ-F-004 | Must | Authenticated user logs out or session expires |
| REQ-F-005 | Should | User requests password recovery |
| REQ-F-006 | Must | Active Admin provisions staff |
| REQ-F-007 | Must | Admin changes role/disables account |
| REQ-F-008 | Must | Any protected operation |
| REQ-F-009 | Must | Investor reads/changes own data |
| REQ-F-010 | Must | Staff reads business data |
| REQ-F-011 | Must | Officer submits, Approver reviews |
| REQ-F-012 | Must | Operator reviews issuer-approved offer |
| REQ-F-013 | Must | Sensitive action attempted |
| REQ-F-014 | Must | Investor submits receiving account |
| REQ-F-015 | Must | Verified Investor sets/replaces destination |
| REQ-F-016 | Must | Investor attempts dependent trade or payout setup |
| REQ-F-017 | Must | Integration submits payment or repayment fact |
| REQ-F-018 | Must | Authorized actor operates demo simulator |
| REQ-F-019 | Must | User/service attempts sensitive action |
| REQ-F-020 | Must | User views account/portal |
| REQ-F-021 | Must | Officer selects existing source loan |
| REQ-F-022 | Must | Officer creates/updates registration draft |
| REQ-F-023 | Must | Officer associates collateral |
| REQ-F-024 | Must | Registration/submission/issuance checks |
| REQ-F-025 | Must | Officer validates loan eligibility |
| REQ-F-026 | Must | Officer enters tranche terms |
| REQ-F-027 | Must | Officer previews proposal |
| REQ-F-028 | Must | Officer prepares disclosure |
| REQ-F-029 | Must | Officer submits complete eligible draft |
| REQ-F-030 | Must | Distinct Approver reviews submitted version |
| REQ-F-031 | Must | Operator reviews issuer-approved version |
| REQ-F-032 | Must | Material source/terms change before issuance |
| REQ-F-033 | Must | Authorized workflow requests issuance |
| REQ-F-034 | Must | Valid final version and revalidation |
| REQ-F-035 | Must | Issuance successful |
| REQ-F-036 | Must | Duplicate/concurrent issuance or source repayment at boundary |
| REQ-F-037 | Must | Authorized staff inspects status |
| REQ-F-038 | Must | Visitor/Investor browses published assets |
| REQ-F-039 | Must | Issued asset terms correction or known collateral conflict |
| REQ-F-040 | Should | Verified release of collateral requested |
| REQ-F-041 | Must | Visitor/Investor browses published offerings |
| REQ-F-042 | Must | Investor selects offer |
| REQ-F-043 | Must | Investor enters quantity |
| REQ-F-044 | Must | Material principal update |
| REQ-F-045 | Must | Investor requests Quote |
| REQ-F-046 | Must | Competing Quote requests |
| REQ-F-047 | Must | Overdue or changed material disclosure before authorization |
| REQ-F-048 | Must | Buyer initiates payment within deadline |
| REQ-F-049 | Must | Payment notification/status query |
| REQ-F-050 | Must | Repeated payment events/requests |
| REQ-F-051 | Must | Checkout expires/cancel requested |
| REQ-F-052 | Must | Valid posted transfer and eligible reserved order |
| REQ-F-053 | Must | Payment confirmed but settlement fails |
| REQ-F-054 | Must | Late confirmation or changed risk after authorization |
| REQ-F-055 | Must | Operator handles mismatch or unresolved payment |
| REQ-F-056 | Must | Authorized refund required |
| REQ-F-057 | Must | Buyer views order |
| REQ-F-058 | Must | Authorized issuer/workflow closes offering |
| REQ-F-059 | Must | Loan default/recovery/maturity/full repayment or valid suspension |
| REQ-F-060 | Must | Operator/issuer views primary activity |
| REQ-F-061 | Must | Investor views owned position |
| REQ-F-062 | Must | Seller creates listing |
| REQ-F-063 | Must | Listing creation commits |
| REQ-F-064 | Must | Visitor/Buyer browses listings |
| REQ-F-065 | Must | Buyer selects listing |
| REQ-F-066 | Must | Buyer requests Quote |
| REQ-F-067 | Must | Buyer authorizes secondary payment |
| REQ-F-068 | Must | Payment fact received |
| REQ-F-069 | Must | Eligible paid Quote Lock settles |
| REQ-F-070 | Must | Settlement completes |
| REQ-F-071 | Must | Seller cancels listing |
| REQ-F-072 | Must | Seller changes asking price |
| REQ-F-073 | Must | Order expires/definitively unpaid |
| REQ-F-074 | Must | Verified receiving account changes |
| REQ-F-075 | Must | Principal material change |
| REQ-F-076 | Must | Credit risk change/default/maturity/zero rights |
| REQ-F-077 | Must | Paid order fails settlement or late payment arrives |
| REQ-F-078 | Must | Operator requests secondary refund |
| REQ-F-079 | Must | Seller/Buyer inspects trade history |
| REQ-F-080 | Must | Operator monitors secondary market |
| REQ-F-081 | Must | Authorized simulator operator selects loan |
| REQ-F-082 | Must | Source loan is inspected |
| REQ-F-083 | Must | Operator enters normal simulated repayment |
| REQ-F-084 | Must | Full settlement requested |
| REQ-F-085 | Must | Source posts receipt |
| REQ-F-086 | Must | Posted receipt notification |
| REQ-F-087 | Must | CreditForge receives event |
| REQ-F-088 | Must | Valid notification or reconciliation lookup |
| REQ-F-089 | Must | Receipt source identity checked |
| REQ-F-090 | Must | Source loan mapped |
| REQ-F-091 | Must | Verified normal post-issuance receipt |
| REQ-F-092 | Must | Receipt accepted |
| REQ-F-093 | Must | Source lookup unavailable |
| REQ-F-094 | Must | Expected receipt/event missing |
| REQ-F-095 | Must | Out-of-order/backdated/reversed fact |
| REQ-F-096 | Must | Material principal event pending |
| REQ-F-097 | Must | Officer reconciles exception |
| REQ-F-098 | Must | Distinct Approver reviews exception |
| REQ-F-099 | Must | Missing or late scheduled payment detected |
| REQ-F-100 | Must | Authorized staff monitors integration |
| REQ-F-101 | Must | Authorized demo fixture initialization |
| REQ-F-102 | Must | Authorized payment/account lookup |
| REQ-F-103 | Must | Authorized transfer request |
| REQ-F-104 | Must | Transfer failure/notification replay |
| REQ-F-105 | Must | Simulated borrower repayment or recovery |
| REQ-F-106 | Must | Authorized accrual evaluation |
| REQ-F-107 | Must | Ownership/principal event |
| REQ-F-108 | Must | Due schedule or final cutoff |
| REQ-F-109 | Must | Verified eligible interest receipt |
| REQ-F-110 | Must | Verified eligible principal receipt |
| REQ-F-111 | Must | Allocation posting |
| REQ-F-112 | Must | Principal allocation completed |
| REQ-F-113 | Must | Fractional allocation |
| REQ-F-114 | Must | Final residual resolution |
| REQ-F-115 | Must | External payable available |
| REQ-F-116 | Must | Payout attempt initiated |
| REQ-F-117 | Must | Payout confirmation |
| REQ-F-118 | Must | Definitive failed payout |
| REQ-F-119 | Must | Excess or correction requiring cash resolution |
| REQ-F-120 | Must | Investor/staff views distribution/payout |
| REQ-F-121 | Must | Buyer requests eligible transaction Quote |
| REQ-F-122 | Must | Buyer reviews Quote |
| REQ-F-123 | Must | Quote expires/cancelled before acceptance |
| REQ-F-124 | Must | Accepted Quote / Order deadline reached |
| REQ-F-125 | Must | Material change, closure or repricing |
| REQ-F-126 | Must | Buyer/staff views transaction |
| REQ-F-127 | Must | Schedule cutoff or verified receipt |
| REQ-F-128 | Must | Issuer Officer reviews risk |
| REQ-F-129 | Must | Distinct Issuer Approver reviews |
| REQ-F-130 | Must | Investor views asset/listing/Quote |
| REQ-F-131 | Must | Default/recovery/maturity/zero rights or valid suspension |
| REQ-F-132 | Must | Existing Quote/Order affected by risk |
| REQ-F-133 | Must | Confirmed accrual cutoff |
| REQ-F-134 | Must | Confirmed Default and authorized issuer |
| REQ-F-135 | Must | Authorized recovery simulation |
| REQ-F-136 | Must | Actual recovery costs/receipt |
| REQ-F-137 | Must | Officer submits recovery proposal |
| REQ-F-138 | Must | Distinct Approver accepts recovery |
| REQ-F-139 | Must | Approved eligible recovery receipt |
| REQ-F-140 | Must | Interim recovery allocation |
| REQ-F-141 | Must | Officer proposes final recovery completion |
| REQ-F-142 | Must | Distinct Approver confirms final loss |
| REQ-F-143 | Must | Credit/financial closure review |
| REQ-F-144 | Should | Verified external collateral release |
| REQ-F-145 | Should | Verified late recovery after final loss |
| REQ-F-146 | Must | Investor or scoped staff monitors case |
| REQ-F-147 | Must | Issuer registers compatible schedule |
| REQ-F-148 | Must | Source receives early/partial/normal payment |
| REQ-F-149 | Must | Monitoring/processing |
| REQ-F-150 | Must | Published asset/listing/Quote viewed |
| REQ-F-151 | Must | Investor opens portfolio |
| REQ-F-152 | Must | Investor views asset history |
| REQ-F-153 | Must | Confirmed bank flow |
| REQ-F-154 | Must | Unresolved trade or refund |
| REQ-F-155 | Must | Investor views cash summary |
| REQ-F-156 | Must | Investor views economic rights |
| REQ-F-157 | Must | Eligible investment performance |
| REQ-F-158 | Must | Financially resolved position |
| REQ-F-159 | Must | Portfolio aggregate |
| REQ-F-160 | Should | Eligible dated flows |
| REQ-F-161 | Should | Ineligible or invalid XIRR |
| REQ-F-162 | Must | Investor sees published asset |
| REQ-F-163 | Must | Issuer views dashboard |
| REQ-F-164 | Must | Operator views market dashboard |
| REQ-F-165 | Must | Staff investigates exception |
| REQ-F-166 | Could | Relevant investor/staff event |
| REQ-F-167 | Must | Staff reconciles asset |
| REQ-F-168 | Must | Investor or staff filters report |
| REQ-F-169 | Could | Authorized CSV export |
| REQ-F-170 | Must | Authorized audit view |
| REQ-NF-001 | Must | Server-side role/object authorization, deny by default |
| REQ-NF-002 | Must | Distinct maker-checker and immutable approval version |
| REQ-NF-003 | Must | Secure credentials/session/recovery |
| REQ-NF-004 | Must | Transport and secrets protection |
| REQ-NF-005 | Must | Server validation and injection/CSRF/CORS controls |
| REQ-NF-006 | Must | Authenticated source callbacks and replay defense |
| REQ-NF-007 | Must | Abuse controls and Quote anti-hoarding |
| REQ-NF-008 | Must | Privacy and safe errors/exports |
| REQ-NF-009 | Must | Dependency/secret review |
| REQ-NF-010 | Must | Atomic local financial state transitions |
| REQ-NF-011 | Must | Inventory/lock concurrency safety |
| REQ-NF-012 | Must | Idempotent commands and economic dedup |
| REQ-NF-013 | Must | Cash and rights conservation |
| REQ-NF-014 | Must | Exact money, precision and range safety |
| REQ-NF-015 | Must | Authoritative timestamps/order and expiry |
| REQ-NF-016 | Must | Durable accepted work and restart recovery |
| REQ-NF-017 | Must | Safe unknown/failure/retry and reconciliation |
| REQ-NF-018 | Must | Backup and verified restore |
| REQ-NF-019 | Must | Observability and correlation |
| REQ-NF-020 | Must | Health/degraded state |
| REQ-NF-021 | Should | Read latency |
| REQ-NF-022 | Should | Local write latency |
| REQ-NF-023 | Should | Asynchronous completion latency |
| REQ-NF-024 | Should | Baseline workload and error budget |
| REQ-NF-025 | Must | Bounded list/export resources |
| REQ-NF-026 | Must | Clear recoverable transaction UI |
| REQ-NF-027 | Must | Selected accessibility and responsive core flows |
| REQ-NF-028 | Must | Maintainable TypeScript and reproducible setup |
| REQ-NF-029 | Must | Automated critical-path verification |
| REQ-NF-030 | Must | Reproducible evidence and honest limitations |

### Open decisions and next
This cut defers optional breadth but retains a large core; actual effort remains unverified. If capacity insufficient, revisit whole optional capabilities and presentation scope, never remove integrity checks or quietly change approved economics. H-01/H-02/H-03/H-04/H-05 must be addressed for retained flows before coding; H-06/H-07 technical design; H-09 hours/rubric/hardware unknown. Review priorities with owner before declaring MVP final. Next 5.5 feasibility and milestone constraints, without detailed calendar plan until requested; 5.6 demo/acceptance; 5.7 review. No implementation or tests run.

## 5.4a Optional enhancement backlog

Owner requested explicit Should/Could backlog for spare capacity. Existing 200 requirement assignments and approved BRD/FD unchanged. EB identifiers are optional delivery enhancements, not new approved REQ-F or RD. Relative effort S/M/L is a qualitative comparison, not estimated hours. Rank is suggested implementation order after core acceptance, not a calendar plan. Implement each selected enhancement database -> backend -> frontend where applicable; reuse existing authoritative facts and authorization, avoid unnecessary schema changes. No enhancement may bypass safety controls or delay core tests and defense preparation.

### Should Have — recommended extensions
| ID | Enhancement | Existing anchors | Relative effort | Minimum acceptance / boundary |
|---|---|---|---|---|
| EB-001 | Historical portfolio charts: actual principal/interest receipts, acquisition/sale/refunds and net cashflow by month; principal exposure by asset/industry | F151–159, FD-024–025 | S | Totals reconcile to scoped actual cash; nominal rights separately labeled; no invented historical NAV |
| EB-002 | XIRR and return explanation | F160–161, FD-026, H-07 | M | Actual dated flows, eligible synthetic terminal only, N/A with reason; validate solver before release |
| EB-003 | Advanced marketplace filters and sorting: coupon, remaining term, price discount/premium, risk, availability; compare selected assets side by side | F038/041/064/150 | S–M | Only published sanitized data; current principal and risk version, no guaranteed return or inferred fair price |
| EB-004 | Investment lifecycle timeline and payout breakdown by asset/claim, from purchase through repayment and recovery | F120/126/146/152/162 | S–M | Trace actual effective events and separate payment/allocation/payout status; historical holder visibility scoped |
| EB-005 | Quantitative performance benchmark suite and reproducible results report | NF021–024 | M | Document fixture, environment, latency/error/throughput and failures; no production SLA claim |
| EB-006 | Forgot password with local simulated email delivery | F005 | S–M | Expiring single-use proof, generic responses, throttle, revoke sessions; no real email dependency required |
| EB-007 | Verified collateral release and reuse | F040/144, BD-007-R1 | M | Distinct approval, external evidence, no overlapping active assignment; until implemented block reuse |
| EB-008 | Late recovery after final loss | F145, FD-023 | L | Actual source cash, frozen original beneficiaries, priority/caps, maker-checker, net loss adjustment once; no reopening/accrual; resolve H-01/H-05 first |
| EB-009 | Expanded issuer/operator operational analytics: cohort Quote funnel, aged locks, payout failure and overdue aggregates | F060/080/100/163–165/167 | S–M | Explicit denominator/time window, scoped records, no false success or private investor details |

### Could Have — additional enhancements if time remains
| ID | Enhancement | Existing anchors | Relative effort | Minimum acceptance / boundary |
|---|---|---|---|---|
| EB-010 | CSV statement/report export | F169 | S | Same scoped filters and as-of totals, safe encoding/formula injection protection, masked data |
| EB-011 | In-app notifications and unread history | F166 | M | Dedup by event/recipient; scoped links; read state not financial state; no required email/SMS |
| EB-012 | Investor watchlist/favorites | New optional UI capability; F038/041/064 | S | Own saved published asset IDs only; no automatic Quote, lock or order |
| EB-013 | Read-only investment illustration calculator | FD-006/011/018/025, F027/043 | S–M | Explicit hypothetical amount/rate/dates and no default assumption; no forecast guarantee, no authoritative transaction price or actual allocation; no new YTM policy |
| EB-014 | Asset comparison and downloadable printable own transaction receipt (browser print) | F126/153/170 | S | Compare disclosed terms, not investment recommendation; receipt only actual verified states and own data, not legal bank receipt; comparison can reuse EB-003 |
| EB-015 | Demo scenario launcher and visual event timeline | F018/081/101, NF019/030 | M | Restricted demo control, isolated seeded fixture; ordinary source posting and transfer paths, never direct financial edits or production reset |
| EB-016 | Read-only reconciliation drill-down visualization | F165/167/170 | S–M | Display differences, cash/rights conservation and correlation references; no automatic balancing entry |
| EB-017 | UI polish: dark mode, saved filters, empty/loading/error states beyond core, responsive chart refinements | NF026/027 | S–M | Core accessibility and recoverable UI remain Must; no sensitive data persisted in unsafe client storage |

### Delivery guardrails
Suggested first optional picks: EB-001, EB-004, EB-003, EB-005 for presentation and demonstrable engineering; EB-002 if numerical policy and tests ready. EB-007/008 have greater domain impact and must not be rushed. All six existing Should functional IDs, four Should nonfunctional IDs and two Could functional IDs are represented; additional EB entries refine existing features or add explicitly optional capabilities. EB-003/014 overlap comparison and should reuse one implementation. No new blockchain, wallet, real banking, business calendar, loan origination, auction, auto-invest or real enforcement. No external source research claimed for these backlog suggestions; they derive from current project requirements and proposed presentation extensions. New feature details remain owner review; no changes to approved economics, no implementation or test claims.

## 5.5 Feasibility & Milestone Constraints

### Capacity and feasibility
Confirmed constraints: one developer, target two months, authored application/test code TypeScript, implementation sequence Database -> Backend -> Frontend -> final integration/deployment, tests throughout. No detailed calendar schedule requested yet. Actual available hours, deadline, experience with chosen stack, hosting resources and rubric unknown. 188 Must requirements represent behavior and invariants, not 188 independent features; no validated effort estimate. Feasibility remains conditional, especially financial precision, effective ownership intervals, settlement and exception paths. Do not describe two months as guaranteed.

| Domain | Relative complexity | Main delivery risk |
|---|---|---|
| Identity/authorization | Medium | Object scope, distinct approvals |
| Asset registration/issuance | Medium–high | Stale source facts, duplicate issuance/collateral |
| Primary | High | Overbooking, immutable quote and payment finality |
| Secondary | Very high | Nested locks, transfer cutoff, historical claims |
| Mock banking | High | Durable cash posting, callback loss, duplicate events |
| Financial engine | Very high | Exact fractions, allocation caps, effective-time ordering |
| Risk/recovery | High | Verified status vs processing, final loss and outstanding payouts |
| Portfolio/operations | Medium–high | Cash vs rights, privacy, exception evidence |

### Dependency and milestone conditions (not schedule)
| Milestone | Depends on | Exit evidence |
|---|---|---|
| M1 Data foundation | Approved scope and resolved relevant financial policy | Reviewed domain model, schema/constraints for retained flows, repeatable migrations and seed fixtures, exact types, explicit state boundaries |
| M2 Backend identity and issuance | M1 | Role/object and maker-checker tests; source-backed registration and single conserved issuance |
| M3 Backend cash and trading | M1/M2, funded payment source | Primary and secondary end-to-end via API, Quote/Order/Lock invariants, direct verified cash, settlement, expiry and concurrency tests |
| M4 Backend servicing and financial engine | M1–M3, source receipt contract | Ordinary/partial/early eligible payments, ownership interval accrual, principal/interest allocations and payouts, exact reconciliation, duplicate and crash tests |
| M5 Backend risk/recovery and portfolio | M2–M4 | Window/processing disclosure, approved risk events, recovery waterfall and final loss, actual cash/rights metrics and exception visibility |
| M6 Frontend integration | Stable core API contracts and backend acceptance | Role-scoped investor/issuer/operator/admin surfaces, checkout and financial disclosures; no client authoritative arithmetic |
| M7 Integrated MVP readiness | M1–M6 | Selected Must acceptance cases pass, reproducible clean setup and demo, backup/restore and failure recovery evidence, known limitations documented |

Dependencies describe business and verification order, not separate services or immutable schema. Financial engine model and accrual rules must inform M1 and M3 before trades are coded, even though full payout integration is tested in M4. Backend integration tests begin with each module, not postponed to M7. No architectural/library commitment here.

### Scope control
Reuse common primary/secondary Quote, transfer verification, settlement and reconciliation logic where semantics permit. Keep mock bank boundaries authoritative but do not require separate infrastructure. Prefer minimal role-scoped tables/forms to analytics dashboards; seeded funded fixtures and local simulated email; no real integration. Protect time for testing, documentation and defense; no optional EB work before critical core tests are stable. If capacity is insufficient, revisit whole capability scope with owner rather than bypassing authorization, exact accounting, locks, idempotency, unknown-payment handling or traceability. Do not mark unresolved fractional closure, payment, refund or recovery as complete. H-01 terminal rounding, H-02 price rounding, H-03 early/excess/payment classification, H-04/05 reconciliation/correction must be resolved for supported paths; H-06 design details deferred; H-07 relevant only if XIRR selected. H-09 partially resolved (team and target duration), exact hours/rubric/hardware still open. No new BRD/FD/RD decisions. No code, tests or benchmarks executed.

## 5.6 Demo Scenarios & Acceptance Criteria

### Scenario design
Use independent deterministic seeded scenarios, not a single contradictory loan history. All figures illustrative VND; actual fixtures must satisfy approved contractual accrual, schedule, ownership and source balance rules. Check source cash, economic rights, historical claims and audit at each boundary; screen success alone insufficient. Seed via authorized isolated simulator only, no direct mutation to manufacture success. Use controlled simulation time under restricted capability for monthly periods; server authoritative timestamps and original Quote deadline still enforced, no arbitrary user backdating. Scenario IDs DS are acceptance groupings, not new requirements or executed tests. Final demo presentation length and rubric unknown; full acceptance suite broader than live demonstration.

### Business scenarios
| ID | Scenario and setup | Expected acceptance / observable evidence | Anchors |
|---|---|---|---|
| DS-001 | Register existing funded current loan with eligible collateral; Officer submits, Approver and Operator approve same version | Invalid/self/stale approval denied; exactly one issuance and offering; initial rights equal eligible principal; all units Bank initially, only Senior offered | F021–040, BD-006-R1/015/017/028 |
| DS-002 | Primary purchase: fixture principal 100m, Senior80m/Junior20m, denomination100k, 800 Senior/200 Junior; Investor A buys100 Senior at par | Quote and lock created, no Order before acceptance; one Order; 10m actual cash A->Bank, 100 units only at settlement, Bank700 Senior/200 Junior, supply unchanged | F041–060/121–126, FD-005/010/011 |
| DS-003 | Continue DS-002 before any principal change: A lists60 units at95k; B buys40 | Listing Lock60, free40, Quote Lock40 subset; after settlement A60 total/20 listed/40 free, B40, Bank700 Senior, 3.8m B->A; remaining listing20; cancel releases remaining20 only, old earned interest retained by A | F061–080, FD-012/013 |
| DS-004 | Independent ordinary source fixture D800k, contractual P100m, R1m, eligible partial principal payment (FT-055) | Funded borrower->issuer transfer1m, classified interest800k/principal200k, contractual P99.8m; Senior-first principal allocation; coupon claims derived from real fixture, no arbitrary split; accepted once automatically, receipt-backed external payouts, Bank allocations internal | F081–120, FD-014–018, FT-055/058 |
| DS-005 | Independent due-obligation fixture window5–9, due60m; payment8 verified12 vs payment12; partial20m by9 and40m on12; source outage; early unearned interest | On-time vs late3 calendar days, processing distinct; shortfall40m after cutoff; unknown not presumed late/default; no premature future interest allocation, no holiday shifting; public sanitized history and last verification | F127–130/147–150/162, FT-103–108 |
| DS-006 | Verified overdue while otherwise eligible, then confirmed default using compatible grace terms | Overdue trades permitted with explicit acknowledgement, default stops new Quotes and ordinary accrual at effective cutoff; paid/unknown locks protected; no immediate final loss; separate maker-checker review | F127–133, BD-020/026/027/029, FT-060–062/074 |
| DS-007 | Independent distressed fixture remaining Senior4.8bn/Junior1.2bn, verified gross4.2bn, actual costs0.2bn, due Senior ordinary interest0.1bn, compatible source components; external investors hold60% of Senior rights at cutoff | Net4bn, Senior interest0.1bn, Senior principal3.9bn, Junior principal0; remaining S0.9bn/J1.2bn. Interim collection not final loss. After approved exhaustion: loss J1.2bn/S0.9bn, total2.1bn. External Senior principal2.34bn/loss0.54bn, Bank principal1.56bn/total loss1.56bn (0.36bn Senior +1.2bn Junior); interest allocated by actual unpaid claim weights, not 60% assumption. No payout for loss; verify actual funds and cost transfer, unresolved payouts separate | F134–146, FD-021–023, FT-068–071 |
| DS-008 | Independent portfolio vector FT-075: acquisition10m, principal payout2m, interest payout0.4m, sale3m, remaining rights4.8m; then unpaid allocated0.2m | Net cash -4.6m, indicative P&L0.2m, ROI2%; unpaid increases P&L to0.4m but not cash; actual payout moves payable to cash once; former holder claims remain visible; loss not duplicate outgoing cash | F151–159, FD-024/025, FT-075–078 |
| DS-009 | Healthy full payoff on separate loan; compatible earned final stub, arrears and principal, actual classified source cash | Preview source revision/amount, verify complete receipt, remaining eligible rights zero, no future coupon/new Quotes; pending payouts and residual fractions visible, financial closure only after obligations resolved or authorized exception; no false collateral release | F084/106–120/133/143, FD-002/023/029, H-01/H-03 |

DS-002/003 are a connected marketplace fixture; DS-004–009 are independent financial vectors unless a later fixture design proves a coherent combined timeline. DS-007 amounts are from FT-068 and the 4.7 worked example, not a collateral valuation or assumed recovery guarantee. Final closure with fractional amounts remains blocked pending H-01 policy; no fixture selection used to hide unsupported rounding.

### Technical scenarios
| ID | Scenario | Expected acceptance | Anchors |
|---|---|---|---|
| DS-010 | 100 distinct eligible Buyers each request100 last available units simultaneously; repeat per NF011 | Exactly one successful lock if no other failure, no oversell/negative inventory; expected conflict responses recorded | F046/123, NF010/011 |
| DS-011 | 20 repeated same Quote accepts, same payment command, duplicate settlement, same receipt economic ID with distinct delivery IDs, payout callbacks | One Order, one debit/credit, one ownership transfer, one allocation and payout effect each; request-key payload mismatch rejected | F050/089/111/117/122, NF012 |
| DS-012 | Last-unit Quote expires concurrently with acceptance/authorization; accepted at minute2, payment unknown at minute10 | Original deadline unchanged; safe mutually exclusive acceptance/release, no new authorization at cutoff; unknown/paid lock retained, no replacement buyer displacement | F051/073/123–125, NF015/017 |
| DS-013 | Crash or callback loss after source transfer posts before settlement, restart | Query authoritative transfer, resume eligible settlement once or visible exception, no second charge; paid-unsettled status and protected lock throughout; record recovery evidence | F053–055/104, NF016/017 |
| DS-014 | Two concurrent60m debits against100m available; failed payout then verified new destination retry | At most one debit posts, no overdraft; failed payout remains payable; no retry while unknown; same entitlement, new verified destination snapshot only after definitive nonpayment | F103/116–118, NF013 |
| DS-015 | Tampered price, other investor Quote/portfolio access, forged source notification, self/stale approval, unauthorized simulator operation | All denied without financial mutation; safe errors, scoped audit evidence, no secrets | F008–019/087, NF001–009 |
| DS-016 | Out-of-order/backdated facts, source unavailable, material principal change before/after payment authorization, refund requiring original payee authorization | Unverified/exception states explicit, affected Quote restricted or replaced safely, authorized cash reconcile; no forced debit, no destructive history or automatic default | F054–056/075–078/093–099/132, NF017/020 |
| DS-017 | Exact fraction/rounding, ownership boundary, property-based financial conservation, backup/restore isolated and reconcile | No silent dust, overpayment, duplicated claims or lost acknowledged facts in tested process crash; approved rounding policy required for terminal cases; restore reconciled before financial writes | FD-027–030, NF014/016/018/029 |

### Live defense vs acceptance
Recommended live narrative: DS-001 -> DS-002 -> DS-003 -> DS-004 (switch to labeled fixture if independent) -> DS-005/006 -> DS-007 -> DS-008; show DS-010 and DS-013 as standout engineering demonstrations. DS-009 healthy closure and other technical cases can be shown through reproducible automated test evidence rather than all live. Actual time allocation deferred until defense constraints known. Evidence should include initial fixture and source balances, request/response IDs, effective/recorded timestamps, ownership/lock counts, exact allocation and payouts, final reconciliation, negative tests and actual measured outcomes. No staged UI-only or manufactured DB success.

### MVP exit acceptance
1. Every retained Must requirement implemented and mapped to observable test or review evidence; all selected critical financial, concurrency, auth and failure-path tests pass. Scenario matrix is a minimum cross-domain demonstration, not exhaustive substitute for 188 Must requirements or all applicable FT vectors.
2. Business and technical scenario outcomes verified against source and database facts; no negative inventory, duplicate economic effects, unauthorized change, unexplained cash/rights difference, false success or unresolved critical correctness defect.
3. Pending/unknown/unsupported cases remain visible and safely blocked; required unsupported path is reported incomplete, not marked passed. No unresolved exploitable high/critical security finding.
4. Repeatable clean setup, isolated seeded fixtures, documented test commands, reproducible evidence and backup/recovery check. Frontend core roles integrated, clear risk and processing states. Optional enhancements not prerequisites; benchmark numerical targets remain proposed and scope-dependent.
5. H-01/H-02/H-03/H-04/H-05 resolved for retained paths before declaring complete; any scope change requires explicit review. No implementation or tests executed in Phase5. Database-first/backend/frontend order unchanged. No detailed calendar plan.

## 5.7 MVP Scope Review

### Scope acceptance
Owner agreed to retain the current MVP and 17 demo scenarios for now and subsequently provide other project documentation for development-method reference. Treat this as the working scope baseline for Database Design, not final approval of every RD, Handoff resolution, architecture choice, or implementation/test result. One developer, target two months; implementation order Database -> Backend -> Frontend -> integration/deployment, with tests throughout. Detailed calendar schedule deferred at owner request. Research-first, adapt-second: use relevant existing development practices, inspect supplied source documents when available, distinguish source mechanisms from CreditForge adaptations, avoid overengineering. No assumption that future reference material has been received or reviewed.

### Scope audit
Inputs: MVP v0.5 blob b0f222e0264477e0f5ede368d9ab83ff94ec632a; Requirements v0.11 blob 56a2de6e2f5dd6b9d6147e8472e43175052bc213; BRD v1.3 blob 203499b0845399e505cee08f297c014d481b8a6f; Financial Rules v1.1 blob c52c94841ff54a8ce9c5bbfc9dd38528e53d2c98.
Structural review: 200 unique priority rows exactly match the 200 requirements register IDs; 188 Must (162 functional, 26 nonfunctional), 10 Should (6 functional, 4 nonfunctional), 2 Could (functional). 17 enhancement items (9 Should, 8 Could) are a separate optional backlog, not additional approved requirements. 17 scenario rows (9 business, 8 technical), 7 milestone rows. No implementation, benchmark or test results claimed. Review of counts and scope alignment is not an exhaustive per-rule audit or verified feasibility estimate.
Retain core issuance, both markets, shared Quote-first settlement, funded simulated banking, financial engine, payout, verified repayment transparency, risk/default/recovery, portfolio and reconciliation. Optional features are not prerequisites; deferred collateral reuse blocked, late recovery recorded unresolved, XIRR not displayed if unsupported. No external banking, real money, borrower portal, blockchain/wallet, holiday API or calendar adjustment. Protect financial invariants and test evidence. Existing BD/FD bind; no new policy introduced.

### Database Design handoff
Phase 6 starts with 6.1 Domain Modeling: derive bounded responsibilities, vocabulary, business identities, ownership, economic events and lifecycle distinctions from current BRD/FD/requirements/MVP before choosing physical tables. Then 6.2 Conceptual Data Model; 6.3 Logical Data Model; 6.4 ERD; 6.5 Physical Database Design; 6.6 Constraints & Indexes; 6.7 Data Integrity & Transaction Strategy; 6.8 Database Design Review. These are proposed document sections, not a frozen design; overlap/iteration allowed. No ORM/schema/architecture commitment in this scope review.
Design must explicitly separate source loan and issued economic rights, contractual repayment window and actual effective/processing times, ownership and locks, Quote/Order/Payment/Settlement, cash and claims, allocations and actual payouts, default and final loss, approvals/version history and audit. Model historical facts and stable identifiers; validate design against DS-001–017 and applicable FR/FT before physical implementation. Consider optional backlog only when its data dependencies materially affect retained core; do not build speculative tables. Resolve H-01 terminal fractional adjustment, H-02 fractional Primary price, H-03 early/excess/payoff classification, H-04 finality and refund, H-05 correction authority before committing affected model/implementation decisions; H-06 exact types/time/order/transactions in design; H-07 solver only if XIRR selected. H-09 actual hours/hardware/rubric still unknown. No phase calendar schedule or technology selection beyond owner TypeScript constraint.
Phase 5 complete as working scope specification; Phase 6 authorized as next design stage. Requirements v0.11 remains consolidated draft with individual RD details pending; scope baseline acceptance does not convert those into confirmed policy. No BRD/FD/Requirements changes in this commit. Future source-driven revisions tracked with reasons and impact, not silent edits.

## Next
Phase 6 — Database Design, starting with 6.1 Domain Modeling. Detailed implementation plan deferred.
