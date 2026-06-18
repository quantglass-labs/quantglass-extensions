# Indicator Contract — reference

Indicators are **deterministic** feature calculations used by signals, backtests,
rankings, and explanations — the same candles always produce the same numbers. This
is the contract reference; for a step-by-step build, see the
**[Indicator guide](guides/indicator.md)**.

## `IndicatorDefinition`

| Field | Type | Notes |
| --- | --- | --- |
| `id`, `name`, `description` | `str` | |
| `category` | `str` | e.g. `"liquidity"`, `"trend"`, `"volatility"` |
| `inputs` | `tuple[str, ...]` | candle fields read (e.g. `("close", "volume")`) |
| `outputs` | `tuple[str, ...]` | named series produced |
| `maturity` | `"computed" \| "catalog"` | see below |
| `families` | `tuple[str, ...]` | grouping tags |
| `source` | `"built-in" \| "extension"` | use `"extension"` |
| `extension_id` | `str \| None` | your manifest id |

### Maturity levels

- **`computed`** — the extension also wires a deterministic calculation
  (`IndicatorPlugin.compute`) into an executable path; eligible for signal/backtest
  use.
- **`catalog`** — listed as a documented contribution target, metadata only, **not**
  computed by the engine. Use this for metadata-only packs; never imply a `catalog`
  entry affects live signals.

## `IndicatorPlugin` (executable contract)

```python
class IndicatorPlugin(Protocol):
    def compute(
        self,
        candles: list[dict[str, Any]],
        context: dict[str, Any],
    ) -> dict[str, list[float | None]]: ...
```

Each returned series is keyed by an `outputs` name and **aligned to the candles**
(same length), with `None` across the warm-up region.

## Rules

- **No network calls, no hidden state, no future-candle access.**
- Return **one value per candle**; use `None` where warm-up makes a value undefined
  rather than back-filling.
- Outputs are a pure function of `candles` (and `context` parameters) — no clocks,
  no randomness.
- Declare exactly the `inputs` you read and the `outputs` you emit.
- Add known-answer tests.

## Test expectations

Cover: warm-up behavior · constant series · trending series · edge cases (empty
input, zero division) · determinism (same input → identical output).

## Documentation

- If an indicator appears in the UI or confidence basis, update
  `docs/technical/04-signal-engine.md` and `docs/user-guide/06-signals.md` in the
  main repo.
- If added only as a `catalog` entry, update the category/family description and
  avoid implying it affects live signals.

> Educational and research tooling. Nothing here is financial advice.
