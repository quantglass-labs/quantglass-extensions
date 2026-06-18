# Provider Adapters — reference

Provider adapters connect QuantGlass to market-data, news, broker/execution, or AI
services. This is the contract reference; for a step-by-step build, see the
**[Provider adapter guide](guides/provider-adapter.md)**.

## Registration contract

An extension registers a provider from its `register(context)` via the
`ProviderRegistrar` protocol the host satisfies:

```python
context.register_provider(
    name: str,
    capabilities: set[Capability],
    client: Any | None = None,
    transport: Literal["public", "keyed", "internal"] = "internal",
) -> None
```

The host's full `ProviderRegistrar.register` accepts additional optional metadata
(`label`, `base_url`, `auth_type`, `profile_configured`, `adapter_status`,
`source`, `notes`); `register_provider` on the context covers the common path.

### `Capability` vocabulary

`Literal["ohlcv", "order_book", "news", "trading", "ai"]` — declare exactly what the
adapter provides.

### `transport`

| transport | Meaning | Required permission |
| --- | --- | --- |
| `public` | Keyless public API | `network_access` |
| `keyed` | Needs a credential | `network_access` (+ a `secret` setting) |
| `internal` | In-process, no network | — |

In addition, any adapter declaring the `trading` capability requires the
`submit_orders` permission. Permission failures **skip** registration with a
`context.diagnostics` entry — they never raise.

## Candle shape

OHLCV adapters must return candles matching `REQUIRED_CANDLE_FIELDS`; validate with
the SDK's `validate_candles` (the same checker the host uses) before returning:

```python
from quantglass_sdk import validate_candles
problems = validate_candles(candles)   # [] = clean
```

Required per candle: `open_time_utc`, `open`, `high`, `low`, `close`, `volume`.
`validate_candles` also enforces UTC-parseable, **monotonic, de-duplicated** open
times and numeric OHLCV.

A typical adapter `client` exposes:

```python
def get_symbols(self, market_type: str) -> list[str]: ...
def get_ohlcv(
    self, symbol: str, timeframe: str,
    start: str | None = None, end: str | None = None,
) -> list[dict[str, object]]: ...
```

## Rules

- Keep capabilities explicit (`ohlcv`, `order_book`, `news`, `trading`, `ai`).
- Public providers must not require API keys; keyed providers read credentials from
  a `secret` setting, never a constant.
- Never log API keys, account IDs, or order-payload secrets.
- Normalize timestamps to UTC; exclude partial candles unless the user opts in.
- Document provider rate limits, pricing, and redistribution constraints.
- Add tests with small deterministic fixtures, including malformed payloads.

## Acceptance checklist

- [ ] Registered with the correct capability and transport.
- [ ] Declares only the permissions it uses (`network_access` for public/keyed;
      `submit_orders` only for `trading`).
- [ ] Unconfigured keyed providers report unconfigured rather than failing silently.
- [ ] Output passes `validate_candles`; invalid upstream responses raise clear errors.
- [ ] Rate limits respected; tests cover timestamp normalization and malformed payloads.

## Core-engine files (for in-tree contributions)

- Provider registry: `apps/backend/app/providers/manager.py`
- Extension registry: `apps/backend/app/extensions/registry.py`
- Public / keyed providers: `apps/backend/app/providers/{public,keyed}.py`
- Corridor ingest: `apps/backend/app/services/market_corridor.py`

> Educational and research tooling. Nothing here is financial advice.
