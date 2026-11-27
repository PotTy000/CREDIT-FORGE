# CreditForge — Functional & Non-functional Requirements

**Version:** 0.2 — Phase 4.1–4.2 draft
**Baseline:** [BRD v1.1](./BRD.md) (blob d20d7c8ffc1c5aec241bc724086c663cdf0514fa); [Financial Rules v1.0](./FINANCIAL_RULES.md) (commit 53cf4e48cc300014af721c848e5c463217824943); [Phase 3 Audit](./PHASE_3_AUDIT.md).
**Status:** inherited business/financial constraints binding; newly introduced identity/access workflow choices proposed pending consolidated review. Requirements not implemented or tested.

REQ-F identifiers denote functional requirements; REQ-NF denotes nonfunctional requirements (4.9); FR remains financial rule. AC is observable acceptance criteria. RD is a requirements decision, not a replacement for BD/FD. No real banking, identity verification compliance, borrower portal, loan origination, wallet, blockchain or actual money. Architecture, token/session implementation, schemas, libraries and UI deferred to Phase 6/7. MVP selection in Phase 5.

## 4.1 Actors, Authentication & Authorization

### 4.1.1 Research-first evidence

Official indexed documentation retrieved in this turn (2026-11-27):
- [Mambu users, roles and permissions](https://docs.mambu.com/docs/understanding-users-roles-and-permissions/) and [permissions](https://docs.mambu.com/docs/permissions/): assign granular permissions through roles. Adopt fixed role bundles initially, not arbitrary per-user grants.
- [Mambu staged process definitions](https://docs.mambu.com/api/api-v2/stagedprocessdefinitions/staged-process-definitions/): submitter cannot approve same definition. Adopt four-eyes principle for already approved CreditForge workflows; source concerns process definitions, not evidence of identical loan/recovery workflow.
- [NIST RBAC](https://csrc.nist.gov/projects/role-based-access-control/faqs) and [separation of duty](https://csrc.nist.gov/glossary/term/separation_of_duty): distinguish conflicting roles from runtime two-person checks.
- [OWASP Authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html), [Authentication](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html), [Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html): default deny, request-level authorization, identity/session controls and sensitive-action verification.
- [Stripe bank ownership verification](https://support.stripe.com/questions/bank-account-ownership-verification) and [bank account update](https://support.stripe.com/questions/how-do-i-update-bank-account-information): match owner/account and retain old destination for in-transit payout. Adopt analogous simulated verification, not Stripe integration, real KYC or compliance.
Evidence is indexed official excerpts, not source-code audit or verification of production bank regulations.

### 4.1.2 Actor inventory

| Actor / role | Scope | Exclusions |
|---|---|---|
| ACT-001 Investor | Own account, verified destination, eligible offers, own orders/listings/portfolio/claims | Other investors' private records; issuer/admin actions |
| ACT-002 Issuer Officer | Single issuer's loan registration, proposal submission, evidence, status/recovery/exception proposals | Self approval, marketplace approval, changing calculated entitlements |
| ACT-003 Issuer Approver | Review exact submitted versions and sensitive facts for issuer | Editing submitted facts, approving own work, marketplace publication |
| ACT-004/005 Marketplace Operator | Independent eligibility review/publication/suspension and settlement/payout exception operations | Issuer approval, underwriting, fabricating payment evidence, direct balance edits |
| ACT-006 System Admin | User provisioning, disabling access, fixed role assignment and permitted nonfinancial settings | Implicit financial permission, self-grant, approval/payment overrides |
| ACT-007 Auditor (optional per BRD) | Scoped read-only approval/financial evidence | Mutation or unrestricted sensitive data |
| ACT-008 Payment System | Authenticated simulated bank payment/payout facts and status queries | Human login/roles, issuer credit status |
| ACT-009 Mock Core Banking | Authenticated authoritative posted borrower receipts, transaction lookup | Marketplace ownership decisions; automatic credit status changes |
| Financial Engine | Internal authorized service executing approved rules | Human identity; arbitrary override |
| Visitor (proposed unauthenticated access state, not new business actor) | Sign-in/register and approved public offer summaries | Private data or transactions |

Three conceptual portals: Investor, Issuer (Officer and Approver), Marketplace (Operator/Admin; scoped auditor if included). Shared login possible; portal navigation derives from effective permissions; no portal UI is a security boundary. Borrower has no login. Simulator control is restricted to separately authorized demo operations, not implied by Investor or Admin role.

### 4.1.3 Authorization policy

Effective permission requires authenticated active identity, allowed role/capability, correct organization/resource ownership or assigned scope, permitted workflow state, and any separation-of-duty or sensitive-action proof. Deny by default. Stable internal user/principal identity rather than editable email/display name; stable investor economic owner identity independent of session. Proposed single operational role per human account; optional auditor dedicated read-only; no conflicting role assignments, no switching roles to approve own previous submission. Distinct person/account provisioning in simulation; two accounts of same person cannot be treated as meaningful real-world separation; no claim of detecting collusion. Admin cannot grant own financial capabilities or mutate financial records; privileged admin provisioning via controlled bootstrap outside public registration; no public admin creation. Role change/disable revokes prior access on next protected request, including previously issued sessions; existing economic rights, orders, payables and system servicing unaffected. Do not delete identities linked to history. Scope service credentials per integration operation, never reuse human login proof. Exact authentication mechanisms and credential rotation design in Phase 7.

### 4.1.4 Permission matrix

A=allowed subject to state/scope; O=own only; R=read scoped; P=propose; V=review/approve; dash=deny. Matrix is illustrative, requirements govern; no blanket access to all fields.

| Action | Investor | Officer | Approver | Operator | Admin | Auditor optional |
|---|---|---|---|---|---|---|
| Browse eligible offer disclosures | A | R | R | R | - | R |
| Buy/list/cancel own token | O | - | - | - | - | - |
| Own portfolio/receiving destination | O | - | - | - | - | - |
| Register loan/propose issuance | - | P | R | R | - | R |
| Internal issuer approval | - | - | V | R | - | R |
| Independent offering review/publication | - | R | R | V | - | R |
| Propose overdue/cure/default/recovery/correction | - | P | R | R | - | R |
| Approve sensitive issuer facts | - | - | V | R | - | R |
| Monitor/reconcile settlement and permitted retry/refund request | O (status only) | R | R | A | - | R |
| Manage accounts/fixed roles | - | - | - | - | A | - |
| View approval/audit evidence | O (own history) | R | R | R | R (access admin logs only) | R |
| Directly edit financial balances/mark paid | - | - | - | - | - | - |

### 4.1.5 Functional requirements

All listed requirements are draft specification; inherited constraints binding. Each row defines actor/trigger/precondition, behavior, exception and observable AC. Details of trade, correction, recovery, payment authorization and retries specified in later sections.

| ID | Actor / trigger / precondition | Requirement and exception | Source | Acceptance criteria |
|---|---|---|---|---|
| REQ-F-001 | Visitor registers with valid unused identifier | Create Investor only, unique normalized email, password and simulated profile; reject invalid/duplicate registration without exposing other accounts; never accept client role selection | ACT-001, UC-020; proposed RD-001 | Register valid new account -> Investor; supplied ADMIN role cannot grant privilege; duplicate cannot create second identity |
| REQ-F-002 | User proves control of registration email | Verify email via time-limited single-use challenge before transactional access; failed/expired challenge cannot activate; resend safely | RD-001, OWASP | Valid challenge verifies once; expired/replayed challenge rejected; unverified account cannot transact |
| REQ-F-003 | Active user signs in | Authenticate credentials, establish session, route authorized capabilities; generic invalid credential response, throttle repeated failures; disabled user denied | ACT-001–007, OWASP | Valid active user gains correct portal access; wrong password/unknown identifier denied; disabled user denied |
| REQ-F-004 | Authenticated user logs out or session expires | Invalidate session/access proof; protected requests require renewed login; logout does not cancel financial obligations | OWASP, BD-030/031 | Reuse revoked session rejected; pending payout remains |
| REQ-F-005 | User requests password recovery | Issue time-limited single-use recovery proof through verified email; generic response; reset revokes existing sessions; no bank credentials | RD-001, BR-044 | Expired/replayed proof denied; successful reset old password/session unusable; no account enumeration |
| REQ-F-006 | Active Admin provisions staff | Create/invite named staff account under fixed approved role and organization; no public staff registration; activation requires verified identity proof; no admin self-grant | BD-014, UC-020, RD-002 | Staff invite activates assigned role only; Investor cannot invite; Admin cannot assign own financial role |
| REQ-F-007 | Admin changes role/disables account | Validate permitted role/scope and conflicts; audit before/after; revoke obsolete access; preserve holdings/history/payout obligations; no destructive delete | UC-020, BR-045/049, RD-002 | Disabled user with old session denied; account history retained; conflicting roles rejected |
| REQ-F-008 | Any protected operation | Server checks active identity, role/capability, scope, object ownership and state on every request; deny missing permission independent of UI | BR-033/036/037/049, OWASP | Investor direct request for issuer approval denied; hidden menu bypass denied; unassigned resource denied |
| REQ-F-009 | Investor reads/changes own data | Scope orders, listings, claims, portfolio and destinations to stable owner identity; only approved counterparty disclosure visible; no arbitrary ID access | ACT-001, BR-018/060, UC-006/009 | Investor A cannot read/update B's private record by changing identifier; permitted own read succeeds |
| REQ-F-010 | Staff reads business data | Scope issuer/operator/auditor access by assigned organization and capability; field masking/minimum necessary access; Admin access logs only by default | ACT-002–007, BD-014, UC-021 | Admin cannot inspect investor private portfolio by default; auditor cannot write; issuer cannot approve marketplace publication |
| REQ-F-011 | Officer submits, Approver reviews | Enforce distinct maker/checker stable identity and exact version; return/reject with reason; prevent self-approval despite role changes; material edit invalidates prior approval | BD-006-R1/019-R1/026/028, BR-048/055/063/065 | Maker attempts approve -> denied; valid distinct checker approves same version; changed version needs reapproval |
| REQ-F-012 | Operator reviews issuer-approved offer | Independent operator approval only after valid issuer approval of same version; no authority to rewrite issuer terms or bypass maker/checker | BD-006-R1/014/028, BR-048/065 | Unapproved/stale version cannot publish; operator cannot substitute for issuer Approver |
| REQ-F-013 | Sensitive action attempted | Require fresh authentication proof for receiving destination change, password/privilege changes and high-risk staff approvals; bind financial payment consent separately to transaction snapshot; failed proof blocks action | BR-033/062/066, OWASP, RD-003 | Stale session cannot change destination until reauthenticated; changing checkout amount invalidates consent; login alone not bank payment approval |
| REQ-F-014 | Investor submits receiving account | Register simulated bank identifier/account reference/owner and request verification from trusted simulator; match stable simulated owner identity and active VND account; not just display name; no real PIN/OTP/password or CCCD images | UC-006, BD-025, BR-039/044/062, RD-004 | Own active matching simulated account verifies; wrong owner/nonexistent/closed account fails; raw bank secrets never requested |
| REQ-F-015 | Verified Investor sets/replaces destination | Proposed one active verified receiving destination per investor, retain versions/history; pending new verification leaves existing valid destination unchanged; future instructions only use newly activated verified version; no silent redirect | BD-025/031, BR-062/069, RD-004 | New account verified then activated; pending payout to old account retains old snapshot; unverified replacement cannot become active |
| REQ-F-016 | Investor attempts dependent trade or payout setup | Require active verified receiving destination before new buy or listing and recheck at relevant instruction creation; browse/own history allowed without destination; existing orders/payables reconcile rather than confiscate; invalid destination leaves payable outstanding | UC-006, BR-033/039/062/069, RD-004 | Unverified investor can browse but cannot create buy/listing; invalid account does not erase claims; retry only after terminal nonpayment and authorized new instruction |
| REQ-F-017 | Integration submits payment or repayment fact | Authenticate dedicated machine identity, restrict operation and issuer scope, verify authoritative source facts; reject human session as substitute, duplicate/replayed economic transaction cannot allocate again | BD-013-R1/019-R1, FR-003/037/063, BR-042 | Forged or wrong-scope event cannot post; repeated valid event produces one allocation; human Admin cannot mark paid |
| REQ-F-018 | Authorized actor operates demo simulator | Separate explicitly authorized simulator capability and simulated transaction history; never give investor/admin implicit power to fabricate CreditForge receipts; source must post valid simulated transaction before notification | BD-013-R1/019-R1, FD-018, RD-005 | Unprivileged user cannot simulate repayment; authorized simulation produces authoritative transaction then event; no direct creditforge balance edit |
| REQ-F-019 | User/service attempts sensitive action | Record actor, role/scope, target, action, version, time, outcome and reason where relevant; redact secrets and private fields; authorized scoped audit lookup; no in-place mutation of financial history | BR-026/045/070, UC-021, FD-029 | Approved/rejected self-approval and role changes traceable; auditor cannot alter logs; passwords/proofs absent from log |
| REQ-F-020 | User views account/portal | Show own profile, verification/access status, allowed navigation, reason for blocked transaction and safe remediation; never disclose unauthorized object existence or sensitive evidence | ACT-001–007, RD-001/004 | Unverified user sees verification path; staff sees allowed actions only; direct unauthorized operation still denied |

### 4.1.6 Proposed requirements decisions

| RD | Proposal | Basis/status |
|---|---|---|
| RD-001 | Investor self-registration with email/password, verified email and recovery; public offer summaries allowed, private/transaction access protected | New workflow proposal, OWASP reference; no real KYC |
| RD-002 | Admin-provisioned staff accounts, fixed role bundles, one operational role per person/account, controlled bootstrap, revoke access on role change | Mambu/NIST adaptation; inherited separation constraints binding |
| RD-003 | Fresh authentication for sensitive actions; separate transaction-specific bank authorization and payment confirmation | OWASP adaptation, BR-033/066; exact MFA/session duration and implementation later |
| RD-004 | Verify simulated bank ownership via trusted simulator, one active receiving destination with historical versions, verified destination before new buy/listing | Stripe analogy, BD-025/031; verification/eligibility additions proposed |
| RD-005 | Explicit restricted simulator operation capability, machine identities independent from human roles | Integration control refinement; exact assignment and deployment later |

No requirement to build real identity proofing, real bank OAuth, SMS OTP service, identity document storage, multi-tenant IAM product or dynamic permission editor. MFA for staff/security assurance, token lifetimes, password policy, recovery rate limits and measurable security targets will be specified in 4.9 and Phase 7; no production compliance claim. Existing approved BD/FD unchanged. RD-001–005 proposed and can be consolidated with later requirements decisions rather than force isolated approval.

### 4.1.7 Handoff and coverage

H-08/OI-011 role/account verification addressed at requirements draft level; collateral identity and disclosure freshness later sections. H-04/H-05 correction/payment authorization workflows remain 4.5–4.7. Requirements acceptance criteria are not executed tests. No architecture or schema choice finalized. Phase 4.2 next: existing loan registration, collateral identity/exclusivity, opening principal and compatible schedule, versioned approvals, one issuance and offering, token quantities and publication eligibility.

## 4.2 Credit Asset Registration & Token Issuance

### 4.2.1 Research-first evidence (2026-11-27)

Official indexed extracts retrieved in this turn: [Mintos Notes creation](https://help.mintos.com/hc/en-us/articles/28939554356625-How-are-Notes-created), [Final terms](https://help.mintos.com/hc/en-us/articles/5624165691921-What-are-Mintos-Notes-base-prospectuses-and-final-terms), [Loan pooling](https://help.mintos.com/hc/en-us/articles/5619797246097-How-are-underlying-loans-pooled-together-in-a-Set-of-Notes); adopt investment-specific terms and disclosures, not pooling, SPV or actual securities. [Mambu loan securities](https://docs.mambu.com/docs/loan-securities-guarantors-and-collateral-assets/), [loan approval](https://docs.mambu.com/docs/approving-a-loan/), [loan versions](https://docs.mambu.com/api/api-v2/loans/get-versions-by-id/) support collateral tracking, submission/approval and version history; CreditForge approves publication eligibility rather than originating credit. [Centrifuge legacy](https://docs.centrifuge.io/getting-started/legacy/) and [Tinlake contracts](https://docs.centrifuge.io/tinlake/contracts/proxyactions/) support tranche exposure; do not import pool NAV, NFT collateral or smart contracts. Exact asset identity, exclusivity, atomic issuance and approval freshness below are local controls from BRD/FD, not externally proven implementation. No full legal document or contract audit.

### 4.2.2 Scope and proposed registration workflow

Single issuer registers one existing funded loan and one collateral asset; borrower may have several distinct loans. Proposed RD-006: Officer selects an existing simulated loan account using stable issuer/source loan reference; retrieve authoritative opening facts from Mock Core Banking, not freehand entry of contradictory balances. Officer supplements collateral evidence, investment disclosure and tranche proposal; simulator need not implement origination or real bank access. Demo fixture provisioning outside the marketplace creates existing loan accounts. Source data absent, stale or inconsistent -> draft/exception, not fabricated issuance. Before issuance, source repayments update the opening basis without distribution to not-yet-issued holders. After issuance, verified eligible receipts enter servicing; at boundary reconcile effective event order and pre-issuance interest; no lost or duplicated receipt.

Proposed RD-007: collateral registry uses canonical simulated asset identity separate from pledge/loan record. Demo asset-type-specific identifier (e.g. simulated land certificate + parcel identifiers, or vehicle chassis/VIN), normalized and matched to a trusted fixture/issuer evidence, with alias/conflict review. No real identifiers or documents required. Same identified asset cannot back two registered loans with outstanding secured obligations; drafts do not establish real pledge, conflicting approvals cannot both issue. Check at registration/submission and final issuance, with concurrency-safe reservation of exclusivity at commitment. Prior verified release required for reuse; zero token rights/writeoff alone is not release. External undisclosed liens cannot be detected; issuer evidence required, known conflict triggers review/restriction under BRD. No legal title, lien registry or automated appraisal claim.

Required private evidence: stable source loan/borrower IDs, simulated disbursement and remaining schedule, principal/rates/dates, pre-issuance principal and interest breakdown, collateral identity/type/description, issuer-reported value and valuation date, secured assignment/exclusivity evidence and references. Officer may add sanitized summary, purpose/industry, risk information and terms. Public information uses simulated borrower alias or approved non-sensitive display name, collateral summary not private identifiers/evidence, outstanding principal and as-of, repayment schedule, maturity, coupon, denomination, tranche sizes, Junior buffer, limited-recourse/illiquidity/default risk, no buyback or guarantee. Valuation is issuer-reported, not guaranteed recovery; LTV may be displayed if value positive with timestamp, no new LTV threshold or risk score imposed. No real CCCD, contracts or title scans necessary; simulated evidence records sufficient for project.

### 4.2.3 Eligibility, financial preview and lifecycle

Before registration/submission/issuance validate existing disbursed current loan, VND, positive eligible outstanding principal, compatible monthly interest + bullet remaining principal schedule, fixed ACT/365 terms and permitted prepayments, one collateral with verified exclusivity, no existing issuance, source opening snapshot consistent. Historical principal repayment permitted. FD-005 full basis: P=verified opening outstanding principal; N=P/F whole units, F=100,000 VND default under FD-010; require exact divisibility, both NS and NJ positive, NS+NJ=N, configured minimum Junior ratio, fixed 0<rS<=rB. 20% minimum is illustrative demo configuration, not calibrated safe level. Do not round principal, automatically change external loan, or silently adjust approved tranche split. Preview computed by authoritative financial rules, not editable balances. Issuance creates both classes; Bank owns all units initially; Junior never offered, Senior offering initial at par. Sale transfers ownership only in 4.3. No new loan disbursement or money movement at issuance.

Proposed RD-008: proposal workflow states Draft -> Submitted -> Issuer Approved -> Marketplace Approved -> Issued; Return/Reject reason retained; returned version editable only as new version, rejected version immutable, resubmission of corrected proposal allowed before issuance, not a second issuance. Material changes to outstanding principal, source terms, collateral evidence/valuation or issuance terms invalidate approvals on previous version. Reviewers return rather than edit. Distinct Officer/Approver and independent Operator bound to exact version. Source freshness verified at issuance; no arbitrary expiry interval finalized. A known source mismatch blocks issuance until reconciled. Separate proposal, credit status, issuance, offering and servicing states. Proposed RD-009: final marketplace approval makes same-version issuance eligible; authorized issuer workflow triggers issuance and publication without another discretionary approval; no human manual mint/supply override. A publication failure after successful issuance retries publication for existing issuance/offering identity, never creates new units; issuance and publication state separately visible. Offering not purchasable until fully published and eligible. Closed offering cannot reopen. Atomic internal posting and concurrency guarantees required; exact transaction/API design Phase 7.

### 4.2.4 Functional requirements

All new RD workflow choices proposed; inherited BD/FD constraints binding. AC are observable specification scenarios, not executed tests.

| ID | Actor / trigger / precondition | Requirement and exception | Source | Acceptance criteria |
|---|---|---|---|---|
| REQ-F-021 | Officer selects existing source loan | Retrieve trusted simulated loan record by stable issuer/source reference; verify source identity, do not originate loan | BD-002-R1/010/017, UC-001, RD-006 | Funded source exists -> draft; missing/unfunded source cannot submit |
| REQ-F-022 | Officer creates/updates registration draft | Capture source references and opening snapshot: original/disbursed, prior principal paid, current principal, dates, schedule, rate, pre-issuance interest, source version/as-of; validate consistency | FR-001–005, BD-017/028 | Original6bn minus prior2bn -> opening4bn; conflicting source balance held |
| REQ-F-023 | Officer associates collateral | Register canonical simulated asset identity and evidence, value/date, description; exactly one per loan | BD-007-R1/016, BR-046/047, RD-007 | Missing/multiple collateral rejected at submission; aliases resolved or held |
| REQ-F-024 | Registration/submission/issuance checks | Reject duplicate source loan or overlapping outstanding secured asset; check verified release before reuse; no external lien guarantee | BR-031/046, RD-007 | Same source loan second issuance denied; same asset on another active secured loan denied; verified release enables eligible reuse |
| REQ-F-025 | Officer validates loan eligibility | Require disbursed current positive VND loan and compatible remaining schedule, rate, prepayment terms and evidence; no external underwriting or contract change | BD-017, FD-001/002/006/018, FR-001/006/007/040 | Overdue at registration, zero principal, incompatible amortization rejected; previously partially repaid eligible loan accepted |
| REQ-F-026 | Officer enters tranche terms | Compute full-basis integer units and exact S/J; verify configured Junior minimum, denomination divisibility and coupon cap | FD-005/008–010, FR-018–021 | 4bn,100k,32k Senior -> 8k Junior; incompatible denomination/coupon rejected |
| REQ-F-027 | Officer previews proposal | Display initial supply, split, remaining principal/unit, fixed coupon, sample projected cashflow and risk warnings; no forecast guarantee; prevent editable derived totals | FD-006/008–011, FR-018–024 | Preview matches canonical 4bn; 80/20 not mandatory; no guaranteed Junior coupon |
| REQ-F-028 | Officer prepares disclosure | Capture required sanitized terms, collateral valuation as-of, limited recourse, risk/illiquidity and no buyback; protect private source evidence | UC-001/005, BR-002/003/021/022, RD-010 | Missing material disclosure blocks submission; public output excludes private asset IDs and evidence |
| REQ-F-029 | Officer submits complete eligible draft | Freeze immutable proposal version and opening snapshot, record submitter/time; prevent incomplete submission | BD-006-R1/028, REQ-F-011, RD-008 | Submitted version immutable; edit creates new version |
| REQ-F-030 | Distinct Approver reviews submitted version | View evidence and preview; approve, return or reject with reason; no self approval or in-place rewrite | BR-048/065, REQ-F-011 | Own submission denied; return reason visible; approval bound to exact version |
| REQ-F-031 | Operator reviews issuer-approved version | Independent eligibility/disclosure check; approve/return/reject; not borrower underwriting; cannot alter terms | UC-003, BD-006-R1/014/028 | Missing issuer approval or version mismatch denied |
| REQ-F-032 | Material source/terms change before issuance | Invalidate affected approvals and require refreshed proposal and both approvals; reconcile authoritative facts | BD-028, BR-065, FR-002 | Principal4bn approved then falls to3bn -> old approval unusable |
| REQ-F-033 | Authorized workflow requests issuance | Revalidate source freshness/current status, eligible outstanding, version, exclusivity, terms and approvals; abort on mismatch | UC-004, BD-007-R1/015/017/028, RD-009 | Source becomes overdue or changed after approval -> no issuance until eligible reapproval |
| REQ-F-034 | Valid final version and revalidation | Create one immutable issuance identity, Senior/Junior classes and initial holdings all Bank; exact supply and opening principal conservation | FD-005/008–010, BR-005/007/010/031/050 | 4bn -> 40k units, Bank32k Senior+8k Junior; no Investor holding or cash transfer |
| REQ-F-035 | Issuance successful | Publish one Senior-only Primary Offering at initial par with approved disclosure; Junior unavailable; failed publication retry same identity | FD-011, BR-011/050, RD-009 | One offering,100k initial price; retry does not mint or duplicate offering |
| REQ-F-036 | Duplicate/concurrent issuance or source repayment at boundary | Idempotent issuance, exclusive loan/asset checks at commitment, reconcile effective source event ordering; no historical receipt replay | FR-001/003/063, BR-025/031/046/050 | Two issuance requests -> one supply; pre-issuance repayment not distributed; ambiguous boundary held |
| REQ-F-037 | Authorized staff inspects status | Show proposal history, approvals, rejection reasons, source freshness, issuance and publication independently; no manual balance edits | UC-021, BR-026/045, RD-008/009 | Publication failed while issued -> visible; retry publication only; history retained |
| REQ-F-038 | Visitor/Investor browses published assets | Show approved sanitized loan terms, current outstanding rights, current risk/status and availability; historical terms distinguish current facts; restrict new orders if ineligible | UC-005, BD-020/027/029, FD-011, RD-010 | Default/maturity not buyable; verified overdue alone remains eligible with warning; private evidence hidden |
| REQ-F-039 | Issued asset terms correction or known collateral conflict | No in-place change to contractual units/coupon/tranche; open authorized exception and apply valid restrictions; preserve history and pending orders | BR-010/045/046/067/070, FD-016/029 | Known overlapping pledge flagged; no silent edit/reissuance; pending paid orders reconciled |
| REQ-F-040 | Verified release of collateral requested | Require authoritative issuer evidence that secured obligations released and distinct review; preserve prior link; allow reuse only after accepted release | BD-007-R1, BR-046, UC-018 | Writeoff/zero token principal alone cannot release; verified release enables subsequent distinct loan |

### 4.2.5 Worked scenario

Source loan originally 6bn, prior principal repayment 2bn, eligible outstanding 4bn. Officer selects existing loan and verifies collateral C-001; proposes 32,000 Senior and 8,000 Junior units at 100k, borrower rate12%, Senior8%, illustrative minimum Junior20%. Issuer Approver and Operator approve same version. If source principal decreases to3bn before issuance, invalidate both approvals and recompute compatible full-basis proposal; do not mint 40k on stale data. If unchanged, issue 40k Bank-owned units once, publish 32k Senior for sale at par. Junior8k stays Bank. No investor money moves until actual purchase. If publication fails, retry existing offering only. Subsequent purchase/repayment handling 4.3/4.5/4.6.

### 4.2.6 Requirements decisions and handoff

| RD | Proposed policy | Basis |
|---|---|---|
| RD-006 | Select authoritative existing loan from Mock Core Banking, supplement evidence/terms; pre-issuance source facts not investor cash | Mambu loan records, BD-017/028 |
| RD-007 | Canonical simulated collateral registry with type-specific identifiers, alias/conflict review and verified release | Mambu securities tracking adapted; BR-046/047 |
| RD-008 | Immutable submitted versions, return/reject history and same-version dual approval; separate lifecycle states | Mambu approval/versioning, BD-006-R1/028 |
| RD-009 | Authorized issuance workflow after final approval, exactly-once issuance and publication retry without re-mint | BD-015, FD-005/010/011; local control |
| RD-010 | Approved sanitized investment disclosure, issuer-reported collateral value/as-of, private evidence restricted; no new scoring threshold | Mintos final terms adapted |

RD-001–010 pending consolidated review. H-08/OI-011 collateral identity and disclosure addressed at draft level; account/roles in 4.1. No architecture, schema, mandatory actual bank link, legal proof, pricing engine or new business/financial policy. H-02 fractional Primary repricing and checkout/availability detailed in 4.3; H-04 late events and corrections in 4.5–4.7; technical locking in Phase 7. Acceptance criteria not executed.

## 4.3–4.10 Planned

 4.3 Primary Market; 4.4 Secondary Market; 4.5 Mock Core Banking Integration; 4.6 Financial Engine & Automatic Payout; 4.7 Credit Risk, Default & Recovery; 4.8 Portfolio, Reporting & Operations; 4.9 Non-functional Requirements; 4.10 Consolidation & Review.

## Revision history

| Version | Change |
|---|---|
| 0.1 | Draft 4.1, reference Mambu/NIST/OWASP/Stripe, REQ-F-001–020 and RD-001–005; BRD/FD unchanged |
| 0.2 | Draft 4.2 using Mintos/Mambu/Centrifuge references; REQ-F-021–040, RD-006–010; previous RD pending; BRD/FD unchanged |
