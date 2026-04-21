# EUR/USD Receivable FX Hedge – Technical Specification

**Created by:** Mechelle Luo  
**Updated by:** Mechelle Luo 
**Date Created:** April 20, 2026  
**Date Updated:** April 20, 2026  
**Version:** 1.0  
**LLM Used:** Claude Sonnet 4.6

**Role:** Treasury Analyst  
**Audience:** Adam Stauffer, Chief Financial Officer  

**Purpose:** Provide a professional, quantitative specification outlining the analytical structure for evaluating FX hedging alternatives on a €20M aerospace receivable with a 180-day settlement horizon.

---

## 1. Problem Statement

Our aerospace manufacturing division expects to receive EUR 20,000,000 from a European customer approximately 180 days from the trade date. This receivable is denominated in euros while the firm reports earnings and funds operations in US dollars, creating a material transaction exposure to fluctuations in the EUR/USD rate.

At the current spot and forward rate of 1.0930, the base-case USD equivalent is **$21,860,000**. EUR/USD six-month historical volatility typically runs 8–12 big figures, meaning a 10-cent adverse move — well within normal range — would cost over $2.0M in foregone USD proceeds, equivalent to roughly a full margin point on the underlying contract.

- **Exposure type:** Foreign currency receivable (long EUR, short USD)  
- **Foreign currency amount:** EUR 20,000,000  
- **Time horizon:** ~180 calendar days from April 14, 2026  
- **Objective:** Protect the USD value of the receivable while preserving optionality where cost-effective  
- **Decision context:** Corporate treasury; requires CFO sign-off before hedge execution

> *The firm's P&L is directly impacted by exchange-rate drift between invoice date and settlement. Leaving this position unhedged is a deliberate financial decision, not a default — and one with quantifiable downside.*

---

## 2. Inputs (Known Variables)

| Variable | Description | Unit | Value | Source |
|---|---|---|---|---|
| `FC_AMT` | EUR receivable notional | EUR | 20,000,000 | Contract invoice |
| `S₀` | EUR/USD spot rate today | USD/EUR | 1.0930 | Reuters FX, Apr 14 2026 |
| `F₀` | 6-month outright forward rate | USD/EUR | 1.0930 | Bank quote, Apr 14 2026 |
| `r_USD` | USD interest rate, annualised | % | 5.25% | SOFR + spread |
| `r_EUR` | EUR interest rate, annualised | % | 3.85% | €STR + spread |
| `t` | Time to maturity | Years | 0.50 (180 days / 360) |  Derived from contract terms |
| `K_put` | EUR put strike | USD/EUR | 1.0700 | Analyst choice; ~2% OTM |
| `K_call` | EUR call strike (collar short leg) | USD/EUR | 1.1200 | Analyst choice; ~2.5% OTM |
| `Premium_put` | Put premium | USD per EUR | 0.0085 | Market indicative (~0.78% notional) |
| `Premium_call` | Call premium received | USD per EUR | 0.0085 | Set equal to Premium_put for ZCC |

> All variables are centralised in the **Inputs** tab, column C, rows 5–16. Yellow-filled cells are editable. No values are hardcoded outside this tab.

---

## 3. Assumptions & Constraints

- Exchange rates are expressed as USD per EUR (direct quote from a USD-base perspective).
- Interest rates are annualised on an actual/360 day-count basis (standard money market convention for both USD and EUR).
- The forward rate (`F₀`) is treated as a market quote. The Money Market Hedge tab independently verifies it via Interest Rate Parity; a divergence greater than 5 basis points triggers a warning flag.
- Option premiums (`Premium_put`, `Premium_call`) are flat analyst inputs, not derived from an implied volatility surface or Black-Scholes model. They represent indicative market levels only.
- The Zero-Cost Collar assumes `Premium_put = Premium_call` (net zero cash premium). If live premiums differ, the net residual cost or credit is displayed explicitly.
- Settlement is a single bullet date; no amortising or multi-tranche receivable structure is modelled.
- Transaction costs, bid-ask spreads on the forward, and counterparty CVA adjustments are excluded.
- The hedge is modelled at 100% of `FC_AMT`; partial hedge ratios are out of scope for this version.

---

## 4. Calculation Flow

1. **Compute USD proceeds under the forward hedge.** Multiply `FC_AMT` by `F₀` to establish the certainty benchmark: the locked-in USD receipt regardless of where spot settles.

2. **Recreate a synthetic forward using money market replication.** Borrow the present value of the EUR receivable today (`FC_AMT / (1 + r_EUR)^t`), convert to USD at spot, and invest at `r_USD` for `t` years. The terminal USD value should approximate the forward proceeds within 5 bps; this cross-check validates the quoted forward rate via Interest Rate Parity.

3. **Compute long put option outcomes across a range of settlement spot rates (`S_T`).** For each scenario: add the put payoff (`MAX(K_put − S_T, 0) × FC_AMT`) to the spot conversion (`S_T × FC_AMT`), then subtract the upfront put premium (`Premium_put × FC_AMT`). This gives net USD proceeds at each `S_T`.

4. **Compute zero-cost collar outcomes across the same `S_T` range.** For each scenario: take the spot conversion, add the put payoff, subtract the call liability (`MAX(S_T − K_call, 0) × FC_AMT`), and subtract any net premium residual (`(Premium_put − Premium_call) × FC_AMT`). At base inputs this residual is zero.

5. **Build a sensitivity table sweeping `S_T` from 0.95×S₀ to 1.05×S₀ in 1% increments (11 rows).** Present all four strategies — unhedged, forward, long put, and collar — side-by-side. Flag the best-performing strategy at each rate. Apply conditional formatting to the "vs. Forward" column to highlight crossover points.

6. **Summarise all four strategies at the current forward rate on a single dashboard tab.** Report gross USD, premium cost, net USD proceeds, and delta versus unhedged. Include six key risk metrics: 1-cent move impact, 10-cent move impact, forward breakeven, put effective floor, collar floor, and collar cap.

---

## 5. Outputs

| Output | Description | Format | Purpose |
|---|---|---|---|
| `USD_forward` | USD proceeds from forward contract | Single value: $21,860,000 | Certainty benchmark |
| `USD_mm` | USD proceeds from money market replication | Single value: ~$21,816,069 | Parity cross-check vs. forward |
| `USD_put` | Net USD proceeds from long put across S_T range | 11-row table | Downside protection with upside participation |
| `USD_collar` | Net USD proceeds from zero-cost collar across S_T range | 11-row table | Cost-free floor with capped upside |
| `Chart_1` | Hedge outcomes vs. S_T (four series) | Line chart | Visual crossover and breakeven analysis |
| `Best_Strategy` | Highest-returning hedge at each S_T | Labelled column in sensitivity table | Quick decision reference |
| `Risk_Metrics` | 1-cent / 10-cent move impact, floors, caps | 6-row block in Summary tab | CFO-ready risk sizing |
| `Summary` | Side-by-side strategy comparison at base forward rate | Formatted dashboard tab | Executive decision support |

---

## 6. Sensitivity Plan

Vary the EUR/USD settlement spot rate (`S_T`) from 0.95×S₀ to 1.05×S₀ — approximately 1.0384 to 1.1477 — in increments of 1% of S₀ (11 scenarios total).

For each value of `S_T`, compute net USD proceeds under all four hedge strategies using the formulas described in Section 4. Present results in a single comparison table with one row per scenario and one column per strategy. Apply a red-to-green conditional formatting gradient on the "vs. Forward" column: red where the put underperforms the locked forward, green where it outperforms.

Display results as both the comparison table and a four-series line chart with `S_T` on the x-axis and USD proceeds on the y-axis. The chart's visual crossover points identify the exact spot rates at which each strategy dominates.

> The ±5% band covers approximately one standard deviation of EUR/USD movement over a 6-month horizon at 10% annualised volatility — a range that encompasses the majority of realistic settlement outcomes without including extreme tail scenarios.

---

## 7. Limitations & Next Steps

**Analytical limitations of this version:**

- **Option premiums are static inputs.** Premiums are not computed from an implied volatility surface or Black-Scholes model. A Garman-Kohlhagen module should replace `Premium_put` and `Premium_call` for a live-ready implementation.
- **No partial hedge ratios.** The model assumes 100% of `FC_AMT` is hedged under each strategy. A hedge ratio parameter (0–100%) driving a blended proceeds calculation is excluded.
- **Single settlement date only.** Multi-tranche or amortising receivables with multiple settlement dates are not supported.
- **No probability weighting.** The sensitivity table treats all S_T scenarios as equally likely. No VaR, Expected Shortfall, or Monte Carlo distribution is computed.
- **Transaction costs excluded.** Bid-ask spreads on the forward and option premiums, counterparty CVA, and hedge accounting considerations (ASC 815 / IFRS 9) are not modelled.

**Immediate next step — Stage 4: Decision Framework & CFO Recommendation**

Stage 4 will consume the sensitivity table and summary dashboard from this model to produce a single-page hedge recommendation memo for Adam Stauffer. It will include: the recommended strategy and rationale, expected USD proceeds under base/bear/bull scenarios, the proposed hedge ratio, execution steps (counterparty selection, ISDA confirmation, settlement instructions), and plain-English treasury policy language suitable for board-level review.

---

## 🔗 How This Spec Connects to Each Stage

| Stage | What This Spec Enables |
|---|---|
| **Stage 2** | Each Input variable became a named cell in the Inputs tab; each Output became a labelled result in its respective tab. Model file: `FX_Hedge_Model_Stage2.xlsx`. |
| **Stage 3** | This document. Captures what was built, documents the logic precisely enough for reconstruction, and designs the improved version. |
| **Stage 4** | The Calculation Flow becomes the AI prompt instruction block; the Outputs table drives the interpretation, scenario narrative, and CFO recommendation memo. |
