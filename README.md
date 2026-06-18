# QuantGlass Extensions

Community extensions and templates for **QuantGlass**, built against the
standalone [`quantglass-sdk`](https://github.com/quantglass-labs/quantglass-sdk).
Everything here imports from `quantglass_sdk` only — no dependency on the host
application — so you can author and unit-test an extension in isolation.

> Educational and research tooling. Nothing here is financial advice.

## Layout

| Path | What it is |
| ---- | ---------- |
| `examples/providers/` | A market-data / trading provider adapter template. |
| `examples/strategies/` | A deterministic strategy plugin template. |
| `examples/extensions/` | A full extension manifest wiring several surfaces. |
| `packs/` | Declarative community content: lesson packs, mission packs, and a momentum strategy pack. |
| `docs/` | Authoring guides: extension types, provider adapters, strategy plugins, the indicator contract. |

## Quick start

```bash
python -m venv .venv
./.venv/bin/python -m pip install -r requirements.txt
# author against quantglass_sdk:
python -c "from quantglass_sdk import ExtensionManifest, StrategyDefinition; print('SDK ready')"
```

**New here? Start with the illustrated walkthrough:
[docs/guides/getting-started.md](docs/guides/getting-started.md)** — empty folder
to an extension the app loads, in ~10 minutes. Then read
[docs/extensions.md](docs/extensions.md) for the full authoring model and the
capability/permission vocabulary, and copy the closest template under `examples/`
or `packs/`.

## How extensions load

The host app discovers extensions through its registry, hands each one an
`ExtensionContext`, and the extension calls `context.register_*(...)` with the
SDK's `Definition` objects. Content packs (lessons/missions) are declarative
JSON-shaped data and are validated whole at registration — they can never inject
markup or executable code into the UI.

## License

AGPL-3.0-or-later. See [LICENSE](LICENSE).
