# Revolut ⇄ AnduinDLT Finance — Crypto Lending Agreement

## REDLINE / COUNTER-COMMENT PACK — 27 Aug 2026

**Base document:** Revolut's new draft (AnduinDLT Finance filled in; margin comments dated 27 Aug 2026).
**Prepared for:** Max / Alan Kennedy, to return to Revolut ahead of the call.

**Convention:** ~~strikethrough~~ = delete, **bold** = insert. Clause numbers follow the new draft.

---

## Summary of the four moves

| # | Item | Position | Action |
|---|------|----------|--------|
| 1 | Minimum term | Re-insert 2-yr floor | Redline Clause 14.2 |
| 2 | Revenue guarantee | **Retainer dropped → replaced by a guaranteed 33% share of drawn capital** (multi-partner model) | New Clause + definition |
| 3 | Liquidity Owner | Remove (already absent) | No action — confirm deletion |
| 4 | Territory (UK/ES/AT/PT) | Accept as drafted | No redline |
| 5 | Clawback | Cap Service Credits at 20% | Redline SLA / Service Credit clause |

---

## 1. Minimum Term — Clause 14.2 (TERM AND TERMINATION)

The new draft is effectively a 90-day contract (Revolut 90-day / Partner 180-day at-will notice, no minimum term). We agreed 2-year minimum. Re-insert it.

> **14.2** ~~We may terminate this Agreement at any time for any reason by giving the Partner not less than ninety (90) days' written notice. The Partner may likewise terminate this Agreement at any time for any reason by giving Revolut not less than one hundred and eighty (180) days' written notice, provided that the Partner continues to fulfil all of its obligations in respect of outstanding Crypto Loans during the notice period.~~
>
> **14.2 This Agreement shall continue in force for an initial minimum period of two (2) years from the Effective Date (the "Minimum Term"), and shall renew automatically thereafter for successive periods of six (6) months (each a "Renewal Term"), unless and until terminated in accordance with this Clause 14. Neither party may terminate this Agreement for convenience during the Minimum Term. After the Minimum Term, either party may terminate with effect from the end of the then-current Renewal Term by giving the other not less than ninety (90) days' prior written notice, provided that the Partner continues to fulfil all of its obligations in respect of outstanding Crypto Loans during the notice period.**

**Note:** the Minimum Term is without prejudice to the parties' rights to terminate for **material breach (14.3)**, on the **mandatory / regulatory grounds (14.4–14.5)**, on a **Change in Law (Cl. 18)**, or on a **Force Majeure Event** — those survive the 2-year lock unchanged.

---

## 2. Revenue guarantee — NEW: Guaranteed Minimum Allocation of Drawn Capital

**What changed:** the March draft's Monthly Retainer (25k→75k→125k EUR + 150k upfront) is gone. In the new commercial model there are **other liquidity partners alongside Anduin**, so a flat retainer no longer fits. Instead, the guarantee is **volume-based**: Revolut guarantees Anduin a **minimum 33% of all drawn capital** (i.e. of aggregate outstanding Crypto Loans across all liquidity partners). That guarantees Anduin's interest income directly, in proportion to the book.

Two pieces: a **definition** and an **operative clause with a true-up** (so it is a real guarantee, not best-efforts).

### 2a. New definition (add to Clause 1 / Definitions)

> **"Aggregate Drawn Capital" means, at any time, the aggregate outstanding principal value of all Crypto Loans disbursed to Clients within the Territory across all liquidity providers on the Revolut Platform (whether the Partner or any other liquidity provider), expressed in the applicable Permitted Crypto Asset(s) or their fiat equivalent.**
>
> **"Guaranteed Minimum Allocation" means thirty-three per cent (33%) of Aggregate Drawn Capital.**

### 2b. New operative clause (add to Clause 7, Lending Pools)

> **7.x — Guaranteed Minimum Allocation.**
>
> **(a) During the Term, Revolut shall allocate the funding of Crypto Loans among liquidity providers such that the aggregate principal value of Crypto Loans funded from the Partner's Lending Pool(s) is at all times not less than the Guaranteed Minimum Allocation, measured as a monthly average of daily balances.**
>
> **(b) The obligation in (a) is conditional on the Partner maintaining, and making available on a Pull Request, Permitted Crypto Assets sufficient to fund at least the Guaranteed Minimum Allocation. To the extent the Partner has failed to maintain such liquidity, the Guaranteed Minimum Allocation shall be reduced, for the affected period, to the amount the Partner was in fact able and willing to fund.**
>
> **(c) True-up. If, in any calendar month, the Partner's monthly-average share of Aggregate Drawn Capital is less than the Guaranteed Minimum Allocation for any reason other than a shortfall attributable to the Partner under (b), Revolut shall pay the Partner, within thirty (30) days of month-end, compensation equal to: (Guaranteed Minimum Allocation − actual funded amount) × the Partner Commission rate applicable in that month, being the Partner Commission the Partner would have earned had the Guaranteed Minimum Allocation been met.**
>
> **(d) Revolut shall provide the Partner with a monthly statement setting out Aggregate Drawn Capital, the amount funded from the Partner's Lending Pool(s), the Partner's share, and any compensation due under (c).**

**Open commercial point for the call:** the reference metric is *drawn* capital (outstanding loans), not committed/undrawn liquidity. Confirm Revolut agrees the 33% floor bites on the *drawn* book, and that the true-up uses the Partner Commission rate (Schedule 1, currently `[●]` — needs filling).

---

## 3. Liquidity Owner provision — confirm removed

Agreed to remove. The new draft **already omits** the Liquidity Owner clause that existed in the March version (third-party liquidity sourcing via DLT Finance). No re-insertion required — flag to Revolut that we are content for it to stay out, and that the multi-partner model in item 2 supersedes it.

---

## 4. Territory — accept as drafted

New draft Schedule 1 = **UK, Spain, Austria, Portugal**. Accepted; no redline. (Alan's open question on *why* the 4-jurisdiction limit can still be raised on the call, but it is not blocking.)

---

## 5. Clawback — cap Service Credits at 20%

Cap the Service Credit / SLA clawback exposure. Add an aggregate cap and make Service Credits the exclusive financial remedy, so failures cannot stack toward a full-fee clawback or trigger fee refunds.

Add to the Service Credit Eligibility clause (SLA schedule):

> **Notwithstanding anything to the contrary in this Agreement or any Schedule, the aggregate Service Credits applied in respect of any single calendar month shall not exceed twenty per cent (20%) of the Partner Commission earned by the Partner in that month. Service Credits shall be the Partner's sole and exclusive liability, and Revolut's sole and exclusive remedy, in respect of any Service Failure, and no Service Failure shall entitle Revolut to any refund, set-off, deduction or clawback of fees in excess of this cap.**

And in the Critical Service Failure termination clause, delete the fee-refund tail:

> ~~…Revolut shall be entitled to a pro-rata refund for any fees paid in advance up until the termination date…~~

**Note:** Schedules 2 (Liquidity SLA) and 3 (Liquidation SLA) in the new draft are still `[to be confirmed]`. The 20% cap above should be stated as an overarching cap in the Agreement body so it governs whatever numbers land in those schedules.

---

## Still-open blanks to resolve before signing (not changes, just gaps)
- **Schedule 1:** Partner Commission rate `[●]`, Revolut Commission `[●]`, Reserve Pool Interest rates (BTC/ETH/USDC/SOL `[●]`), Liquidation Fees `[●]`, Lending Pool thresholds `[●]`.
- **Schedules 2 & 3:** Liquidity SLA and Liquidation SLA both `[to be confirmed]`.
- **Long Stop Date:** 30 Nov 2026 to sign (Clause 14.1) — matches Alan's note.
