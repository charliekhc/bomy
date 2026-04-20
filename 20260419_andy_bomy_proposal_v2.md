# BOMY Consortium Platform — Proposal v2

**Authors:** Andy (platform architecture) with Bob (strategy/engineering)
**Owner:** Charlie
**Document date:** 2026-04-19
**Supersedes:** `20260418_andy_bomy_proposal_v1.md`, `20260418_bob_bomy_proposal_v1.md`

---

## 0. What Changed Since v1

v1 was scoped as a standard multivendor marketplace. During review we learned BOMY is a **Consortium** — a curated brand collective, content media platform, and resource integration hub. v2 re-scopes the foundation to support that model while keeping the technical decisions from v1 that already align.

Key changes in v2:

1. **Business model locked** — 25% regular commission, 10% brand subscription commission, RM59/yr platform membership, three-tier stackable membership
2. **Return & refund policy locked** — 7-day window, 2-3 day review, 5-10 day refund, six reason categories, case-by-case fault
3. **Wallet V1 scope confined** — non-top-up credit only (refunds, referrals, promos); top-up deferred pending e-money licensing review
4. **Studio is external-tools-only** — no in-platform Studio module in V1 or V2
5. **Mascot = egg avatar** — no separate collectibles economy; no trading
6. **Hosting locked** — AWS Singapore (ap-southeast-1) with migration checkpoint to AWS Malaysia (ap-southeast-5) evaluated annually during Stage 10 and executed if trigger criteria met (see Section 20)
7. **Stage 0 extended** — 4–5 weeks with 7 ADRs before Stage 1 starts
8. **Bob's 11-stage spine adopted** with Andy's implementation depth
9. **All platform parameters are admin-configurable** — no hardcoded values

---

## 1. Executive Summary

BOMY is a curated Consortium platform combining:

- **Collective** — vetted brand marketplace (not open enrollment)
- **Media** — brand storytelling, content, and community
- **Resource Hub** — shared fulfilment (Goodie Box), shared vouchers, shared events

Buyers browse and purchase through three stackable membership modes. Sellers (Brands) pay an annual BOMY membership of RM899 and operate through BOMY's payment, fulfilment, and content surfaces. BOMY takes 25% commission on regular orders and 10% on brand subscription revenue.

### Scope Boundary (Hard Callout)

**V1 (Stages 0 through 9, ending at launch):** commerce core, brand membership billing, consumer membership tiers (platform + brand + free), BOMY Wallet credits (no top-up), returns/disputes SLA, referral, egg avatar gamification, SEO on every page, partner network directory, events, feedback community, external-tool Studio workflow (upload only), sitewide admin-configurable parameters.

**Stage 10+ / Future V2:** wallet top-up (pending BNM e-money licensing review under Financial Services Act 2013), advanced loyalty tiers, mobile apps (PWA first), marketing campaign tooling depth, MeiliSearch upgrade, regional expansion to Malaysia AWS region.

**Explicitly out of scope (indefinitely):** mascot trading, collectibles economy, native Content Studio, peer-to-peer wallet transfers, buyer cash withdrawals.

---

## 2. BOMY Consortium Vision & Mission

### Vision
A curated consortium where Malaysian and international brands, media storytelling, and community come together in a single trusted marketplace.

### Mission
- Elevate vetted brands through shared platform infrastructure
- Make buyers feel part of a curated community, not a transactional marketplace
- Operate fulfilment, content, and loyalty as shared resources rather than per-seller fragmentation

### What BOMY Is Not
- Not an open marketplace (no self-signup for sellers)
- Not a wallet provider (no top-up/withdrawal in V1)
- Not a content production studio (sellers use external tools; BOMY hosts the output)

---

## 3. Business Model

### Revenue Streams

| Stream | Rate | Notes |
|--------|------|-------|
| Regular marketplace orders | 25% commission | Base commission on gross order value |
| Brand subscription plan | 10% commission | 90% to brand; separate ledger treatment |
| Platform membership (#1) | 100% to BOMY | RM59/yr default, admin-configurable |
| Brand annual membership | 100% to BOMY | RM899/yr per seller to list on platform |

### Commission Basis Rules (locked)

For a regular-order purchase at retail price `R` with commission `c = 25%`:

- **No voucher/discount:** BOMY takes `R × c`
- **Platform voucher (#1 applied):** BOMY takes `R × c` from full retail. **The voucher is fully BOMY-funded** — BOMY absorbs the entire voucher amount from its own margin. The seller receives the same amount as if no voucher was used (`R × (1 − c)`), so sellers are commercially neutral to platform voucher campaigns
- **Brand subscriber discount (#2 applied):** Let discounted price be `D = R × (1 − d)`. BOMY takes `D × c` (brand absorbs discount cost)
- **No stacking:** platform voucher and brand discount are mutually exclusive at checkout

Subscription-plan purchases (the annual/quarterly subscription fee itself) use the 10% rate, not 25%. The billing engine tags every transaction with a `revenue_source` enum (`regular_order`, `brand_subscription`, `platform_subscription`, `goodie_box_cogs`, `voucher_fund`, `refund`, `referral_grant`) so the split rule picks the right percentage.

### Worked Examples (reference)

**A) Regular order, no discount:** Retail RM100 → BOMY RM25, Brand RM75.
**B) Regular order, #1 voucher RM10:** Buyer pays RM90 → BOMY net RM15 (RM25 commission − RM10 voucher), Brand RM75.
**C) Regular order, #2 discount 10%:** Buyer pays RM90 → BOMY RM22.50, Brand RM67.50.
**D) Brand subscription 12-month plan priced RM300:** BOMY RM30, Brand RM270.

---

## 4. Membership Model

### Tier #1 — BOMY Platform Membership

| Attribute | Value |
|-----------|-------|
| Price (default) | RM59/year |
| Billing cadence | Annual |
| Auto-renew | Yes, with notifications at T-30d, T-14d, T-7d, T-1d |
| Refund on cancellation | None (post-renewal within-term) |
| Revenue split | 100% BOMY |

**Benefits:**
- **Quarterly Goodie Box** — curated sample box shipped 4×/year from BOMY HQ
- **Monthly Voucher** — platform-wide, redeemable at any seller. Amount type: random (BOMY-chosen), percentage, or fixed RM; set per month by admin
- **Priority Event Access** — early/priority seat allocation for BOMY events
- **Early Launch Notifications** — new brand/product drops surfaced ahead of public
- **Feedback Community Access** — gated private forum for #1 members

### Tier #2 — Brand Subscription Plan (stackable)

| Attribute | Value |
|-----------|-------|
| Term options | 3, 6, or 12 months (brand may offer any subset) |
| Price | Brand-set per term, BOMY-approved, locked for term |
| Refund on cancellation | None mid-term |
| Revenue split | 90% Brand / 10% BOMY |
| Fulfilment operator | Brand (seller-operated) |

**Benefits:**
- **Starter Kit** — one-off curated welcome shipment (retail value exceeds subscription fee)
- **Quarterly Curated Delivery** — 3mo = 1 box, 6mo = 2 boxes, 12mo = 4 boxes (Starter Kit excluded from count)
- **Subscriber Discount 5–10%** — on regular (non-subscription) purchases from same brand for the term; brand sets exact percentage within that band
- **Early Access** — new launches from that specific brand
- **Exclusive Events / Digital Experiences** — brand-scoped

Buyer can hold **many #2 subscriptions simultaneously**; each is independent.

### Tier #3 — Free (default)

- Full browsing, story/media access
- Purchase at standard retail
- No recurring billing, no Goodie Box, no vouchers, no discount

### Stackability Matrix

| State | Valid? |
|-------|--------|
| #3 alone | Yes (default) |
| #3 + N×#2 | Yes |
| #1 alone | Yes |
| #1 + N×#2 | Yes |
| #1 + #3 | No (mutually exclusive) |

### Admin-Configurable Membership Parameters

All of the following are editable via admin without code change:

- #1 annual price
- #1 auto-renew notification schedule (days array)
- Monthly voucher amount/type/cap per month
- Brand subscription term options allowed globally
- Default subscriber discount range (floor/ceiling — currently 5–10%)
- Grace period after failed renewal charge
- Goodie Box curation window dates per quarter

---

## 5. Returns & Refund Policy (locked verbatim)

### Eligibility (7-day window from delivery date)

Eligible reasons:
1. Item not as shown
2. Wrong item sent
3. Defective/inadequate item sent
4. Bought wrong item (buyer error)
5. Customer doesn't like item upon receiving
6. Other reason

Ineligible unless defective: perishables, customised/personalised items, digital downloads.

Merchants may extend the window beyond 7 days (shown on store page).

### Flow (case-by-case fault assignment)

1. Buyer initiates via **My Orders → Request Return/Refund**
2. Buyer uploads photos + reason code
3. BOMY/merchant reviews within **2–3 business days**
4. On approval, buyer receives shipping instructions
5. Item inspected on receipt
6. Refund issued within **5–10 business days** to original payment method OR BOMY Wallet (buyer chooses)

Fault (and therefore return-shipping cost) is decided case-by-case by BOMY ops or the seller. Platform arbitration covers reason-6 and contested reason-1/3 cases.

### Buyer Responsibilities

- Secure packaging for return shipment
- Trackable service recommended; retain proof of postage
- BOMY not liable for lost/damaged returns caused by improper packaging

### Membership Refunds

- #1 platform membership: **no mid-term refund**
- #2 brand subscription: **no mid-term refund**
- Starter Kit is separate from the subscription's quarterly deliveries and is not individually refundable

---

## 6. Technical Architecture

### Pattern: Modular Monolith → Microservices

Deploy as one Node.js service with clear module boundaries, communicating via event bus. Split to microservices only when scaling pain is proven (not speculatively).

**Modules (logical boundaries):**
- `identity` (auth, roles, sessions, MFA)
- `catalog` (products, services, categories, media)
- `commerce` (cart, checkout, orders)
- `payments` (PSP integration, splits, idempotency)
- `wallet` (credit ledger, no top-up in V1)
- `subscriptions` (platform #1 + brand #2 engines)
- `membership` (brand RM899/yr billing)
- `fulfilment` (Goodie Box ops, shipping, tracking)
- `returns` (dispute flow, arbitration)
- `referral` (escrow, credits, fraud)
- `gamification` (egg avatar, XP, stages)
- `voucher` (issuance, redemption, expiry)
- `seo` (per-page metadata, structured data)
- `cms` (static pages, community forum)
- `events` (seller events, calendar)
- `admin` (platform config, audit, moderation)
- `notification` (email/SMS/push)
- `analytics` (event collection, reporting)

### Tech Stack (locked)

| Layer | Choice |
|-------|--------|
| Frontend | Next.js 15 + TypeScript + Tailwind + shadcn/ui |
| Backend | Node.js + Fastify + TypeScript |
| ORM | Drizzle ORM |
| DB (primary) | PostgreSQL 16 with Row-Level Security |
| Cache | Redis (ElastiCache) |
| Queue | BullMQ (Redis-backed) |
| Object storage | Cloudflare R2 (products, assets) |
| Video storage | Cloudinary (seller homepage videos) |
| Search (V1) | PostgreSQL FTS |
| Search (V2) | MeiliSearch (migration path) |
| Auth | NextAuth.js v5 |
| PSP | Dual — Curlec/Razorpay Route (MYR) + Stripe Connect (USD). ADR-06 documents the accepted decision and settlement algorithm |
| Email | SendGrid |
| SMS | Twilio |
| Push | Firebase Cloud Messaging |
| Observability | OpenTelemetry → Grafana Cloud + Sentry |
| WAF/CDN/Bot | Cloudflare (WAF, DDoS, Turnstile) |
| Secrets | AWS KMS |
| IaC | Terraform |
| CI/CD | GitHub Actions |

---

## 7. Security Architecture

### Authentication

- **Primary:** OAuth (Google + Meta) via NextAuth.js with PKCE (RFC 9700)
- **Secondary:** Passkey (WebAuthn Level 3) for return visits
- **Tertiary:** Email magic link
- **Recovery:** SMS OTP + email verification flow
- Never single-factor OAuth-only — if an OAuth account is compromised, buyer can recover via passkey or magic link

**MFA required for:** seller admin actions, BOMY admin actions, payout changes, bank account updates, wallet config changes, commission rate changes.

### Authorization

- RBAC with policies: Buyer, Seller Staff, Seller Owner, BOMY Ops, BOMY Admin, BOMY Finance
- PostgreSQL Row-Level Security policies for seller isolation (`current_setting('app.current_seller_id')`)
- API-level + object-level authorization (two checks on every sensitive mutation)

### RLS Implementation Guardrails

Row-Level Security only protects data if tenant context is reliably set and reset around every database access. A misconfigured connection pool or a missed middleware can silently expose cross-tenant data with no runtime error. Guardrails — all mandatory for Stage 2 merge:

1. **Connection acquisition wrapper** — the app never obtains a DB connection directly. All access goes through a helper that (a) opens a transaction, (b) runs `SET LOCAL app.current_user_id`, `SET LOCAL app.current_user_role`, `SET LOCAL app.current_seller_id` (when applicable), (c) executes the query, (d) commits/rolls back, (e) returns connection to pool. `SET LOCAL` scopes values to the transaction — no leakage across pool reuse.
2. **Default-deny RLS policies** — every tenant-scoped table has RLS enabled with a default-deny policy plus explicit allow policies keyed on the `app.current_*` settings. Tables without RLS enabled fail a CI lint check.
3. **Admin escape hatch is explicit** — bypassing RLS requires setting `app.bypass_rls = true` inside a transaction, and only runs on a dedicated DB role (`bomy_admin`) available only to admin services. Every bypass emits an audit-log entry with user, reason, SQL statement.
4. **Middleware enforcement** — every HTTP request hits a middleware that sets tenant context from the authenticated session before any route handler runs. Request paths that legitimately need no tenant context (health checks, public marketing) are allow-listed by exact route match.
5. **Integration tests for RLS bypass attempts** — test suite includes negative tests: seller A's session attempts to read seller B's orders (must return empty), admin without `app.bypass_rls` attempts cross-tenant query (must return empty), unauthenticated request attempts any tenant-scoped query (must fail).
6. **Runtime assertion** — in dev/staging, every query emits a warning if run without `app.current_user_id` set. In production, the same condition emits a structured log line tagged `rls.missing_context` which alerts if > 0 events/hour in normal flow.
7. **Schema migrations require RLS review** — any migration touching a tenant-scoped table must include RLS policy changes in the same PR. Migration CI step verifies RLS enabled on all tenant-scoped tables.
8. **Connection pool configuration** — `pool.max` sized to match expected peak; connections use `DISCARD ALL` on return to pool as defence-in-depth against leaked session state; `statement_timeout` and `idle_in_transaction_session_timeout` set to bound blast radius of any misuse.
9. **Threat model entry (Stage 0):** explicit scenario "compromised seller credential attempts cross-tenant data access" — must be exercised in the Stage 8 pen test.

### Data Protection

- **At rest:** provider-managed storage encryption (AES-256) + application-level AES-256-GCM on sensitive PII fields (phone, IC number, bank account, payout account), keys in AWS KMS
- **In transit:** TLS 1.3 everywhere, HSTS, strict CSP, SRI on static assets
- **Secrets:** AWS Secrets Manager + KMS, rotated quarterly
- **Audit:** immutable append-only log to isolated S3 bucket with Object Lock (evidence retention for compliance & dispute arbitration)

### Abuse & Fraud

- Cloudflare WAF with custom rule sets per surface (auth, checkout, referral, admin)
- Cloudflare Turnstile on signup, login, referral claim, checkout
- Rate limiting per IP + per account + per endpoint family
- Device fingerprinting for referral abuse detection
- Velocity caps (daily referral claims, daily wallet credit grants, daily Goodie Box redemptions)
- Anomaly detection on auth patterns (geo-velocity, device-velocity)

### Compliance Alignment

- **OWASP ASVS v5** verification throughout Stages 2–9
- **OWASP API Security Top 10 (2023)** tests in CI
- **NIST SP 800-63B-4** password/credential rules
- **PCI DSS** — via PSP tokenization; BOMY never stores raw card data
- **PDPA Malaysia (Act 709)** — data classification, retention, DPO role
- **AMLA** — KYC/KYB via PSP for sellers and high-value flows
- **SST** — Sales and Service Tax calculated at checkout per SKU category

### Third-Party Risk Register (tracked in Stage 0)

| Vendor | Surface | Data | Risk Tier | Incident Playbook |
|--------|---------|------|-----------|-------------------|
| Cloudflare | Edge, WAF, DNS | Requests, IPs | Critical | Failover to direct origin, backup DNS |
| AWS Singapore | Compute, DB | All platform data | Critical | Cross-AZ failover, backup region |
| Curlec / Razorpay (MYR) | Payments | Card tokens, bank, FPX | Critical | Stripe handles USD corridor; internal failover to manual PSP if outage |
| Stripe Connect (USD) | Payments | Card tokens | Critical | Curlec handles MYR corridor; manual PSP if outage |
| SendGrid | Email | Addresses | High | SES fallback |
| Twilio | SMS | Phone numbers | High | SES-SNS fallback |
| Cloudinary | Video | Brand media | Medium | R2 fallback |
| Grafana Cloud | Observability | Logs, traces | Medium | Local retention buffer |

---

## 8. Payment Architecture

### PSP Decision (locked — dual PSP)

**Decision: dual PSP.**
- **Curlec / Razorpay Route** — MYR orders, local payment methods (FPX, DuitNow, Malaysian cards), split payouts to seller MYR bank accounts
- **Stripe (Connect)** — USD orders, international cards, cross-border payouts

Rationale: no single PSP today covers both Malaysian local-payment depth (FPX/DuitNow are effectively mandatory for MYR) and the international USD corridor at competitive fees and reliable split-payout semantics. The ops overhead of running two PSPs is an accepted cost.

**Routing rule:** the checkout currency determines the PSP. MYR → Curlec/Razorpay. USD → Stripe. Cart currency is locked at order creation; no mid-order PSP switching.

**ADR-06** documents the accepted decision (status: Accepted at Stage 0 start). ADR-06 still owns the **settlement algorithm** per-case as described in "Settlement Algorithm" below, and the daily reconciliation job design. Those are non-trivial because the two PSPs have different webhook schemas, dispute windows, and payout cadences.

**Cross-PSP operational concerns captured in ADR-06:**
- Unified internal ledger with PSP-agnostic records; PSP is a metadata field
- Dual webhook ingestion with per-PSP signature verification and idempotency
- Per-PSP reconciliation job running against each settlement report
- Dispute/chargeback handling flows routed by PSP of origin
- Refund paths must use the same PSP as the original charge (no cross-PSP refund)
- Reporting unifies revenue across PSPs by currency and `revenue_source`

### Split Payout Model

- BOMY is **not** merchant of record in V1 default. Seller is merchant. BOMY takes commission via split payout at PSP level
- **Exception:** Goodie Box and #1 platform membership — BOMY IS merchant of record (seller is BOMY itself)
- Payout cadence: T+3 business days after order fulfilment confirmation, subject to 7-day return window hold for dispute buffer

### Settlement Algorithm (must be formalised in ADR-06)

Three distinct cash-flow topologies, one per discount case. The chosen PSP must support all three without reconciliation drift.

**Case A — Regular order, no voucher/discount:**
- Buyer pays `R` to PSP
- PSP split: seller receives `R × 0.75`, BOMY receives `R × 0.25`
- Direct PSP split payout works

**Case B — Regular order, #2 brand subscriber discount applied (rate `d`):**
- Buyer pays `D = R × (1 − d)` to PSP
- PSP split: seller receives `D × 0.75`, BOMY receives `D × 0.25`
- Direct PSP split payout works; the brand is bearing the discount cost out of its own share

**Case C — Regular order, #1 platform voucher applied (amount `v`) — FULLY BOMY-FUNDED:**
- Buyer pays `R − v` to PSP (NOT R, because the buyer never hands BOMY the full retail)
- Seller is commercially owed `R × 0.75` (full 75% of retail) — seller is commercially neutral to platform voucher campaigns
- BOMY is commercially owed `R × 0.25 − v` (its normal commission minus the voucher amount it fully funds). If `v > R × 0.25`, BOMY's net on that transaction is negative — this is acceptable and expected for acquisition campaigns; the voucher cap per campaign is admin-configurable to manage exposure
- **Direct PSP split cannot represent this** — seller's payout exceeds the buyer's payment, because the voucher is funded entirely by BOMY
- **Resolution:** for orders carrying a #1 voucher, BOMY funds the full shortfall `v × 0.75` to seller from its own float (the other `v × 0.25` is foregone commission). Two implementation options for ADR-06:
  - **C1 Hold-and-forward:** PSP settles full buyer payment `R − v` to BOMY's operating account. BOMY then pays seller `R × 0.75` from a combination of buyer funds and BOMY margin. BOMY becomes merchant of record for voucher-carrying orders.
  - **C2 Delayed reconciliation:** PSP does split on `R − v` proportionally. BOMY settles the delta (`v × 0.75`) to seller out-of-band daily/weekly. Reconciliation must be automated and audited.
- Accounting entries (double-entry) regardless of implementation:
  - Dr `voucher_fund_expense` `v`; Cr `seller_payable` `R × 0.75`; Dr `commission_revenue` `R × 0.25`; Cr `cash_received_from_buyer` `R − v`
- **Refund of a #1-voucher order:** voucher is not re-issued; buyer receives `R − v` as refund; BOMY and seller unwind their respective revenue entries; BOMY absorbs the `v × 0.25` of commission it would have kept. This must be coded as an explicit refund algorithm, not left to per-case judgement.

**Case D — Brand subscription plan purchase (term fee):**
- Buyer pays `S` (the subscription price) to PSP
- PSP split: brand receives `S × 0.90`, BOMY receives `S × 0.10`
- Direct PSP split payout works. Must use a distinct `revenue_source` tag so reporting separates subscription revenue from regular orders.

**ADR-06 owns the final choice between C1 and C2**, including:
- Which is easier to get right with the chosen PSP
- How it interacts with refunds and chargebacks
- Working capital requirements on BOMY's side (C1 requires more float)
- Treatment of brand's T+3 payout window when BOMY is intermediary

**Reconciliation requirement:** daily reconciliation job compares (a) PSP settlement reports, (b) BOMY ledger entries, (c) per-seller payout records. Any variance > RM1 per transaction or > RM10 daily aggregate triggers ops review within 24h.

### Idempotency

- Every payment write (order, refund, referral grant, wallet credit, subscription charge) carries a client-supplied idempotency key
- Idempotency stored in Redis with 24h TTL + PostgreSQL for longer audit
- Retries safe; duplicate keys return cached response

### Chargebacks & Disputes

- PSP webhook triggers dispute state
- Evidence bundle assembled from order + shipping + return records
- Automatic wallet freeze on seller pending dispute resolution
- Dispute dashboard in admin with SLA tracking

---

## 9. Currency System

### MYR Base, USD International

- **Storage:** all monetary values stored in minor units (sen for MYR, cents for USD) as `int64`
- **Base currency:** MYR — all platform revenue, commission, and accounting ledger in MYR
- **USD:** shown alongside for international buyers; USD order creates snapshot of FX rate at order creation time

### FX Rate Sources & Failure Cascade

Explicit cascade — each tier checked in order; first usable source wins. Every tier emits structured telemetry so failure mode transitions are observable.

| Tier | Source | Freshness Rule | Behaviour |
|------|--------|----------------|-----------|
| 1 | BNM KL USD/MYR reference rate (primary) | Fetched at most 24h ago | Used normally |
| 2 | BNM rate, stale 24–48h | Between 24h and 48h old | Used; checkout UI shows minor note "using most recent official rate"; ops alert fires |
| 3 | Open Exchange Rates API (fallback) | Fetched successfully within last hour | Used, `fx_source` = `oer`; ops alert fires |
| 4 | Last-known-good cached rate (any source) | Within last 7 days | **USD checkout proceeds using the last-known-good rate** with a clear buyer-facing disclaimer at checkout: rate may differ from current market, `fx_source` is tagged as `last_known`, `fx_tier` = 4, and the specific rate + its age are shown. Ops alert fires; admin banner displayed. |
| 5 | No usable rate | Older than 7 days or no cache | USD checkout blocked. MYR checkout continues unaffected. P1 alert fires; ops runbook triggered. |

**Key rules:**
- **Never use a rate older than 7 days for live checkout** (protects against large FX drift under a prolonged outage — this is the hard ceiling behind Tier 4)
- **Never synthesise a rate** from indirect sources (cross-pair calculation, third-party scraping) — only the named official/licensed sources are permitted
- **Tier 4 disclaimer is mandatory and prominent** — buyer must see the rate, its timestamp/age, and acknowledge before confirming the order. The disclaimer text is admin-configurable
- **MYR checkout must always remain available** if USD rate fails entirely (Tier 5) — international buyers can opt into MYR-denominated purchases
- **Existing orders unaffected:** FX degradation impacts only new USD orders being created. Already-placed orders retain their snapshotted rate per the historical immutability rule below.

### FX Snapshot & Immutability

- **Rate snapshot:** each USD order persists `fx_source`, `fx_rate`, `fx_timestamp`, `fx_version`, `rounding_mode`, `fx_tier` (1–4, for audit of degraded-mode orders)
- **Historical immutability:** past transactions NEVER revalued — the snapshot is the source of truth for refunds, reporting, and accounting

### Display Rules

- Buyer currency preference: explicit selector + country default
- Checkout shows both currencies
- Receipts show transacted currency with reference rate noted

---

## 10. BOMY Wallet (V1 non-top-up)

### Scope V1

Wallet is a **credit-only ledger** for:
- Refund credits (buyer choice: refund to original payment OR to wallet)
- Referral rewards (RM5 locked until qualified purchase)
- Promo/voucher credits
- Goodwill gestures from BOMY ops

**Not in V1:** buyer top-up, P2P transfer, withdrawal to bank, wallet-to-wallet transfer between users.

### Ledger Design

- **Double-entry** (every credit has a paired debit elsewhere) — essential for financial correctness
- **Append-only** — never mutate, only add compensating entries
- **Idempotent writes** via idempotency keys on all wallet operations
- **Schema accommodates future top-up** without migration (reserved fields for `source`, `kyc_status`, `compliance_flags`) but top-up code paths are feature-flagged OFF

### Future Top-Up Considerations (Stage 10+ / Future V2)

- Requires BNM e-money licensing review under Financial Services Act 2013
- Likely requires DEAP (Designated Electronic-money Account Provider) or partnership with licensed e-money issuer
- Compliance gate in ADR must be signed off before top-up feature flag flipped

### Expiry Rules

- Refund credits: no expiry
- Referral credits: 180-day expiry from grant
- Promo credits: admin-configured expiry per campaign
- Goodie Box / Voucher funding credits: monthly (expire end of month)

---

## 11. Referral System

### Reward

- **Amount:** RM5 (sen = 500) — admin-configurable
- **USD equivalent:** computed at grant time using BNM snapshot
- **Storage:** immutable record with `reward_source_amount_sen`, `fx_source`, `fx_rate`, `fx_timestamp`, `granted_to_currency`, `granted_amount_minor`

### Escrow & Unlock

1. Referred buyer signs up with referral code
2. Referred buyer makes first qualifying purchase (paid order settled, not merely placed)
3. 7-day escrow window runs (aligns with return window)
4. If no return initiated, referrer's RM5 credit is unlocked and credited to wallet
5. If return initiated and approved, referral is clawed back via compensating wallet entry

### Anti-Abuse

- **Same-device / same-fingerprint** signups blocked from mutual referral
- **Same payment method** across referrer and referred blocked
- **Phone verification gate** — unverified phone cannot unlock referral
- **Monthly cap** per referrer (default 20 referrals/month, admin-configurable)
- **Velocity alerts** on admin dashboard for patterns
- **Manual review queue** for suspicious grants above velocity threshold

---

## 12. Egg Avatar Gamification

### Concept

Every buyer has an egg avatar from signup. The egg hatches as they engage with BOMY, progressing through visual stages. Purely a cosmetic / engagement feature — no economic value, no trading, no tradeability.

### Progression Stages

1. **Stage 0 — Plain egg** (default at signup, buyer picks colour)
2. **Stage 1 — Cracked egg** (first qualifying purchase)
3. **Stage 2 — Partially hatched** (N qualifying purchases)
4. **Stage 3 — Hatched creature** (M qualifying purchases, initial customisation unlocked)
5. **Stage 4 — Customised** (profile-page customisation: colours, patterns, accessories)

Thresholds admin-configurable.

### Qualifying Events

- Only **paid + settled + past-return-window** orders count
- Refunded orders REDUCE progression (via compensating XP entry)
- Review submission optional XP boost
- Referral unlocks optional XP boost

### Integrity

- Server-side progression authority — client never self-reports
- Progression re-computable from event log (event sourcing pattern) for audit / rollback safety
- Admin tool to manually adjust XP with reason code

### UI Surfaces

- Profile page (primary)
- Small avatar badge on comments/reviews (social surface)
- Popup on stage advancement (celebration moment)

---

## 13. SEO Architecture

### Per-Page Controls (every page)

- `meta_title` (manual override + auto-default)
- `meta_description`
- `canonical_url`
- `slug` (uniqueness enforced, redirect on slug change)
- `open_graph_title`, `og_description`, `og_image`
- `twitter_card` fields
- `robots` (index/follow flags per page)
- `hreflang` entries (future multi-lingual)

### Auto-Defaults

- Product pages: derived from product name + brand + category
- Seller storefront: derived from brand name + tagline
- Static pages: admin-authored via CMS

### Technical SEO

- `sitemap.xml` auto-generated, submitted to Google Search Console
- `robots.txt` admin-editable
- Structured data (schema.org) for: Product, Organization, BreadcrumbList, Event, Article, FAQPage
- Server-side rendering (Next.js SSR/ISR) for all public pages
- Core Web Vitals budgets as CI gates (LCP < 2.5s, INP < 200ms, CLS < 0.1)

### URL Governance

- Slug uniqueness across seller scope + global scope
- Slug change creates 301 redirect automatically
- Redirect chain depth capped at 3; warnings beyond

---

## 14. Sitemap v1.2

### Public

- `/` (Home)
- `/brands` (Brand directory / collective)
- `/brands/[slug]` (Brand storefront)
- `/products` (Browse)
- `/products/[slug]` (Product detail)
- `/services` (Service directory)
- `/events` (BOMY + seller events calendar)
- `/stories` (Brand storytelling / media hub)
- `/membership` (Membership explainer + signup)
- `/goodie-box` (Goodie Box marketing page)
- `/partner-network` (Partner directory)
- `/search`
- `/about`, `/help`, `/terms`, `/privacy`, `/refund-policy`

### Buyer Account

- `/account` (overview)
- `/account/orders`
- `/account/subscriptions` (platform + brand subs unified view)
- `/account/wallet`
- `/account/addresses`
- `/account/payment-methods`
- `/account/referrals`
- `/account/vouchers`
- `/account/avatar` (egg customisation)
- `/account/community` (feedback forum, #1-gated)
- `/account/notifications`

### Seller/Brand

- `/seller` (dashboard home)
- `/seller/profile` (brand page editor)
- `/seller/listings` (product CRUD)
- `/seller/inventory`
- `/seller/orders`
- `/seller/subscriptions` (buyers on their brand plan)
- `/seller/promotions`
- `/seller/events`
- `/seller/media` (R2 + Cloudinary uploads)
- `/seller/payouts`
- `/seller/analytics`
- `/seller/settings`
- `/seller/membership` (their BOMY RM899/yr status)

### Admin

- `/admin/users` (buyers + sellers)
- `/admin/brands` (approval, moderation)
- `/admin/listings` (moderation)
- `/admin/cms` (pages, SEO manager)
- `/admin/referrals` (rules, fraud console)
- `/admin/gamification` (rules, XP adjustments)
- `/admin/membership` (#1 config, pricing, renewal notifications)
- `/admin/subscriptions` (brand #2 approvals, term rules)
- `/admin/vouchers` (monthly issuance config, templates)
- `/admin/goodie-box` (quarterly curation, shipping tracker entry)
- `/admin/finance` (reconciliation, payouts, commission reports)
- `/admin/returns` (dispute arbitration queue)
- `/admin/events` (calendar moderation)
- `/admin/partner-network` (directory curation)
- `/admin/audit-logs`
- `/admin/feature-flags`
- `/admin/config` (platform-wide configurable parameters)

---

## 15. Partner Network Directory

A curated directory of service providers (photographers, logistics partners, packaging suppliers, content creators) that BOMY sellers can engage.

- Admin-curated listings (not self-signup)
- Public-facing browse + filter
- Sellers can submit requests / contact forms
- No transactional layer in V1 (intros only)

---

## 16. Events & Community

### Events
- Seller-scoped events (promotions, launches)
- BOMY-scoped events (platform-wide campaigns, Goodie Box reveals)
- Calendar UI with RSVP
- Priority RSVP gate for #1 members

### Community Forum
- Access gated by #1 membership
- Thread-based feedback & discussion
- Moderation queue
- Integrations: email digest, weekly summaries
- Built on lightweight forum primitives (not a full Discourse port in V1)

---

## 17. External Studio Workflow

BOMY does not build content production tools. Sellers use external tools (Canva, CapCut, Adobe Express, etc.) and upload finished assets.

### Supported Asset Types

- Product images → Cloudflare R2 (zero egress, cheap)
- Seller homepage video → Cloudinary (transcoding, adaptive streaming)
- Brand logos → R2
- Marketing banners → R2

### Upload Constraints (admin-configurable)

- Max image size (default 10MB)
- Max video size/length (default 500MB / 2 minutes)
- Allowed formats (JPG, PNG, WebP, MP4, MOV)
- Automatic format conversion (Cloudinary)
- Malware scanning on upload via ClamAV integration

---

## 18. Admin Configuration Panel

All platform parameters editable by admin without code deploy. Stored in `platform_config` table with version history + audit log.

### Configurable Parameters (sample, not exhaustive)

| Parameter | Default | Notes |
|-----------|---------|-------|
| `platform_membership_price_myr_sen` | 5900 | #1 price |
| `platform_membership_term_days` | 365 | Annual |
| `platform_membership_renewal_notifications_days` | [30,14,7,1] | Days before renewal |
| `brand_subscription_commission_pct` | 10 | BOMY's cut |
| `regular_order_commission_pct` | 25 | BOMY's cut |
| `brand_subscriber_discount_floor_pct` | 5 | Min brand discount |
| `brand_subscriber_discount_ceiling_pct` | 10 | Max brand discount |
| `referral_reward_myr_sen` | 500 | RM5 default |
| `referral_escrow_days` | 7 | Aligns with return window |
| `referral_monthly_cap_per_user` | 20 | Anti-abuse |
| `return_window_days_default` | 7 | Sellers may extend |
| `refund_processing_sla_days_min` | 5 | Lower bound of 5–10 |
| `refund_processing_sla_days_max` | 10 | Upper bound |
| `voucher_monthly_amount_type` | `percent` | enum: random/percent/fixed |
| `voucher_monthly_default_value` | 10 | interpreted per type |
| `fx_source_primary` | `bnm` | BNM daily feed |
| `fx_source_fallback` | `openexchangerates` | |
| `fx_stale_threshold_hours` | 48 | Trigger fallback |
| `gamification_stage_thresholds` | [1,5,15,30] | Orders needed per stage |
| `goodie_box_curation_window_days_before_quarter` | 30 | Admin prep time |
| `search_engine` | `postgres_fts` | `meilisearch` in V2 |

### Audit & Safety

- Every config change writes to immutable audit log (who, what, when, old→new)
- Changes to commission rates or pricing require MFA + two-admin approval
- Automatic rollback window (admin can revert within 24h without data loss)
- Staging env supports config preview before production push

---

## 19. Development Stages (Bob's 11-stage spine, extended)

### Stage 0 — Discovery, Compliance, ADR Backlog (4–5 weeks, extended per Option B)

- Consortium PRD refresh with all business model details locked
- Threat modeling (auth, checkout, wallet, referral, returns, subscription)
- Compliance gap list (PDPA, AMLA, SST, FSA-2013 for future wallet top-up)
- **ADR Backlog (7 items):**
  - ADR-01: Shipping model (platform-managed / seller-managed / hybrid)
  - ADR-02: Support tooling (Intercom / Zendesk / custom)
  - ADR-03: Commission implementation (flat / tiered / category-based)
  - ADR-04: Return shipping default (party-at-fault matrix details)
  - ADR-05: Hosting/data residency final rule (AWS SG now, MY trigger criteria)
  - ADR-06: PSP choice (locked — dual: Curlec/Razorpay MYR + Stripe USD) + settlement algorithm
  - ADR-07: Wallet architecture (ledger schema, idempotency boundaries, top-up gate)
- Third-party risk register filled
- Evidence retention policy + S3 Object Lock configured
- Change freeze policy documented

**Deliverables:** PRD v2, risk register, ADR docs, data classification, compliance gap list, threat model.

### Stage 1 — Platform Foundation (2–3 weeks)

- Monorepo setup, CI/CD, Terraform, AWS Singapore infra
- Environment separation (dev/stage/prod)
- Secret management (AWS Secrets Manager + KMS rotation)
- Baseline security headers + Cloudflare WAF rules
- NextAuth.js + OAuth + passkey + magic link foundation
- Observability pipeline (OTEL → Grafana Cloud + Sentry)

**Deliverables:** Platform skeleton, staging env, auth baseline, CI checks.

### Stage 2 — Core Domain & Data Model (3–4 weeks)

- Entities: users, roles, brands, listings, orders, payments, wallet ledger, subscriptions (both kinds), memberships, referrals, avatars, vouchers, configs
- Immutable double-entry ledger
- Audit framework + soft-delete/versioning
- Event schema registry (Zod, versioned — `order.created.v1`)
- Row-Level Security policies for brand isolation
- `platform_config` table with admin UI skeleton

**Deliverables:** DB schema v1, migration pipeline, event contracts, audit framework, config panel MVP.

### Stage 3 — Seller/Brand Experience MVP (4–5 weeks)

- Brand onboarding (admin-curated), KYB flow via PSP
- Brand page editor (logo, cover, video, story, promo blocks)
- Listing CRUD with SEO fields
- Media upload pipeline (R2 + Cloudinary + ClamAV)
- Seller dashboard (orders, sales, buyer history — permission-filtered)
- Seller events creation
- RM899/yr BOMY membership billing

**Deliverables:** Seller portal v1, brand storefront rendering, moderation flags.

### Stage 4 — Buyer Experience MVP (4–5 weeks)

- Registration/login (OAuth + passkey + magic link + SMS recovery)
- Egg avatar: initial colour choice, profile display
- Account tabs: addresses, payment methods, orders, wallet, referrals
- Cart + checkout UX with dual-currency
- Order tracking, basic notifications

**Deliverables:** Buyer portal v1, checkout pipeline, auth hardening.

### Stage 5 — Payments, Multi-Currency, Referral (4–6 weeks)

- PSP integration per ADR-06
- Split payout rules (25% regular, 10% subscription)
- Refunds + chargebacks flow
- BNM FX fetch + fallback + snapshot persistence
- Referral rules engine with fraud guardrails
- Wallet ledger live (credit-only)
- Idempotency on all payment writes

**Deliverables:** Stable payments, wallet live, referral live, fraud v1.

### Stage 6 — Memberships & Subscriptions (4–5 weeks)

- #1 Platform membership billing, renewal scheduler, notification jobs (T-30/T-14/T-7/T-1)
- #2 Brand subscription billing per term (3/6/12 months), starter kit shipment trigger, quarterly delivery scheduler
- Voucher issuance engine (monthly, three amount types)
- Brand subscriber discount engine at checkout
- No-stacking enforcement
- Goodie Box admin module (curation, shipment list, tracking entry)

**Deliverables:** Three-tier membership live, Goodie Box ops, voucher system.

### Stage 7 — Gamification, SEO, Returns (3–4 weeks)

- Egg avatar progression engine (event-driven, server-authoritative)
- Profile customisation (colour, patterns, accessories)
- Per-page SEO panel in admin + seller contexts
- Sitemap + robots + structured data
- Returns workflow with 6 reason codes, 2–3 day review SLA, arbitration queue
- Wallet refund pathway

**Deliverables:** Avatar live, SEO system live, returns system live.

### Stage 8 — Security Hardening (3–4 weeks)

- OWASP ASVS v5 verification
- OWASP API Top 10 tests in CI
- Pen-test readiness + external pen test
- SBOM generation + Sigstore signing
- Session hardening, MFA enforcement for seller/admin
- Anomaly detection live

**Deliverables:** Security evidence pack, remediations closed, go-live signoff.

### Stage 9 — Performance, Reliability, UAT (3–4 weeks)

- Load tests (checkout, search, subscription renewal spike)
- Idempotency / retry / failover drills
- SLO targets + alerting (LCP < 2.5s, API p95 < 300ms, error rate < 0.5%)
- **DR drills per Section 20:** Tier 1 monthly restore test (financial), quarterly Tier 2 full-region failover drill
- **Canary + auto-rollback pipeline** wired (per Section 20 Release Safety Gates)
- **Change-freeze policy** activated and enforced by CI/CD (per Section 20)
- **Annual Data Protection Review** calendar entry set (per Section 20 PDPA controls)
- Runbooks + on-call rotation
- Pilot with selected brands, staged rollout

**Deliverables:** Launch checklist, dashboards, on-call handbook, first-pass DR drill report, canary pipeline in production.

### Stage 10 — Post-Launch Optimization (ongoing, 4-week cycles)

- Conversion funnel tuning
- Search relevance (preparing MeiliSearch migration)
- Fraud model iteration
- Hot-module extraction to microservices as justified by scaling pain
- AWS Malaysia migration checkpoint (criteria: data residency requirement formalised, or Malaysia-specific latency SLO)

**Deliverables:** Monthly KPI + risk reports, roadmap increments.

---

## 20. Infrastructure & Hosting

### V1 — AWS Singapore (ap-southeast-1)

Rationale:
- Mature region, best multi-AZ availability
- Lower latency to international buyers (USD segment)
- Full service parity (RDS, ElastiCache, S3, KMS, Secrets Manager, CloudWatch)
- Cloudflare edge handles MY last-mile latency

### Migration Checkpoint — AWS Malaysia (ap-southeast-5)

Trigger criteria (any):
- Data residency requirement formalised (regulator ruling, partnership clause)
- Malaysia-specific latency SLO pressure from analytics
- Cost advantage above threshold

Trigger criteria are evaluated annually during Stage 10 (and ad-hoc if a regulator ruling forces the question mid-cycle); migration executes in Stage 10 if any trigger is met.

### Baseline Infra

- VPC with private subnets per AZ
- RDS PostgreSQL 16 Multi-AZ
- ElastiCache Redis (cluster mode enabled)
- Storage (see **Storage Source-of-Truth Matrix** below — not all buckets are interchangeable)
- ECS Fargate for service workloads
- ALB + CloudFront + Cloudflare (layered CDN/WAF)
- Terraform for all IaC
- CI/CD via GitHub Actions with OIDC to AWS (no long-lived keys)

### Storage Source-of-Truth Matrix

To prevent operational ambiguity, each asset class has exactly one authoritative store:

| Asset Class | Source of Truth | Backup / Replica | Notes |
|-------------|-----------------|------------------|-------|
| Product images, brand logos, banners, review photos | **Cloudflare R2** | Lifecycle copy to AWS S3 (Glacier) nightly | Zero egress cost; CDN fronted by Cloudflare |
| Seller homepage video | **Cloudinary** | R2 nightly archive | Transcoding + adaptive streaming handled by Cloudinary |
| Audit logs, financial evidence, dispute records | **AWS S3 (Object Lock, isolated account)** | Versioned; replicated cross-region | Write-once, compliance-grade retention |
| Database snapshots (RDS PITR + daily) | **AWS S3 (same account as RDS)** | Cross-region replica | PITR 35 days, daily snapshot 90 days |
| Terraform state | **AWS S3 (infra account)** | Versioned | State locking via DynamoDB |
| Static marketing assets (SSR/ISR output) | **Next.js / CloudFront** | Rebuildable from source | No authoritative storage needed |
| PII at rest (phone, IC, bank) | **RDS encrypted columns** | Backup inherits encryption | AES-256-GCM app-layer + RDS TDE |
| Uploaded documents (KYC, KYB) | **AWS S3 (isolated, encrypted, access-logged)** | Cross-region replica | Legal retention per AMLA / PDPA |

**Rule:** no asset class has two source-of-truth stores. If an asset exists in multiple places, exactly one is canonical and the rest are derived copies (backup, cache, or CDN).

### Disaster Recovery Targets (RPO / RTO)

| Tier | Systems | RPO | RTO | Restore Drill |
|------|---------|-----|-----|---------------|
| **Tier 1 — Critical financial** | RDS ledger, payments, subscriptions, wallet | **5 minutes** | **1 hour** | Monthly restore test on copy |
| **Tier 2 — Core platform** | Auth, catalog, orders, CMS | **15 minutes** | **4 hours** | Quarterly full-region failover drill |
| **Tier 3 — Media & ancillary** | R2 objects, Cloudinary, search index | **24 hours** | **8 hours** | Bi-annual drill |
| **Tier 4 — Observability & analytics** | Grafana logs/metrics, analytics warehouse | **24 hours** | **24 hours** | Annual drill |

**Backup mechanisms:**
- PostgreSQL: continuous WAL archiving (PITR up to 35 days) + daily full snapshots (retained 90 days) + monthly snapshots (retained 1 year)
- R2/S3: object versioning + lifecycle policies
- Cross-region replication for Tier 1 data to ap-southeast-2 (Sydney) as DR target
- Secrets / KMS key material: multi-region keys

**Drill policy:**
- Monthly: Tier 1 restore to a sandbox DB, verify latest backup integrity, reconcile with ledger
- Quarterly: full Tier 2 failover drill (blue-green region, documented runbook, on-call participation)
- Bi-annual: regional outage simulation with comms/runbook exercise
- All drill outcomes filed to audit log; failures generate follow-up incidents

### Release Safety Gates (Canary + Auto-Rollback)

All production deploys pass through a canary stage. No direct-to-100% deploys except emergency security hotfixes approved by two admins.

- **Canary pattern:** 5% traffic for 15 min → 25% for 30 min → 100%
- **Auto-rollback triggers (any one fires rollback):**
  - Error rate rises > 1.0% over baseline (or > 0.5% for 5 min)
  - Checkout failure rate > 0.5% for 3 min
  - Payment webhook processing lag > 60s for 5 min
  - API p95 latency > 1000ms for 5 min
  - Any alarm on Tier 1 systems (payments, wallet, ledger)
- **Feature flags** (LaunchDarkly or OpenFeature) for graduated rollout of individual features within a deploy
- **Blue-green** for infra-level changes (DB major version upgrade, network topology, ECS task definition breaking changes)
- **Rollback record:** every auto-rollback generates a post-mortem within 48 hours

### Change-Freeze Policy

To prevent regressions during high-traffic events and financial cycles, we apply scheduled freeze windows where only hotfix deploys are permitted.

**Automatic freeze windows:**
- 48 hours before, during, and 24 hours after any scheduled BOMY marketing campaign
- 48 hours before, during, and 24 hours after Goodie Box shipment cycle kick-off each quarter
- Monthly on the 25th–1st (to protect end-of-month financial close and subscription billing runs)
- Public holidays in Malaysia (observed days only)
- 7 days before and 3 days after major platform releases (defined as a deploy containing schema migrations or PSP integration changes)

**During freeze:**
- Only hotfixes addressing Tier 1 incidents, security vulnerabilities, or active data-integrity issues are permitted
- Any hotfix deploy requires two-admin sign-off and an attached incident ticket
- All deploys during freeze generate an automatic exception log entry
- Config changes in admin panel (which are data, not code) remain permitted, except commission/pricing changes which inherit the freeze

**Manual overrides:** only by Charlie or a designated deputy, recorded in audit log with justification.

### PDPA Cross-Border Transfer Controls (AWS Singapore hosting)

Since V1 hosts Malaysian buyer/seller PII in Singapore, PDPA Act 709 Section 129 (cross-border data transfer) rules apply. We mitigate via:

- **Standard Contractual Clauses** with AWS (existing AWS DPA covers this)
- **Data subject consent** at account creation (clear consent for cross-border processing in privacy policy)
- **Data minimisation:** PII encrypted at column level with KMS; access logs retained
- **Annual Data Protection Review** (new recurring task, owned by DPO role):
  - Verify AWS DPA + cross-border controls still valid
  - Verify retention policies align with PDPA schedule
  - Verify third-party risk register still accurate (Section 7)
  - Verify no new data categories added without privacy assessment
  - Report to Charlie + filed in audit bucket
- **Data residency trigger review** (annual): if Malaysian regulator issues residency requirement, ADR-05 migration criteria triggers and we move to ap-southeast-5

**Incident disclosure (provisional, pending legal validation):** Current working target is notification to the Personal Data Protection Commissioner within 72 hours of discovery, aligned with PDPA 2024 amendment guidance. The exact SLA, trigger thresholds (what qualifies as a "breach" under the amended Act), notification channel, and notifier role **must be validated by external legal counsel before the go-live runbook is finalized in Stage 9.** The 72-hour figure in this proposal should be treated as the working assumption for planning, not as fixed policy.

---

## 21. Stage 0 ADR Backlog (detailed)

### What is an ADR?

**ADR = Architecture Decision Record.** A short, dated document that captures one important technical or product decision at the moment it is made. Each ADR follows the Michael Nygard format:

- **Context** — the problem, the constraints, the forces at play
- **Decision** — what we chose
- **Status** — proposed / accepted / superseded / deprecated
- **Consequences** — the trade-offs, what this locks us into, what remains open

### Why we use ADRs

Months or years from now, someone — a new engineer, an auditor, a regulator, or one of us — will ask *"why did we pick Stripe over Curlec?"* or *"why is brand subscription commission 10% instead of 25%?"* Without an ADR, the reasoning lives only in Slack threads, meeting notes, or memory, and it gets lost. With an ADR, the reasoning is version-controlled next to the code and survives team turnover.

This matters especially for BOMY because several decisions (PSP choice, commission model, shipping model, wallet architecture) carry compliance, tax, and financial implications that future engineers, auditors, or regulators may need to review.

### How ADRs work in practice

- Each ADR is a markdown file in `/docs/adrs/NNNN-short-title.md`
- One owner per ADR, team review, marked **Accepted** once signed off
- ADRs are never edited after acceptance — if the decision changes later, write a new ADR that **supersedes** the previous one
- Reference: Michael Nygard's original template (2011), https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions

### The 7 ADRs for BOMY Stage 0

Each ADR below follows the Nygard format: Context, Decision, Status, Consequences.

1. **ADR-01 Shipping model** — platform-managed (BOMY integrates with 1–2 couriers, sellers ship through us) vs seller-managed (sellers pick couriers, BOMY shows tracking) vs hybrid (BOMY recommended, seller override). Owner: Bob. Due: Stage 0 W1.

2. **ADR-02 Support tooling** — Intercom (best UX, costly) vs Zendesk (mature, mid-cost) vs Freshdesk (cheap, adequate). Owner: Charlie. Due: Stage 0 W2.

3. **ADR-03 Commission implementation** — flat 25% all categories vs tiered by category (e.g., food 18%, fashion 28%) vs seller-negotiated. Owner: Andy. Due: Stage 0 W2.

4. **ADR-04 Return shipping default** — party-at-fault matrix per reason code; who pays return courier when fault ambiguous. Owner: Bob. Due: Stage 0 W3.

5. **ADR-05 Hosting/data residency** — AWS Singapore final confirmation + Malaysia trigger criteria. Owner: Andy. Due: Stage 0 W3.

6. **ADR-06 PSP choice & settlement algorithm** — **decision locked: dual PSP** (Curlec/Razorpay Route for MYR, Stripe Connect for USD). ADR-06 documents the accepted decision with status: Accepted, and formalises the settlement algorithm for the four cash-flow cases in Section 8 (Case A normal split, Case B brand-discount split, Case C #1-voucher with hold-and-forward or delayed reconciliation choice, Case D subscription-plan split) plus the daily reconciliation job design and cross-PSP operational concerns. Owner: Andy + Bob. Due: Stage 0 W4.

7. **ADR-07 Wallet architecture** — ledger schema, idempotency boundary, top-up gate, KYC field reservation. Owner: Andy. Due: Stage 0 W4.

---

## 22. Open Questions for Charlie

1. ADR-01 input: do you have a preferred courier / logistics partner for Goodie Box and seller fulfilment?
2. ADR-02 input: existing support stack preference?
3. ADR-03 input: is 25% uniform across categories, or should some (e.g., services, high-margin digital) differ?
4. Brand vetting workflow — who curates which brands get invited? Is there a scoring rubric?
5. Goodie Box cost model — is RM59/yr × member count expected to exceed Goodie Box COGS + shipping, or is the box a loss-leader for #1 acquisition?
6. Consumer tier naming — is "Platform Membership" the marketing name, or something like "BOMY Circle" / "BOMY Collective"?
7. Feedback community moderation — in-house BOMY ops, or community-volunteer moderators?
8. Events capacity — who hosts BOMY-scoped events (physical location, webinar platform)?
9. Brand RM899/yr — when does a new brand pay? On approval, on first listing, on first sale?
10. Do we want Bahasa Melayu as a V1 language, or English-only at launch?

---

## 23. Sources

- OWASP Top 10 (2021): https://owasp.org/Top10/2021/
- OWASP ASVS v5: https://owasp.org/www-project-application-security-verification-standard/
- OWASP API Security Top 10 (2023): https://owasp.org/API-Security/editions/2023/en/0x11-t10/
- NIST CSF 2.0: https://www.nist.gov/publications/nist-cybersecurity-framework-csf-20
- NIST SP 800-63B-4: https://pages.nist.gov/800-63-4/sp800-63b.html
- OAuth 2.0 Security BCP (RFC 9700): https://www.ietf.org/rfc/rfc9700.html
- OpenID Connect Core: https://openid.net/specs/openid-connect-core-1_0-35.html
- W3C WebAuthn Level 3: https://www.w3.org/TR/webauthn-3/
- PCI DSS document library: https://www.pcisecuritystandards.org/document_library?class=pcidss&doc=pci_dss
- Malaysia PDPA Act 709: https://www.pdp.gov.my/ppdpv1/en/akta/pdp-act-2010-en/
- Financial Services Act 2013 (Malaysia): https://www.bnm.gov.my/documents/20124/761682/fsa2013_act_en.pdf
- BNM KL USD/MYR reference rate: https://financialmarkets.bnm.gov.my/data-download-kl-usd-myr-reference-rate
- Google Search Essentials: https://developers.google.com/search/docs/essentials
- Google sitemaps: https://developers.google.com/search/docs/advanced/sitemaps/overview
- Google structured data: https://developers.google.com/search/docs/guides/intro-structured-data
- Stripe Connect marketplace docs: https://docs.stripe.com/connect/marketplace
- Stripe idempotency: https://docs.stripe.com/api/idempotent_requests
- NextAuth.js v5: https://authjs.dev/
- Drizzle ORM: https://orm.drizzle.team/
- Cloudflare R2: https://developers.cloudflare.com/r2/
- MeiliSearch: https://www.meilisearch.com/docs

---

## Appendix A — Revision History

- **v1 (2026-04-18)** — Initial Andy proposal, standard multivendor assumptions
- **v1 Bob (2026-04-18)** — Parallel Bob proposal, 11-stage spine, added ledger/WAF/ASVS
- **v2 (2026-04-19)** — Consortium re-scope, locked business model, 3-tier membership, return policy verbatim, admin-config-first, AWS Singapore hosting, 7 ADRs, incorporates 4 rounds of Bob feedback
- **v2 patch (2026-04-19, post-Bob-review)** — Fixed scope boundary wording (V1 = Stages 0–9); added DR targets with 4-tier RPO/RTO matrix; added storage source-of-truth matrix to remove R2/S3 ambiguity; added change-freeze policy with automatic windows; added canary + auto-rollback release gates; added PDPA cross-border transfer controls and annual Data Protection Review
- **v2 patch 2 (2026-04-19, post-Bob-review-2)** — Resolved Malaysia migration timeline inconsistency; checkpoint is now consistently described as a Stage 10 annual evaluation (criteria review + execution both in Stage 10). Also removed a separate ADR-05 implication that Stage 3 touches the migration decision
- **v2 patch 3 (2026-04-20, post-Bob-go-signoff)** — Four conditional-for-Stage-1 items closed: (1) PDPA 72h SLA softened to "provisional, pending counsel validation" with validation gated before Stage 9 runbook finalization; (2) added explicit Settlement Algorithm in Section 8 covering four cash-flow cases (regular, #2 discount, #1 voucher with C1/C2 implementation alternatives, subscription plan) plus daily reconciliation job with RM1/RM10 variance thresholds; ADR-06 scope expanded to cover settlement algorithm choice; (3) expanded FX failure cascade to 5 tiers with explicit degraded-mode behaviour, 7-day hard limit on stale rates, MYR-always-available guarantee; (4) added RLS Implementation Guardrails subsection with 9 mandatory controls including connection-acquisition wrapper, default-deny policies, admin-bypass auditing, runtime assertions, pen-test scenario
- **v2 patch 4 (2026-04-20, Charlie alignment corrections)** — Three corrections per Charlie's guidance that were not yet reflected: (1) **PSP is locked as dual PSP** (Curlec/Razorpay for MYR + Stripe Connect for USD) — no longer presented as candidates; ADR-06 now documents the accepted decision at Stage 0 start; (2) **FX Tier 4 behaviour changed** from "USD checkout suspended" to "USD checkout proceeds using last-known-good rate with mandatory buyer-facing disclaimer showing rate + age" (7-day hard ceiling retained; Tier 5 is the real block); (3) **#1 platform vouchers are explicitly marked as fully BOMY-funded** — seller is commercially neutral; BOMY absorbs the full voucher amount from its own margin and can run acquisition campaigns even at net-negative on individual transactions within admin-configurable caps

## Appendix B — Glossary

- **Consortium** — BOMY's curated collective of brands + media + services
- **Collective** — the vetted brand directory aspect
- **Goodie Box** — quarterly curated sample box for #1 members
- **Starter Kit** — one-off welcome shipment for new #2 subscribers
- **Egg Avatar** — buyer's cosmetic gamification companion (no trading, no economic value)
- **BOMY Wallet** — internal credit ledger (no top-up in V1)
- **Brand Member** — seller paying RM899/yr platform membership
- **Platform Member** — buyer paying RM59/yr #1 membership
- **Brand Subscriber** — buyer holding an active #2 subscription with a specific brand
