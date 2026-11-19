# MILESTONE_1_PLAN.md — Historical EUR/USD Scanner & Decision Log

**Owner:** Marcelo Bayon · **Version:** v0.2 (DRAFT) · **Updated:** 2026-11-19
**Scope gate:** no paper execution in this milestone. Guiding principle: honest measurement, not a flattering backtest.

## Three questions, kept separate (do not conflate)

These are **sequential, independent gates**. Passing one does **not** prove the next.

- **Gate 1 — Reproduction:** does the engine reproduce what Marcelo observed? (journal reconciliation)
- **Gate 2 — Objective identification:** does it identify the setup consistently from explicit rules, without his eye? (engine validation on history)
- **Gate 3 — Measurable edge:** does the setup pay after costs? (historical here; forward testing later)

Milestone 1 delivers Gate 1 and Gate 2, and the *apparatus* for Gate 3 — it does **not** claim an edge. A scanner that can explain precisely why it skipped 100 opportunities is the goal, not an impressive backtest number.

## Deliverables

Foundation (no PI coupling) · OANDA historical ingestion (bid/ask/mid, quality-stamped) · deterministic indicators · strategy engine over the candidate grid · **decision log** · **journal-reconciliation report** · test suite (determinism, no look-ahead, rule logic).

## Data-split methodology (anti-overfitting) — approved in principle

- Time-ordered **~60 / 20 / 20** dev / validation / out-of-sample over the ingested **M5/M15 history** (which is deep, independent of the journal). Hold-out examined **once**.
- **Before implementation, document the actual calendar boundaries and candle counts** of each split (a required P2 output, not an assumption).
- The **journal period is a reconciliation set only** — never used to tune profit.
- No "run all combinations, keep the winner." Pre-registered comparison with multiple-testing correction; full grid reported.

## Journal handling (enumerated, preserved, unverified for performance)

Verified from the live Sheet + pasted CSV on 2026-11-19:

- **22 dated entries total**, range **02/09/2025 → 03/25/2026**.
- **Feb 2025 (02/09, 02/10):** two entries, a year before the main log; 02/09 is "Not Entered," 02/10 a no-trade. → Handled as a **separate pre-period bucket**, flagged, **excluded from the 2026 reconciliation set and from any tuning.** Not deleted.
- **2026 window (02/16 → 03/25):** the **reconciliation set** (20 entries: ~6 executed demo trades, the rest no-trades/skips).
- **April 2026 entries: NOT FOUND** in Journal, Trades, or report tabs. **Open item — source needed from Marcelo.** Not invented, not silently dropped.
- **Performance columns are UNVERIFIED and are not ground truth.** Confirmed inconsistencies, preserved verbatim, flagged (not overwritten):
  - 02/18/2026 — Close "1.8403" and Net "0.65644" are impossible for EUR/USD (data error).
  - 03/03/2026 — short narrative (price fell, "i did pake a profit") conflicts with logged Net "-0.00154" (sign error).
  - 02/16, 02/17 — Net signs vs "Profitable=TRUE" inconsistent; "Risk Tolerance" column mixes lot size / "pips" / tiny decimals across rows.
- Outcomes are **reconstructed only where source information supports it** (entry/exit prices + direction), and the reconstruction is labeled *inferred*, distinct from Marcelo's *recorded* values.

### Reconciliation report categories (Gate 1)

For each 2026 entry, the engine's decision vs Marcelo's documented decision is classed as:
- **Agree** (both trade / both skip, same direction),
- **Disagree** (one trades, the other skips, or opposite direction),
- **Cannot determine intent** (his note is ambiguous or data is insufficient).
His **original reasoning/notes are preserved and shown** beside each.

## Execution-pricing model (for outcome measurement)

- **Indicators computed on bid candles** (to match what Marcelo read on MT5/TradingView).
- **Outcomes modeled with bid/ask:** long entry = ask, long exit = bid; short entry = bid, short exit = ask.
- **Spread + slippage included** in net results. OANDA historical pricing is **not assumed** to equal MT5/TradingView experience — the difference is measured (see verification B).

## Provenance & quality (architectural requirement)

Every decision-log row is traceable to: the exact candle ids used, their **provider + data-quality status**, the **operating-config version**, and the **strategy-variant version**. A low-quality or questionable input is flagged and **blocks** a confident signal rather than silently producing one.

## Phased tasks

**P1 — Foundation (strategy-agnostic; cleared to proceed).** Repo skeleton; Supabase project; migrations for versioned operating-configs, strategy-definitions/variants, instruments, candles (with provenance + quality). Capability-flag config. Auth/RLS. Health endpoint.

**P2 — Market data + verifications.** Broker-independent provider interface; OANDA adapter (data-only); ingest EUR/USD 15m+5m (bid/ask/mid); normalize + quality-stamp; integrity checks (dup/missing/out-of-order/stale). **Document split boundaries + candle counts.** **Verification A:** actual S5 depth + pagination cost. **Verification B:** historical-candle vs live-stream price difference vs MT5/TradingView. (A/B require the practice token.)

**P3 — Indicators.** Deterministic EMA for both candidate sets, completed candles only, bid basis, warm-up documented.

**P4 — Strategy engine.** Pure function `(candles, config, variant) → signal{status, rules_passed, rules_failed, entry, stop, target, R, provenance, explanation}`. Candidate dimensions as configurable switches. **Not started until candidate definitions + acceptance tests are signed off.**

**P5 — Backtest replay + decision log.** Chronological, no look-ahead, reproducible; one explained row per in-window bar.

**P6 — Reconciliation + reporting.** Engine-vs-journal agreement report (categories above); per-variant performance summary under the split methodology, net/R/CI, full grid.

## Acceptance tests (Milestone 1) — sign-off needed before P4

- EMA matches an independent hand-computed fixture; warm-up correct; **bid basis** verified.
- No look-ahead provable by replay; forming candle never read.
- Window/day enforced in `America/Denver` across a DST boundary.
- **Each candidate dimension (A1/A2, B1/B2, C1/C2/C3, D1/D2/D3, E1/E2, F1/F2/F3, G) has passing + failing fixtures.**
- Transition conditions C1/C2/C3 fire **independently** on crafted cases (not merged).
- Determinism: same candles → identical decision log.
- Bid/ask outcome model applies correct side per direction; spread/slippage included.
- Intrabar stop/target ambiguity resolved by S5 where available, else documented conservative tie-break (assume stop first) — both paths tested.
- Provenance present on every row; low-quality input blocks a signal.
- Reconciliation runs over the 2026 set and classifies agree/disagree/cannot-determine; Feb 2025 kept separate; April flagged as missing.

## Deferred to Milestone 2+

Paper accounts; **position sizing computed from stop distance + chosen dollar risk** (eliminates the lot/pip/risk confusion the journal shows); simulated fills; stop/target execution; duplicate-order protection; approval/auto modes; dashboard; alerts; AI assistant.

## Blockers / dependencies

OANDA practice token (for P2 verifications) · candidate + acceptance-test sign-off (for P4) · April-2026 journal source (for complete reconciliation) · journal workbook versioned/exported for the reconciliation set.
