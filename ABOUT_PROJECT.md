# ABOUT_PROJECT.md — Forex Intelligence

**Owner:** Marcelo Bayon · **Version:** v0.2 (DRAFT)
**Status:** Phase 0 approved · P1 foundation cleared to proceed · strategy NOT approved
**Last updated:** 2026-11-19
**Companion docs:** `MILESTONE_1_PLAN.md`, `STRATEGY_DEFINITION.md`

> Purpose: an **honest measurement system** that identifies and explains Marcelo's historical EUR/USD setups — not a bot built to look profitable, and not a real-money trader.
> **DRAFT notice:** candidate strategy rules in the companion docs are hypotheses under investigation, never approved production rules.

---

## 1. What this is

A personal, paper-only platform to (a) ingest trustworthy EUR/USD data, (b) evaluate a documented, deterministic strategy, and (c) record and explain every would-be decision — so the strategy can be *measured* before it is trusted. First proof required: the engine can explain, bar by bar, why it would or would not take each setup.

## 2. Three questions this project refuses to conflate

Marcelo's framing, treated as a core design constraint — **separate achievements; passing one does not prove the next:**

1. **Reproduction** — can the software reproduce what Marcelo observed? (journal reconciliation)
2. **Objective identification** — can it identify the setup consistently from explicit rules, independent of his eye? (engine validation)
3. **Measurable edge** — does the setup pay after costs? (historical, then forward)

A scanner that explains precisely why it skipped 100 opportunities beats an impressive-but-unreliable backtest. We will not rush to a running bot.

## 3. Governing principles

1. **Honest measurement over flattering results.** Overfitting assumed until disproven; no variant chosen by raw profit; multiple-testing accounted for; simpler preferred when differences aren't meaningful.
2. **Deterministic engine.** Same candles → same indicators → same decisions. No LLM decides trades.
3. **Explainability + provenance.** Every decision traces to the exact candles (with source + quality status), operating-config version, and strategy-variant version. **A questionable input never silently becomes a reliable result.**
4. **Paper-only, enforced structurally.** No real-money path in the codebase — absent, not disabled. No live credentials, no live endpoint, no paper→live toggle.
5. **The journal calibrates; it does not score.** Source material for defining rules, not a performance dataset. Original wording preserved; documented facts kept distinct from inferred interpretations.
6. **Separation of concerns.** Operating config, strategy definition, strategy version, market data, and (later) execution are isolated.

## 4. Architecture — APPROVED

- Standalone app: own `forex-intelligence` repo, own Netlify, own Supabase. **No coupling to Portfolio Intelligence; PI is never imported or modified.**
- Reuse PI *conventions* by copying patterns: ordered SQL migrations as source of truth; boot-without-config capability flags; AES-256-GCM encryption of provider credentials (server-only); RLS everywhere; scheduled-function → secret-guarded API route; integer minor units for money; **FX prices as fixed-scale decimals, never float.**
- **Broker-independent market-data interface** — strategy code never talks to a provider directly.
- **Data provenance + quality status is a first-class architectural requirement**, not an afterthought: every candle carries source, timestamps, and a quality flag; low-quality inputs block confident signals.

## 5. Operating configuration — APPROVED (versionable)

EUR/USD · Mon–Thu · **03:00–04:00 America/Denver** · 15m trend · 5m entry · paper only. All time math in `America/Denver` with DST handled; no hard-coded offsets. **Operating config is a versioned record, separate from the strategy definition**, so alternate windows (documented: 02:00–03:00, 06:00–07:00) can be compared later without changing any rule.

## 6. Market data — decisions + open verifications

- **Primary: OANDA v20 practice API, data-only.** Returns bid/ask/mid candles (`price=MBA`), S5–monthly granularities, ≤5000 candles/request, history advertised to 2005.
- **MetaTrader 5 retained as Marcelo's manual reference** (not the ingestion source — it needs a running Windows terminal).
- **No real-money execution.**
- **Indicators on bid candles; outcomes modeled with bid/ask** (long in=ask/out=bid; short in=bid/out=ask) + spread + slippage. OANDA pricing is **not assumed** equal to MT5/TradingView.
- **Open verifications (need the practice token):** (A) actual S5 depth + pagination cost; (B) historical-candle vs live-stream price difference. Carried as explicit tasks, not assumptions.

## 7. Strategy — NOT approved

Under investigation as pre-registered candidate variants (EMA set, timeframe alignment, three transition conditions, three pullback definitions, 1-vs-2 confirmation, three stop anchors, experimental filters). The 100 EMA is a legitimate candidate (Marcelo referenced it) but **not mandatory without evidence**; no universal max-stop is imposed. See `STRATEGY_DEFINITION.md`.

## 8. Trading journal — status & integrity

- Calibration source material, **not** performance data; originals preserved verbatim.
- Enumerated 2026-11-19: **22 dated entries, 02/09/2025 → 03/25/2026.** Two Feb-2025 entries handled as a separate pre-period bucket (excluded from the 2026 reconciliation set and from tuning). The 02/16–03/25/2026 entries are the reconciliation set.
- **April 2026 entries were not found** in any tab — flagged as an open item, not invented or silently dropped.
- Performance columns (Enter/Close/Net) are **unverified**; confirmed errors (e.g., 02/18 close 1.8403; 03/03 sign conflict) are flagged, not overwritten. Outcomes reconstructed only where source prices+direction support it, labeled *inferred*.
- Reconciliation reports **agree / disagree / cannot-determine-intent**, with Marcelo's original reasoning shown.

## 9. Scope — Milestone 1 only

In: foundation; ingestion + quality; deterministic indicators; strategy evaluation across candidates; decision log; journal reconciliation. Out (deferred): paper execution, **position sizing (Milestone 2: computed from stop distance + chosen dollar risk, to mechanically remove the lot/pip/risk confusion the journal shows)**, dashboard, alerts, AI assistant.

## 10. Security & safety

Server-side authz + RLS; provider token encrypted at rest, never in browser/logs; scheduled jobs secret-authenticated; idempotency keys; **paper-only is a missing capability, not a disabled one.**

## 11. Open decisions

Tracked in `STRATEGY_DEFINITION.md` §6 and `MILESTONE_1_PLAN.md` §Acceptance. Nothing in the strategy is official until it survives out-of-sample testing and Marcelo approves it.

---

*Maintenance: propose edits as section + current text + proposed text + reason; Marcelo approves. Draft status preserved until explicitly promoted.*
