# QuantGlass extension guides

Step-by-step, illustrated tutorials for building QuantGlass extensions. Each builds
something runnable, tests it with **no host app and no network**, and shows where it
appears in the product. Start at the top.

| Guide | You build… |
| --- | --- |
| **[Getting Started](getting-started.md)** | Your first extension — empty folder to an app-loaded extension in ~10 minutes. Start here. |
| [Provider adapters](provider-adapter.md) | A market-data / news / broker / AI adapter, with the capability · transport · permission model. |
| [Strategy & signal plugins](strategy-plugin.md) | A setup that emits signal candidates the engine validates with its own honest backtest. |
| [Indicators](indicator.md) | A deterministic, fixture-testable indicator series. |
| [Content packs & localization](content-packs-localization.md) | Lesson and mission packs — and the honest state of pack localization. |
| [Packaging, permissions & trust](packaging-and-trust.md) | Declaring capabilities/permissions, packaging for discovery, and the trust model. |

**Looking for the contract details** rather than a walkthrough? See the reference
docs one level up: [extensions](../extensions.md), [extension types](../extension-types.md),
[provider adapters](../provider-adapters.md), [strategy plugins](../strategy-plugins.md),
[indicator contract](../indicator-contract.md) — and the
[`quantglass-sdk`](https://github.com/quantglass-labs/quantglass-sdk) symbol surface.

> Educational and research tooling. Nothing here is financial advice.
