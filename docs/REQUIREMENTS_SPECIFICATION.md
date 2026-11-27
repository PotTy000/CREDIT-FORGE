# CreditForge — Functional & Non-functional Requirements

**Version:** 0.1 — Phase 4.1 draft
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

## 4.2–4.10 Planned

4.2 Credit Asset Registration & Token Issuance; 4.3 Primary Market; 4.4 Secondary Market; 4.5 Mock Core Banking Integration; 4.6 Financial Engine & Automatic Payout; 4.7 Credit Risk, Default & Recovery; 4.8 Portfolio, Reporting & Operations; 4.9 Non-functional Requirements; 4.10 Consolidation & Review.

## Revision history

| Version | Change |
|---|---|
| 0.1 | Draft 4.1, reference Mambu/NIST/OWASP/Stripe, REQ-F-001–020 and RD-001–005; BRD/FD unchanged |
