# Prediction-Market KO Certificate — Calendar Deleveraging Supplement

**Audience:** External legal counsel (LL)
**Purpose:** Supplement to `perpetual-certificate-lawyer-primer.html` v5.0, addressing the prediction-market (PM) asset class specifically. Identifies the dated-vs-perpetual split, the calendar-deleveraging mechanic required to make leverage survivable through resolution, and the smart-contract / prospectus implications.
**Status:** Working draft for counsel discussion. Programme-level mechanics unchanged from base primer.
**Companion documents:** `ko-cert-primer.html` (§2.1, §3.1quinquies, §3.3, §3.5, §S9, Q21), `ko-cert-term-sheet.html`, `structured-products-issuance-primer.md`.

---

## 1. Problem the supplement addresses

The v5.0 base primer treats PMs as one more underlying class on the existing perpetual KO architecture. That is correct for the *daily trading mechanic* — reference price = venue probability quote, barrier adjustment passes through carry, knock-out terminates the cert at residual. It is **not** correct for the *terminal behaviour* of a PM:

- Asset-price classes (BTC, gold, equities) have no terminal event. Mark moves continuously and is bounded only by economics.
- PM classes resolve at a **known terminal time T*** to one of two values (0 or $1 in cents: 0¢ or 100¢). Mark snaps to that value at resolution.

The implication is structural. A 5× long cert sitting at mark 47¢ at T*−1h, where the holder is wrong on resolution, takes a near-instantaneous −100% loss the moment resolution prints. Knock-out triggers, but the holder receives ~zero residual. Across an issuer's book at every major resolution event (US election, Fed decision, World Cup final), the realised P&L profile is bimodal and ugly — and unlike crypto, the timing is fully predictable.

The fix is not a bigger insurance fund. It is **time-decaying leverage**, implemented inside the cert wrapper as a scheduled barrier restrike on the calendar.

---

## 2. Product split — what changes vs. v5.0

### 2.1 Two PM cert formats, not one

Add a second PM cert format alongside the existing perpetual KO format.

| Format | Use case | Resolution handling |
|---|---|---|
| **Perpetual PM KO** *(existing, v5.0)* | Open-ended / indeterminate-resolution PMs (e.g., "Will AGI arrive?", "Will Putin remain head of state?") — no fixed T* | Standard perpetual mechanic; no calendar deleveraging needed |
| **Dated PM KO** *(new — this supplement)* | Fixed-resolution PMs (elections, Fed decisions, scheduled sports, economic releases) — known T* | Calendar deleveraging schedule baked into Final Terms; cert is terminated and settled at T*, no rollover |

Format is fixed at issuance in the Final Terms. Series naming convention extension:

```
Format: PM-[VENUE]-[EVENT_ID]-[DIRECTION]-[KO_BARRIER_BPS]-[T*]

Examples:
PM-POLY-TRUMP2028-LONG-KO38-NOV2028   (dated long, 5× at issuance, resolves Nov 2028)
PM-KALSHI-FEDDEC-SHORT-KO62-DEC2026   (dated short, hawkish cut)
PM-POLY-AGI2030-LONG-KO20              (perpetual long, open-ended underlying)
```

### 2.2 Why this is a Final-Terms split, not a base-prospectus split

Both formats use the same payoff formula, same knock-out trigger, same back-to-back hedge on the same venue universe, same TVTG token *sui generis* wrapper. The only differences are:

- Presence/absence of a `final_resolution_ts` field in the Series parameters
- Presence/absence of a `calendar_deleveraging_schedule` array in the Series parameters
- Final settlement logic at T* (defined in §4 below)

These are Final-Terms variables, not structural product changes. The base prospectus describes the architecture; the Final Terms elects perpetual or dated per Series. This minimises counsel rework against the v5.0 draft.

---

## 3. Calendar deleveraging mechanic

### 3.1 The mechanic in one paragraph

At pre-disclosed calendar boundaries before T*, the dated PM cert undergoes a **Calendar Restrike Event**: the Knock-Out Barrier moves away from the mark, the Contract Size (multiplier) shrinks proportionally, and the certificate's NAV at the moment of restrike is preserved. The holder's token balance is unchanged. The economic effect is that the cert's *forward* leverage drops to the next tier cap; the holder's *realised* P&L up to the restrike is preserved in the new-series NAV. From the holder's perspective, a Calendar Restrike Event is a corporate-action entry, not a cash distribution.

### 3.2 Default schedule (Series-level, defined in Final Terms)

```
T*  −  90d   →   Issuance window (or earlier): lev cap = 5.0×
T*  −  30d   →   Restrike to lev cap = 3.0×
T*  −   7d   →   Restrike to lev cap = 2.0×
T*  −  24h   →   Restrike to lev cap = 1.0×  (spot-equivalent exposure)
T*  −   1h   →   Trading flips reduce-only; no new positions or transfers
T*           →   Final settlement at TWAP of mark over [T*−30min, T*]
```

The schedule is fixed per Series at issuance and printed in the Final Terms. Each tier cap is an *upper* bound; the cert may be issued at a lower leverage and skip earlier restrikes.

### 3.3 NAV-preserving restrike formula

For a long dated PM cert at the moment of a Calendar Restrike Event:

```
At restrike time t_r, given:
  M_t   = mark price at t_r  (probability in cents, 0–100)
  B_old = barrier of pre-restrike Series
  N_old = contract multiplier of pre-restrike Series (cash/cent)
  L_new = target leverage cap of post-restrike Series

Compute the new Series parameters:

  B_new = M_t × (1 − 1 / L_new)                       (new barrier)

  V_t   = (M_t − B_old) × N_old                       (cert NAV at t_r, pre-restrike)

  N_new = V_t / (M_t − B_new)                         (new multiplier — chosen
                                                       so post-restrike NAV equals V_t)

  Cert NAV post-restrike at mark M_t = V_t            (preserved by construction)
  Implied forward leverage           = L_new           (by construction)
```

For short certs, swap `(M_t − B)` for `(B − M_t)` and flip the sign on `(1 − 1/L)`.

The holder's token count is unchanged. The Series identifier on-chain updates from `...-KO38` to `...-KO31` (illustrative). The smart contract treats this as an in-place parameter update on the same security, not as a token swap (see §6.1).

### 3.4 Issuer-side mechanics

At each Calendar Restrike Event, the issuer mechanically deleverages its hedge book on the Reference Venue:

1. Compute the aggregate notional exposure of the Series before restrike: `Σ (token_supply × N_old)`.
2. Compute the aggregate notional exposure after restrike: `Σ (token_supply × N_new)`.
3. The difference is the unwind size. The issuer reduces its back-to-back PM venue position by exactly that amount.
4. Slippage on the unwind is absorbed into the issuer spread; pre-funded in the cert pricing formula at issuance (see §5.1 of base primer for carry passthrough mechanic — calendar passthrough is identical in form).

Unwind dates are pre-disclosed → MMs and venue liquidity providers can quote into the calendar restrike, reducing slippage.

---

## 4. Final settlement at T*

The dated PM cert terminates at the underlying event's resolution time T*. Mechanics:

1. **Trading halt** at T*−1h (reduce-only window — same as the 5y perpetual primer §2.3).
2. **Expiry mark** = 30-minute TWAP of Reference Venue mark over `[T*−30min, T*]`. TWAP rather than last-print mitigates manipulation in the thin pre-resolution window.
3. **Settlement value per cert**:
   ```
   Long:  V_final = max(0, (TWAP_expiry − B_current) × N_current)
   Short: V_final = max(0, (B_current − TWAP_expiry) × N_current)
   ```
   where `B_current, N_current` are the Series parameters following the final (T*−24h) restrike.
4. **Cash distribution** in the cert's settlement currency (EUR or USD per Series) to all holders on the snapshot block immediately before T*.
5. **Delisting**: Series is permanently retired; historical state queryable via the smart-contract event log.

No automatic rollover. A successor Series referencing a later T* (e.g., the next election) may be listed in advance under supplementary Final Terms; holders must opt in.

---

## 5. Worked example — TRUMP2028 long, 5× at issuance

Issuance: 2026-12-01. T* = 2028-11-07 (US presidential election resolution day). 5.0× lev cap at issuance.

| Date | Event | Mark | L target | B | N | NAV/cert | Implied lev |
|---|---|---|---|---|---|---|---|
| 2026-12-01 | Issuance | 47.0¢ | 5.0× | 37.60¢ | $1,000/¢ | $9,400 | 5.00× |
| 2028-10-08 | Restrike T*−30d | 51.0¢ | 3.0× | 34.00¢ | $686.0/¢ | $11,662 | 3.00× |
| 2028-10-31 | Restrike T*−7d | 56.0¢ | 2.0× | 28.00¢ | $470.0/¢ | $13,160 | 2.00× |
| 2028-11-06 | Restrike T*−24h | 62.0¢ | 1.0× | 0.62¢ (≈0) | $214.3/¢ | $13,160 | 1.00× |
| 2028-11-07 | Final settlement (Yes resolves) | TWAP = 99.4¢ | — | — | — | $21,156 | — |

NAV at each restrike is preserved at the moment of the event (rows show pre-restrike NAV carried forward; the new barrier/multiplier are calibrated so that the cert is worth the same dollar amount immediately after the restrike).

In the "No" scenario at T*: TWAP ≈ 0.6¢. Post-final-restrike (1× lev, barrier ≈ 0), the cert behaves as direct spot exposure. Settlement value ≈ `max(0, (0.6¢ − 0¢) × $214.3/¢)` ≈ $128 vs. pre-event NAV of $13,160 — a −99% loss, but the holder was already at 1× exposure for the last 24 hours, so the bimodal terminal jump did not compound through leverage.

**Compare** to a perpetual 5× through resolution: holder would have held ~$13,160 of 5×-leveraged exposure into the snap to 0¢; knock-out triggers near-zero residual. Same dollar outcome in "No"; the calendar deleveraging does not save the holder from being wrong, but it removes the *gap discontinuity* where a small last-mile mark move at high leverage produces an outsized loss inconsistent with the holder's risk budget.

---

## 6. Smart-contract / TVTG implementation

### 6.1 In-place parameter update, not token swap

The Series is a single TVTG token *sui generis* throughout its life. Calendar Restrike Events update the Series' parameters in storage; they do not burn old tokens or mint new ones. Same ISIN (if elected for the Series), same contract address, same token balance per holder.

This is the cleanest path for two reasons:
- **Tax characterisation** — an in-place parameter adjustment under a pre-disclosed schedule is far more defensible as a non-realising event than a token-for-token exchange (see §7).
- **Custody and broker integration** — most qualified crypto custodians (Tangany, Bankhaus von der Heydt) and MiFID-licensed brokers handle parameter updates as a corporate-action data feed; they do not handle automatic token swaps cleanly across the board.

### 6.2 Calendar logic on-chain

At issuance, the contract stores the Series' `calendar_deleveraging_schedule` as an array of `(timestamp, target_leverage_cap)` tuples. A keeper / oracle-triggered function `executeCalendarRestrike()`:

1. Verifies `block.timestamp ≥ next_scheduled_timestamp`.
2. Reads current mark `M_t` from the elected Reference Venue oracle (Kalshi API or Polymarket on-chain feed).
3. Computes `B_new, N_new` per §3.3.
4. Updates Series storage atomically.
5. Emits `CalendarRestrike(seriesId, t_r, M_t, B_old, B_new, N_old, N_new, L_new)`.
6. Issues a hedge-unwind instruction to the issuer's off-chain execution system (or, for Polymarket-referenced Series, executes the unwind on-chain in the same block).

The function is idempotent (will not double-execute for the same scheduled date). Failure modes (oracle stale, hedge unwind partial) are handled by a 24-hour grace window during which the restrike can be deferred — disclosed in the Final Terms.

### 6.3 Reference price during the restrike window

To prevent mark manipulation at the restrike moment, `M_t` in §3.3 is the **30-minute TWAP** of the Reference Venue mark ending at the scheduled timestamp, not the spot mark at the block. Same TWAP methodology as final settlement (§4.2). This is consistent with the v5.0 primer's mark-source rules and does not introduce a new oracle dependency.

---

## 7. Open questions for counsel (extends Q1–Q24 in base primer)

| # | Question |
|---|---|
| Q25 | Does a pre-disclosed, NAV-preserving Calendar Restrike Event on the same TVTG token *sui generis* constitute a "disposal" under German EStG §20 / Austrian KESt / Liechtenstein VStG, or a continuation of the same security? Confirmation sought in each Phase-1 target market (DE, AT, FL). |
| Q26 | For PRIIPS KID purposes, is the scenario calculation required to model the calendar restrike schedule (multiple discrete leverage tiers across the life of the product), or is a worst-case model based on the issuance-tier leverage acceptable? |
| Q27 | MiFID II appropriateness: does the periodic deleveraging materially alter the complexity/risk classification of the cert vs. the perpetual format already covered under Q1–Q3 of the base primer? Implication for retail vs. professional-only distribution. |
| Q28 | ESMA binary-options decision (EU 2018/795): the dated PM cert's payoff is bounded but not strictly binary (residual value between barrier and mark prior to T*). Does the bounded-but-non-binary characteristic remain sufficient to avoid the ban — same analysis as Q21 in v5.0 — given the additional terminal jump at T*? |
| Q29 | For dated PM certs referencing **Polymarket** (on-chain venue): the Final Settlement TWAP is computed from on-chain price data. Does this raise additional Benchmark Regulation (BMR) considerations vs. an exchange-traded perpetual mark? Same question for Kalshi (CFTC-regulated event contracts). |
| Q30 | Gambling-law characterisation under §S9 of the base primer: does a *dated* PM cert with a fixed T* resolve to a different gambling-law analysis than a perpetual PM cert? Specifically — does the time-bounded terminal payoff structure increase or decrease risk of dual securities/gambling regulation in DE / AT? |

---

## 8. Sequencing recommendation

| Phase | Product | Rationale |
|---|---|---|
| **Phase 1A** *(immediate)* | Dated PM KO on top-20 liquid Kalshi events, 5× max, EU professional-only distribution under base prospectus | Cleanest regulatory story; reuses v5.0 architecture; tests calendar restrike mechanic with limited blast radius |
| **Phase 1B** *(+3 months)* | Add Polymarket-referenced Series (same product, on-chain reference); add EU retail distribution after PRIIPS KIDs | Adds the larger PM venue pool once Phase 1A trading + restrike data is in hand |
| **Phase 2** *(+6 months)* | Add Perpetual PM KO for open-ended events (no T*, no calendar deleveraging) — pure v5.0 format | Higher residual gap risk; only after Phase 1 data validates issuer hedge book mechanics |

The dated format leads. Perpetual PM certs remain in the programme but are added once the issuer has live data on a regulated, time-bounded variant.

---

*Working draft for LL discussion. Not legal advice. Mechanics assume v5.0 base primer architecture; any deviation from that primer should be flagged in counsel comments and reconciled before circulation.*
