---
description: ColdBox engine support — BoxLang vs Adobe ColdFusion vs Lucee, feature differences, and runtime notes.
icon: server
---

# Engine Support & Differences

ColdBox runs on three engines. **BoxLang is the recommended runtime** — it's developed by the same team as ColdBox and gets every feature first.

| | BoxLang 1+ | Adobe CF 2023+ | Lucee 5+ |
| --- | --- | --- | --- |
| Status | ✅ First-class | ✅ Supported | ✅ Supported |
| Language | BoxLang (`.bx`/`.bxm`) + CFML | CFML | CFML |
| ColdBox pre-compiled edition (`bx-coldbox`) | ✅ | — | — |
| BoxLang Prime (native CacheBox/LogBox/WireBox) | ✅ | — | — |
| AI features (bx-ai, `toAi()`, `toMCP()`, `toAiGateway()`) | ✅ | — | — |
| Server-Sent Events (`event.sse()`, `toSSE()`) | ✅ | — | — |
| cbMCP live introspection | ✅ | — | — |
| Virtual thread executors | ✅ | — | — |
| Desktop applications | ✅ | — | — |
| Full null support (`enableNullSupport`) | ✅ | ⚠️ partial | ⚠️ partial |

🚀 Features marked BoxLang-only are tagged **BoxLang Exclusive** throughout these docs.

## BoxLang Notes

- Enable `enableNullSupport` in your `Application.bx` for full null handling — ColdBox 8.2+ runs cleanly with it on
- Prefer `bx-coldbox` (`box install bx-coldbox`) for the pre-compiled, engine-installed edition
- Use BoxLang classes and closures in application code: `() => {}` shorthand everywhere

## Adobe ColdFusion Notes

- Requires CF 2023 or newer; CF 2021 support was dropped in ColdBox 8
- Some Java engine quirks require defensive guards ColdBox already applies; keep the engine patched
- Async/parallel constructs are limited compared to BoxLang — ColdBox abstracts what it can

## Lucee Notes

- Lucee 5+ supported; Lucee 6/7 recommended
- Same async caveats as Adobe — the abstractions work, native constructs are fewer

## See Also

- [Deployment](deployment/README.md) — per-engine production setups
- [Scheduled Tasks](../digging-deeper/scheduled-tasks.md) — engine-aware scheduling notes
