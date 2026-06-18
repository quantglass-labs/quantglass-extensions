# Strategy & Signal Contributions — reference

Strategies contribute **setups** that emit signal **candidates**; the engine earns
the confidence through its own honest backtest, conformal, and calibration
machinery — a strategy never asserts its own confidence. This is the contract
reference; for a step-by-step build, see the
**[Strategy & signal plugin guide](guides/strategy-plugin.md)**.

## `StrategyDefinition`

Declares the setup so it appears in the catalog and can be filtered.

| Field | Type | Notes |
| --- | --- | --- |
| `id` | `str` | Unique. |
| `name`, `description` | `str` | Shown in the catalog. |
| `setup_types` | `tuple[str, ...]` | The setup ids this strategy can emit. |
| `direction` | `"long" \| "short" \| "both"` | |
| `market_types` | `tuple[str, ...]` | default `("crypto", "stocks")` |
| `timeframes` | `tuple[str, ...]` | default `("15m", "1h", "4h", "1d")` |
| `source` | `"built-in" \| "extension"` | use `"extension"` |
| `extension_id` | `str \| None` | your manifest id |
| `candidate_factory` | `Callable[[dict], list[dict]] \| None` | presence flips `executable` to `true` |

## `StrategyPlugin` (executable contract)

```python
class StrategyPlugin(Protocol):
    def evaluate(
        self,
        candles: list[dict[str, Any]],
        features: dict[str, list[float | None]],
        context: dict[str, Any],
    ) -> list[StrategyCandidate]: ...
```

## `StrategyCandidate`

```python
@dataclass
class StrategyCandidate:
    setup_type: str
    signal_type: Literal["BUY_ZONE", "SELL", "HOLD", "WAIT", "WATCH"]
    direction: Literal["long", "short"]
    confidence_hint: float          # a HINT; the engine decides what is shown
    reasons: tuple[str, ...]        # plain-language facts, numbers verbatim
    metadata: dict[str, Any] = {}
```

A related `BacktestModelPlugin.simulate(candles, entries, costs, context) -> dict`
lets an extension contribute a backtest model.

## Requirements

- Use **closed candles** by default; avoid lookahead/future-candle access.
- Include fees and slippage assumptions; backtest the exact entry/stop/exit shown.
- Report sample size and out-of-sample results separately; label low-sample setups
  unvalidated.
- Keep `confidence_hint` calibrated to evidence, not optimism.
- `reasons` state facts only — never advice or a price prediction; keep numbers and
  indicator names verbatim.
- Add deterministic tests with known outcomes.

## Good first strategy areas

Breakout-retest · range mean-reversion · higher-timeframe trend filter · ETF/crypto
relative-strength rotation · volatility-regime filters.

## Acceptance checklist

- [ ] Deterministic tests pass; the backtest describes the displayed trade.
- [ ] No lookahead; fees/slippage included.
- [ ] Low-sample strategies labeled unvalidated; UI copy avoids financial advice.

## Core-engine files (for in-tree contributions)

- `apps/backend/app/services/signal_engine/`
- `apps/backend/tests/test_backtest.py`
- `apps/backend/tests/test_calibration_corridor.py`

> Educational and research tooling. Nothing here is financial advice.
