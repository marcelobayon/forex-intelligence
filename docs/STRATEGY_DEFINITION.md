# STRATEGY_DEFINITION.md — Candidate Strategy Worksheet

**Owner:** Marcelo Bayon · **Version:** v0.2 (DRAFT) · **Updated:** 2026-11-19
**Status:** DRAFT. **Nothing here is an approved production rule** except the operating frame (§1). All "candidate" items are hypotheses to be investigated, never represented as final.

> **Legend:** 🟩 APPROVED frame · 🟨 CANDIDATE (investigate, not assumed) · ⬜ OPEN decision
> **Evidence legend:** *Documented* = Marcelo's verbatim journal words. *Inferred* = a rule the team derived (not confirmed).
> **Guiding principle:** honest measurement, not a flattering backtest. No variant is adopted because it scored highest.

---

## 0. Three distinct questions this project must not conflate

Per Marcelo's framing — these are **separate achievements; passing one does not prove the next:**

1. **Reproduction** — can the software reproduce what Marcelo observed? (journal reconciliation)
2. **Objective identification** — can it identify the setup consistently with explicit rules, independent of his eye? (engine validation)
3. **Measurable edge** — does the setup have an edge after costs? (historical, later forward)

This worksheet defines #2's candidates. It does **not** assert #3.

## 1. Operating frame — 🟩 APPROVED

EUR/USD · Mon–Thu · 03:00–04:00 America/Denver · trend 15m · entry 5m · one trade per session · starting R:R target 1:1 (revisitable) · indicators on completed candles only · No-trade is a valid, never-forced outcome.

## 2. Candidate dimensions — 🟨 INVESTIGATE (orthogonal switches)

### A. EMA set
- **A1:** 20 / 50 / 200 · **A2:** 20 / 50 / 100 / 200
- *Documented:* "the 100 ema was crossing the 200 ema on the 15 minute" (02/25); "20 ema is crossing the 50 and 100 on the 5 minute" (03/12).
- *Inferred:* the 100 EMA **may** add filtering. **Not mandatory** — kept as a candidate because Marcelo referenced it, to be proven or dropped on evidence, never assumed required.

### B. Directional-bias timeframe requirement
- **B1:** 15m defines bias · **B2:** 15m **and** 5m must agree
- *Documented:* "50 ema is above the 200 on the 15 but below on the 5 min so no trade" (02/26).
- *Inferred:* alignment was **sometimes** required; whether consistent or situational is an open empirical question.

### C. Transition / entanglement — **three distinct conditions, tested separately (do NOT merge into one rejection rule)**
- **C1 — actual cross:** a watched EMA pair has crossed within the last _N_ bars. *Documented:* "The 20 ema is crossing the 200 ema so the market it is in a transition … i am not going to trade" (02/23).
- **C2 — approaching/imminent cross:** EMAs converging toward a cross (separation shrinking below _X_, or "about to cross"). *Documented:* "the 20 ema was about to cross the 200 ema" (02/25).
- **C3 — cross-timeframe disagreement:** 15m and 5m imply different directions. *Documented:* "on the 15 minute though it does look like it is down trending" vs 5m crossing (02/24).
- These are measured and reported **independently**; they are not collapsed into one identical filter. Whether each should block a trade is tested, not assumed.

### D. Pullback definition — **test three**
- **D1:** retrace toward the **20 EMA** · **D2:** retrace toward the **200 EMA** · **D3:** retrace defined by **price/candle structure**, no EMA touch required.
- *Documented:* "the pull back to me was the candle stick being pushed back up to the 200 ema. not all the way but back in the direction of the 200 ema" (03/09); other entries reference the 20 EMA / general pullbacks.
- *Purpose (Marcelo's words):* discover "whether the EMA is essential to the setup or simply a reference I used to recognize a pullback."

### E. Confirmation candle — **test both**
- **E1:** 1 closed 5m candle in trend direction · **E2:** 2 closed candles.
- *Documented (his open question):* "should i have waited for the second to close as the confirmation?" (03/09).

### F. Structural stop anchor — **test three**
- **F1:** pullback-candle wick · **F2:** confirmation-candle wick · **F3:** recent swing high/low.
- *Documented (his open question):* "should the … stop loss be the wick of the pull back candle or the wick of the confirming candle when shorting?" (03/03).
- Stop distance becomes a **rule output**, not an ad-hoc choice. **No universal maximum stop distance is imposed** — the "70 pips felt too large" concern (02/18) is logged as a question to investigate, not converted into an invented threshold.

### G. Orderly-market / entry-distance filters — 🟨 **experimental candidates only, not established rules**
- Candidate guards: skip if spread > _S_; skip if entry > _D_ pips (or _D_×ATR) from reference. Investigated as hypotheses; not treated as part of the baseline strategy unless evidence supports them.

## 3. Methodology (anti-overfitting) — 🟩 APPROVED

- **Pre-registered grid** (this doc) locked before out-of-sample is examined.
- **No automated "run every combination, pick the highest."** A controlled, pre-registered comparison that **accounts for multiple testing**; all tested variants reported with sample sizes, costs, and results.
- Time-ordered **dev / validation / out-of-sample** split; hold-out viewed **once**.
- Journal period used **only** for reconciliation (#1 above), never tuning.
- **Prefer the simpler variant** when differences are not meaningful; added conditions (A2, C2, filters) must *earn* their place.
- Everything reported **net of modeled costs, in R, with sample sizes and confidence intervals.** No profitability claim from small samples.

## 4. Provenance (applies to every rule evaluation)

Every signal/decision is traceable to the exact candles (with source + quality status), operating-config version, and strategy-variant version used. A questionable or low-quality input is flagged and **never silently becomes a confident decision.**

## 5. What the journal does NOT yet define (honest gaps)

Threshold values _N, X, K, S, D_; whether A2/B2/C-conditions/filters are mandatory or situational; position sizing (deferred to Milestone 2 — computed from stop distance + chosen dollar risk, removing the lot/pip/risk confusion the journal repeatedly shows).

---

## 6. Decisions still requiring your approval

1. **Reference-EMA question (D) resolved** → you chose D1/D2/D3 all tested. ✅ (recorded)
2. **Acceptance tests for each candidate** — before P4 strategy coding begins, you asked that candidate definitions and their acceptance tests be sufficiently specified. Those test specs are drafted in `MILESTONE_1_PLAN.md` §Acceptance and need your sign-off.
3. **Threshold search ranges** — the values of _N, X, K, S, D_ to scan (and the multiple-testing correction) need your okay before tuning, so the search space is pre-registered, not fished.
4. **Any remaining mis-statement** — flag any *inferred* rule above that misreads what you did.

*No strategy rule is marked official until it survives §3 on out-of-sample data and you approve it.*
