# MiFID Focus — Personnel & Setup Wishlist

**Purpose:** Define the operating setup that lets DLT put 100% of its energy on the **MiFID box** — leverage products (perps, options, turbos, mini-futures), structured yield + downside protection (covered calls, zero-interest credit, principal-protected notes, BRCs/Sprinters), and Delta-1 security tokens (tokenised equities, ETFs, commodities) — across all underlyings.

**Premise:** We stop running things that don't differentiate us. Everything in the **MiCAR box** (spot trading, spot settlement, spot post-trade) is commodity infrastructure that Uphold already operates at scale. We offload it. What we keep and double down on is the part that is genuinely our moat: the **risk engine + post-trade/reporting layer** for derivatives and structured products.

**Scope & coverage:** The MiFID engine serves **both B2B clients and retail**, with the **product launchpad targeting EU + Global** reach (MiCAR/MiFID-native in the EU, extensible to other jurisdictions). Retail is an explicit distribution channel, not just an institutional API play.

---

## Part A — Offload spot trading & settlement to Uphold

### The principle
For B2B clients, DLT keeps the **client-facing contract** (our brand, our endpoints, our SLAs) but stops being the **principal and operator** of spot execution, settlement, and the associated risk. Our endpoints stay the integration surface clients code against; behind them, spot order flow, custody, settlement, and counterparty/credit risk are routed straight into Uphold's systems.

In short: **we keep the API, Uphold keeps the balance sheet and the back office** for spot.

### What "point our endpoints directly to Uphold" means in practice

1. **Endpoint pass-through, not re-implementation.**
   Today our spot endpoints (place order, get fills, balances, settlement status) hit our own matching/settlement stack. We refactor them into a **thin routing/adapter layer**: same request/response schema for the client, but the call is translated and forwarded to the equivalent Uphold endpoint. Clients see no change to their integration. We delete the spot engine underneath over time.

2. **Settlement moves onto Uphold's rails.**
   Spot trades settle in Uphold's ledger and custody. DLT no longer holds spot inventory, no longer fronts settlement, no longer carries the spot float. Client balances for spot are Uphold balances, surfaced through our API.

3. **Risk moves with it.**
   Counterparty risk, credit risk, and settlement risk on spot become **Uphold's** risk. DLT's risk engine stops modelling spot exposure entirely and focuses only on the internal derivatives/structured books (Part B, Team 2). This is the single biggest reduction in our risk surface.

### What we must nail down with Uphold (open items to elaborate)

- **Regulatory boundary / who is the executing entity.** Is DLT acting as agent/introducer routing to Uphold (Uphold is principal), or is this white-label where DLT remains the regulated face? This determines licensing, best-execution obligations, and who owns the client relationship of record. **This is the first decision — everything else hangs off it.**
- **Liability & best execution.** If our endpoint forwards to Uphold and Uphold gets a bad fill, who is liable to the client? Need a clear allocation in the integration agreement.
- **Latency & SLA.** A pass-through adds a hop. For spot this is fine, but we need contractual SLAs from Uphold so our client-facing SLA is backable.
- **Data & reporting.** We still need read access to spot fills/settlement data to give clients unified reporting (spot + derivatives in one view). Define the data feed Uphold gives us back.
- **Fee/economics split.** Uphold takes spot execution/settlement economics; DLT's margin comes from the MiFID products and from being the integration/distribution layer. Model this so offloading spot doesn't quietly hand away revenue we can't replace.
- **Failover.** If Uphold's spot endpoint is down, our endpoint is down for spot. Decide whether that's acceptable or whether we keep a minimal fallback.

### Why this is the right move
- **Removes the most duplication, fastest.** Both DLT and Uphold run a spot execution + post-trade stack. Running two is waste. Picking Uphold's and killing ours frees every engineer currently maintaining spot.
- **Shrinks our risk + capital footprint.** No spot inventory, no settlement float, no spot counterparty risk on our books.
- **Access to Uphold's bigger LP lines → capital efficiency.** Beyond removing risk, routing spot into Uphold lets us tap **Uphold's larger liquidity-provider lines** instead of funding our own. That's tighter pricing for clients and materially better **capital efficiency** for us — we don't tie up balance sheet backing spot liquidity we can source from a bigger partner.
- **Lets us be MiFID-pure.** Engineering, risk, compliance, and product all point at one box instead of being split across MiCAR commodity work.

### Financing discipline — no capital extraction until robust
Until the offload is complete and the MiFID engine is proven, **we do not extract capital from the business.** Retained capital stays in to keep the business robust through the transition (offloading spot, standing up the derivatives/structured stack, funding the launch cycle). Capital extraction is a *later*-stage decision, gated on business robustness — not something we do while the model is still being de-risked.

---

## Part B — Team setup to own the MiFID box

**Shape:** 3–4 product/tech teams, each **4–5 engineers + dedicated QA + Product Manager**, plus a **dedicated legal capacity (internal + external)** whose explicit mandate is to turn around **PRIIPs KIDs and product approvals fast** so we can launch new structured/leveraged products on a short cycle.

> Rationale for dedicated legal: in the MiFID box, the rate-limiter on shipping a new structured product (a new BRC, a new principal-protected note, a new tokenised underlying) is not the code — it's the **PRIIPs KID, term sheet, and product governance/approval**. If legal is a shared, queued resource, product velocity dies. We give it dedicated lanes.

### Team 1 — Back Office / Post-Trade & Reporting
- **Owns:** post-trade lifecycle for derivatives & structured products — confirmations, clearing/settlement of the MiFID instruments, lifecycle events (corporate actions, coupon/barrier events on structured notes, expiry/exercise on options), transaction reporting (MiFIR/EMIR), and client statements.
- **Why it's a team, not a function:** this is half our moat. The slide says it — *"risk engine + post-trade are the moat."* Spot post-trade goes to Uphold; **derivatives/structured post-trade stays here and is hard.**
- 4–5 eng + QA + PM.

### Team 2 — Risk
- **Owns:** the **risk engine for the internal books** — margin (initial/variation), real-time exposure, P&L, scenario/stress, limits, liquidation logic, and the risk modelling for structured-product payoffs (barriers, knock-ins/outs, downside protection).
- **Scope tightened by Part A:** because spot risk goes to Uphold, this team models only the MiFID/internal books — deeper, not broader.
- **Crossed/internal books:** handles the netting and crossing of internal flow (the "crossed books" referenced in the takeaways).
- 4–5 eng (quant-leaning) + QA + PM.

### Team 3 — API / Platform
- **Owns:** the MiFID client-facing API surface — the derivatives & structured-product endpoints clients integrate against. Auth, rate limits, versioning, SDKs, sandbox, docs, and the reporting feed for our MiFID instruments.
- **Why central:** owning the API *is* the MiFID strategy. We are the integration and distribution layer for derivatives and structured products — the API is the product clients build on, and whoever owns it owns the relationship. This is not a spot function; it is the front door to everything in the MiFID box.
- 4–5 eng + QA + PM.

### Team 4 — New Products (Financial Engineering + Legal)
- **Owns:** designing and launching new MiFID products — financial engineers structuring payoffs (notes, certificates, options strategies, new underlyings incl. prediction markets), working hand-in-glove with **dedicated legal** to produce the PRIIPs KID, term sheet, and product-governance sign-off for each launch.
- **Composition is mixed by design:** financial engineers + quants + the dedicated internal legal counsel + a PM, with external legal on retainer for jurisdiction-specific and novel-product questions.
- **Includes pricing logic.** The launchpad owns the **pricing engine** for its products — the pricing/valuation logic for each payoff (options, structured notes, yield products) is part of the team's deliverable, not an external dependency. Product structuring and pricing ship together.
- **Mandate:** short, repeatable launch cycle — *"pump out PRIIPs and launch new products quickly."* This is the team that turns "underlyings are open-ended" into shipped product.
- 4–5 people + QA (for the structuring/pricing tooling) + PM.

> **Source note — launchpad squad sizing.** The one-pager specs a leaner *flexible product launchpad* squad: **1 PM/PO + 3 Devs + 1 QA + 1 PjM** (adds a dedicated **Project Manager**, which the other teams don't list). Read this as the **minimum viable launchpad pod** — the unit you replicate per product line — rather than a contradiction of the 4–5 eng team above. Confirm with Max whether the launchpad runs as one lean 6-person pod or scales to the fuller Team-4 shape; the two can co-exist (one pod to start, grow into the larger team).

### Cross-cutting: dedicated Legal capacity
- **Internal:** counsel embedded with Team 4 (and supporting Team 1 on reporting) who lives the product pipeline and owns KID/term-sheet/governance turnaround as a standing function, not ad-hoc.
- **External:** retained firm(s) for jurisdiction coverage, novel instruments, and the Uphold integration agreement (Part A boundary/liability questions).
- **KPI:** time-from-product-spec to launch-ready KID. This is the metric the buyer should care about — it's the speed the market is demanding.

### Cross-cutting: People resources (customer-facing ops)
Beyond the product/tech teams, the setup needs standing **people resources** to run the client relationship — these are not covered by the engineering teams above and must be staffed explicitly:
- **Tech Support** — front-line and integration support for B2B clients and retail: API/integration help, incident triage, and the human layer behind the SLAs. Scales with the retail + EU/Global footprint.
- **Account Management** — owns the ongoing commercial relationship: onboarding, retention, upsell of new launchpad products, and the feedback loop from clients back into Team 4's product pipeline.

> These roles are what make the offering *operable* at retail + global scale. Without them, "own the API / own the relationship" (Team 3's premise) has no human backing when a client actually needs help.

---

## One-page summary

| | What we do | Who runs it |
|---|---|---|
| **Spot trading** | Keep our endpoints, forward to Uphold | **Uphold** |
| **Spot settlement & risk** | Offload entirely | **Uphold** |
| **Derivatives/structured post-trade & reporting** | Build & own — moat | **Team 1 (DLT)** |
| **Risk engine (internal books)** | Build & own — moat | **Team 2 (DLT)** |
| **MiFID client API (derivatives & structured)** | Build & own — distribution moat | **Team 3 (DLT)** |
| **New MiFID products + PRIIPs/legal + pricing** | Build & own — velocity | **Team 4 + Legal (DLT)** |
| **Spot liquidity / LP lines** | Source from Uphold's larger book — capital efficiency | **Uphold** |
| **Tech Support & Account Management** | Build & own — client ops | **People resources (DLT)** |

**Headcount ask:** 4 teams × (4–5 eng + 1 QA + 1 PM) ≈ **24–28 product/tech**, plus **financial engineers/quants in Team 4**, plus **1–2 internal legal + external legal retainer**, plus **customer-facing People resources (Tech Support + Account Management)**. The flexible product launchpad can start as a single lean pod (**1 PM/PO + 3 Devs + 1 QA + 1 PjM**) and scale into the fuller Team-4 shape. The deliberate trade: we shed everything spot to Uphold — offloading not just risk but **balance-sheet drag**, tapping Uphold's LP lines for capital efficiency — and reinvest that headcount + capital into being the best derivatives/structured-product engine in the MiCAR+MiFID world. **No capital is extracted until the business is robust.**
