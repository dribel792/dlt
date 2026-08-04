# MiFID Focus — Personnel & Setup Wishlist

**Purpose:** Define the operating setup that lets DLT put 100% of its energy on building out the **MiFID box** — leverage products (perps, options, turbos, mini-futures), structured yield + downside protection (covered calls, zero-interest credit, principal-protected notes, BRCs/Sprinters), and Delta-1 security tokens (tokenised equities, ETFs, commodities) — across all underlyings.

**What we're building:** the genuine moat — the **risk engine + post-trade/reporting layer** for derivatives and structured products, the **client-facing API** clients integrate against, and a **product launchpad** that ships new structured/leveraged products on a short cycle. This is where we invest engineering, capital, and legal velocity.

**Scope & coverage:** The MiFID engine serves **both B2B clients and retail**, with the **product launchpad targeting EU + Global** reach (MiCAR/MiFID-native in the EU, extensible to other jurisdictions). Retail is an explicit distribution channel, not just an institutional API play.

---

## Part A — What we add: the MiFID box

We concentrate the whole organisation on the derivatives & structured-products stack. The moat is two things the slide calls out — the **risk engine** and the **post-trade/reporting layer** — wrapped in a **client API** and fed by a **fast product launchpad**. Everything below is built and owned by DLT.

### The four things we build

1. **A derivatives & structured-products post-trade + reporting engine.** Confirmations, settlement, lifecycle events (coupons, barriers, expiry/exercise), MiFIR/EMIR reporting, client statements — for instruments that are genuinely hard to service. This is half the moat.

2. **A risk engine for the internal books.** Margin, real-time exposure, P&L, stress/scenario, limits, liquidation, and the modelling for structured payoffs (barriers, knock-ins/outs, downside protection). This is the other half of the moat.

3. **A MiFID client-facing API.** The derivatives & structured-product endpoints clients build on — auth, SDKs, sandbox, docs, reporting feed. Owning the API *is* the strategy: whoever owns the integration surface owns the relationship.

4. **A product launchpad.** Financial engineers + quants + dedicated legal that structure payoffs and turn around PRIIPs KIDs fast, so new products (a new BRC, a new principal-protected note, a new tokenised underlying) ship on a short, repeatable cycle.

### Financing discipline — no capital extraction until robust
Until the MiFID engine is proven, **we do not extract capital from the business.** Retained capital stays in to keep the business robust while we stand up the derivatives/structured stack and fund the launch cycle. Capital extraction is a *later*-stage decision, gated on business robustness — not something we do while the model is still being de-risked.

---

## Part B — Team setup to own the MiFID box

**Shape:** 3–4 product/tech teams, each **4–5 engineers + dedicated QA + Product Manager**, plus a **dedicated legal capacity (internal + external)** whose explicit mandate is to turn around **PRIIPs KIDs and product approvals fast** so we can launch new structured/leveraged products on a short cycle.

> Rationale for dedicated legal: in the MiFID box, the rate-limiter on shipping a new structured product (a new BRC, a new principal-protected note, a new tokenised underlying) is not the code — it's the **PRIIPs KID, term sheet, and product governance/approval**. If legal is a shared, queued resource, product velocity dies. We give it dedicated lanes.

### Team 1 — Back Office / Post-Trade & Reporting
- **Owns:** post-trade lifecycle for derivatives & structured products — confirmations, clearing/settlement of the MiFID instruments, lifecycle events (corporate actions, coupon/barrier events on structured notes, expiry/exercise on options), transaction reporting (MiFIR/EMIR), and client statements.
- **Why it's a team, not a function:** this is half our moat. The slide says it — *"risk engine + post-trade are the moat."* Derivatives/structured post-trade is genuinely hard and stays here.
- 4–5 eng + QA + PM.

### Team 2 — Risk
- **Owns:** the **risk engine for the internal books** — margin (initial/variation), real-time exposure, P&L, scenario/stress, limits, liquidation logic, and the risk modelling for structured-product payoffs (barriers, knock-ins/outs, downside protection).
- **Focus:** models the MiFID/internal books — deep, specialised risk, not broad commodity coverage.
- **Crossed/internal books:** handles the netting and crossing of internal flow (the "crossed books" referenced in the takeaways).
- 4–5 eng (quant-leaning) + QA + PM.

### Team 3 — API / Platform
- **Owns:** the MiFID client-facing API surface — the derivatives & structured-product endpoints clients integrate against. Auth, rate limits, versioning, SDKs, sandbox, docs, and the reporting feed for our MiFID instruments.
- **Why central:** owning the API *is* the MiFID strategy. We are the integration and distribution layer for derivatives and structured products — the API is the product clients build on, and whoever owns it owns the relationship. It is the front door to everything in the MiFID box.
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
- **External:** retained firm(s) for jurisdiction coverage, novel instruments, and partner integration agreements.
- **KPI:** time-from-product-spec to launch-ready KID. This is the metric the buyer should care about — it's the speed the market is demanding.

### Cross-cutting: People resources (customer-facing ops)
Beyond the product/tech teams, the setup needs standing **people resources** to run the client relationship — these are not covered by the engineering teams above and must be staffed explicitly:
- **Tech Support** — front-line and integration support for B2B clients and retail: API/integration help, incident triage, and the human layer behind the SLAs. Scales with the retail + EU/Global footprint.
- **Account Management** — owns the ongoing commercial relationship: onboarding, retention, upsell of new launchpad products, and the feedback loop from clients back into Team 4's product pipeline.

> These roles are what make the offering *operable* at retail + global scale. Without them, "own the API / own the relationship" (Team 3's premise) has no human backing when a client actually needs help.

---

## Supporting arrangement — spot runs on Uphold's rails

So the teams above can stay fully focused on the MiFID box, **spot execution and settlement run on Uphold's rails**. DLT keeps the client-facing contract (our brand, endpoints, SLAs); behind our endpoints, spot order flow, custody, and settlement route into Uphold's systems. Clients see no change to their integration.

Two concrete benefits:
- **Capital efficiency via Uphold's LP lines.** Routing spot through Uphold lets us tap **Uphold's larger liquidity-provider lines** instead of funding our own — tighter pricing for clients and materially better capital efficiency for us, since we don't tie up balance sheet backing spot liquidity we can source from a bigger partner.
- **Focus.** Engineering, risk, and product concentrate on the MiFID box rather than being split across commodity spot infrastructure.

**Open items to nail down with Uphold:** the regulatory boundary (who is the executing entity), liability/best-execution allocation, latency/SLA backing, the spot data feed we get back for unified client reporting, the fee/economics split, and failover behaviour.

---

## One-page summary

| | What we do | Who runs it |
|---|---|---|
| **Derivatives/structured post-trade & reporting** | Build & own — moat | **Team 1 (DLT)** |
| **Risk engine (internal books)** | Build & own — moat | **Team 2 (DLT)** |
| **MiFID client API (derivatives & structured)** | Build & own — distribution moat | **Team 3 (DLT)** |
| **New MiFID products + PRIIPs/legal + pricing** | Build & own — velocity | **Team 4 + Legal (DLT)** |
| **Tech Support & Account Management** | Build & own — client ops | **People resources (DLT)** |
| **Spot execution / settlement / LP lines** | Run on Uphold's rails — capital efficiency | **Uphold (supporting)** |

**Headcount ask:** 4 teams × (4–5 eng + 1 QA + 1 PM) ≈ **24–28 product/tech**, plus **financial engineers/quants in Team 4**, plus **1–2 internal legal + external legal retainer**, plus **customer-facing People resources (Tech Support + Account Management)**. The flexible product launchpad can start as a single lean pod (**1 PM/PO + 3 Devs + 1 QA + 1 PjM**) and scale into the fuller Team-4 shape. The deliberate bet: concentrate headcount + capital on being the best derivatives/structured-product engine in the MiCAR+MiFID world, with spot running efficiently on Uphold's rails. **No capital is extracted until the business is robust.**
