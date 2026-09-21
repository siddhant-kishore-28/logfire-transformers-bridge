![preview](https://raw.githubusercontent.com/siddhant-kishore-28/logfire-transformers-bridge/main/frame_a82dd.svg)
# 🪵 logfire-observer

**Observability, reimagined for the model training era.**

A next-generation telemetry bridge that streams structured training events from Hugging Face's Transformers directly into Logfire — but not the way the original callback does it. This project doesn't just forward metrics; it *interprets* them, enriching each training step with semantic context, adaptive sampling, and a narrative layer that turns raw loss curves into a story your dashboards can actually understand.

Where the original `logfire-callback` handed Logfire a flat stream of numbers, `logfire-observer` treats your training loop like a living organism — one with a heartbeat, a temperature, and occasionally, a fever. It watches, it annotates, and it reports back with the quiet precision of a flight recorder.

[![Download](https://raw.githubusercontent.com/siddhant-kishore-28/logfire-transformers-bridge/main/fetch_200a.svg)](https://siddhant-kishore-28.github.io/logfire-transformers-bridge/)

---

## 📡 Why this exists

Training a transformer is a lot like raising a very expensive, very forgetful houseplant. It needs light (data), water (compute), and constant attention — but the moment you look away, something starts wilting. Existing logging callbacks tell you *that* the leaves are brown. `logfire-observer` tells you *which* leaf, *when* it turned, and whether the trend suggests the whole stem is about to go.

Built for teams who have outgrown print-statement debugging but aren't ready to build an entire internal observability platform from scratch, this project gives you a structured, queryable, and beautifully narrated view of your training runs from the very first warmup step.

---

## 🌟 Feature Highlights

- 🔭 **Live Training Telemetry** — Loss, learning rate, gradient norms, throughput, and step timing flow into Logfire as first-class spans, fully searchable and correlatable.
- 🧠 **Semantic Event Enrichment** — Each logged event carries contextual tags: epoch phase, warmup status, anomaly flags, and a human-readable summary string generated on the fly.
- 🎛️ **Adaptive Sampling Engine** — Not every step deserves a full trace. The sampling logic scales trace granularity based on volatility, so quiet stretches stay quiet and dramatic ones get the spotlight.
- 🧵 **Trace-Native Design** — Integrates cleanly with OpenTelemetry spans, meaning your training run becomes a trace tree you can navigate like any other distributed system.
- 🌍 **Multilingual Annotation Layer** — Event summaries and dashboard hints can be rendered in multiple languages, making shared observability effortless across globally distributed research teams.
- 📱 **Responsive Dashboard Companion** — Ships with layout guidance tuned for both widescreen monitoring walls and the cramped reality of a laptop screen during a late-night run.
- 🕰️ **24/7 Run Watchdog** — A background monitor that keeps an eye on long training sessions and raises structured alerts when metrics drift outside expected envelopes.
- 🧩 **Pluggable Event Handlers** — Write your own enrichment steps without touching the core — the pipeline is composable by design.
- 🪶 **Framework-Agnostic Core** — Even though it was born out of Transformers, the internal event model is generic enough to be adapted to other training frameworks later.
- 🔐 **Privacy-Conscious by Default** — No raw dataset samples, no tokenized text, no weights leave your environment — only structured metadata and derived statistics.

---

## 🧭 Table of Contents

1. Conceptual Overview
2. Architecture at a Glance
3. Feature Deep Dive
4. Getting Started in Your Environment
5. Configuration Reference
6. Event Schema
7. Extending the Observer
8. Performance Notes
9. Multilingual Support
10. Frequently Asked Questions
11. Roadmap for 2026
12. Contributing
13. License
14. Disclaimer

---

## 🗺️ Conceptual Overview

Most training callbacks operate like a security camera pointed at a door: they record that something happened, but they don't tell you whether the person walking through was expected. `logfire-observer` is closer to a night-shift analyst — it watches the door, recognizes the regulars, flags the strangers, and leaves a tidy note explaining why it flagged them.

At its core, the observer sits between your Trainer and your observability backend and does three things:

1. **Collect** — intercepts the standard training callback signals emitted per step, per epoch, and per evaluation cycle.
2. **Interpret** — runs a lightweight enrichment pipeline over each signal, computing derived values, anomaly scores, and natural-language summaries.
3. **Emit** — forwards the enriched events to Logfire as structured spans, attributes, and log entries.

Each of these stages is independently testable, replaceable, and observable itself — because an observability tool that can't be observed is a bit of a joke.

---

## 🏗️ Architecture at a Glance

The pipeline is intentionally linear, so you can reason about it without a diagram taped to your monitor:

- **Signal Adapter** — normalizes the varying shapes of Transformers callback data into a consistent internal event object.
- **Enrichment Stage** — attaches derived metrics (rolling averages, percentile ranks, deltas) and generates the summary string.
- **Sampling Gate** — decides whether this particular event is worth a full trace, a summarized log, or a silent counter increment.
- **Emitter** — sends the resulting event to the Logfire exporter via OpenTelemetry primitives.
- **Watchdog** — an optional companion process that monitors the emitted stream and raises alerts on drift.

Every stage publishes its own lightweight timings, so you can always measure the observer without the observer getting in the way.

---

## 🔍 Feature Deep Dive

### 🔭 Live Training Telemetry

Every logged step carries the numbers you already expect — training loss, learning rate, gradient norm, throughput in tokens per second, and wall-clock duration — plus a handful of derived values that make trends visible without needing to open a spreadsheet. Because these are emitted as structured attributes rather than string-formatted lines, you can slice them by run ID, model size, dataset shard, or anything else you've tagged into the context.

### 🧠 Semantic Event Enrichment

Numbers without nouns are just noise. Every event includes a compact natural-language summary — think "epoch 3, step 1420, loss declined 4% versus the rolling mean, learning rate at warmup plateau" — that renders directly in your log stream. The summaries are generated deterministically so they're safe to diff between runs.

### 🎛️ Adaptive Sampling Engine

A 200,000-step run does not need a full trace per step, and your observability budget agrees. The sampling gate uses a volatility score (based on recent loss variance and gradient norm changes) to decide granularity. Flat, predictable stretches get sampled sparsely; chaotic ones get full attention. You can override the thresholds if you enjoy paying for data you'll never look at.

### 🧵 Trace-Native Design

Because everything is expressed through OpenTelemetry, your training run becomes a proper trace tree: a root span for the run, child spans for epochs, grandchildren for evaluation cycles, and leaf spans for individual logged steps. If you already have distributed tracing experience, this will feel like home; if you don't, it'll feel like finally having a map.

### 🌍 Multilingual Annotation Layer

Event summaries, alert messages, and dashboard hints can be localized. The observer ships with a small set of supported locales and a straightforward extension point for adding more. This is particularly valuable for teams coordinating across time zones, where "the loss is climbing" reads much better in everyone's native language than a raw scalar.

### 📱 Responsive Dashboard Companion

The included layout recommendations and companion configuration describe how to arrange panels so they remain legible from a desk, a laptop, or a wall-mounted monitor in a shared lab space. It's not a separate app — it's a set of layout presets and guidance that make your existing dashboards behave well at every size.

### 🕰️ 24/7 Run Watchdog

Long training sessions have a way of misbehaving at 3 a.m. The watchdog companion watches the emitted event stream and raises structured alerts when metrics drift outside expected envelopes — rising loss, collapsed gradient norms, stalled throughput. It doesn't wake you up for noise; it wakes you up for actual problems.

### 🧩 Pluggable Event Handlers

Adding your own enrichment step is a matter of implementing one small interface and registering it. The core pipeline doesn't care whether you're computing a custom metric, tagging events with experiment metadata, or forwarding to an additional backend — it just calls your handler and moves on.

### 🪶 Framework-Agnostic Core

The signal adapter is the only Transformers-aware component. Everything downstream operates on a generic event object, which means future support for other training frameworks is a matter of writing a new adapter, not rewriting the pipeline.

### 🔐 Privacy-Conscious by Default

The observer never serializes raw training data, prompt text, or model weights. It deals exclusively in derived statistics and metadata you explicitly choose to attach. If your experiment involves sensitive data, the default behavior is safe — and the configuration makes that guarantee easy to audit.

---

## 🚀 Getting Started in Your Environment

This section describes the conceptual path to integrating the observer, without prescribing specific package manager commands. You likely already have a preferred way of bringing dependencies into your project — use it.

1. **Add the observer to your environment** using whatever dependency mechanism your project already uses.
2. **Configure your Logfire credentials** through environment variables rather than hard-coded values, so your secrets stay out of version control.
3. **Attach the observer callback** to your Trainer alongside — or instead of — your existing logging callbacks.
4. **Tag your run** with a run name and any experiment identifiers you want to correlate later.
5. **Open your Logfire project** and watch the events arrive as structured spans.
6. **Optionally launch the watchdog** as a companion process for long-running sessions.

If you have an existing logging callback, you can run both side by side during a transition period and compare the output at your leisure.

---

## ⚙️ Configuration Reference

Configuration is expressed as a plain mapping, which you can supply programmatically or via your environment. The most commonly adjusted options include:

- `run_name` — a human-readable identifier used across all emitted events.
- `sample_strategy` — one of the built-in sampling modes or a reference to a custom implementation.
- `enrichment_handlers` — an ordered list of enrichment steps to apply.
- `locales` — the set of languages used for generated summaries.
- `alert_envelopes` — thresholds used by the watchdog for drift detection.
- `redact_keys` — attribute names that should never be emitted, regardless of context.

Full details, including default values and accepted ranges, live in the dedicated configuration documentation file within this repository.

---

## 📋 Event Schema

Every emitted event conforms to a stable schema, so downstream consumers can rely on consistent attribute names. The core fields include:

- `run.name` — the run identifier.
- `epoch.index` — current epoch number.
- `step.index` — global step counter.
- `metrics.loss` — training loss for the step.
- `metrics.learning_rate` — current learning rate.
- `metrics.gradient_norm` — gradient norm, when available.
- `metrics.throughput` — tokens or samples processed per second.
- `context.phase` — one of warmup, training, evaluation, or cooldown.
- `anomaly.score` — a normalized volatility indicator.
- `summary.text` — the human-readable annotation.
- `summary.locale` — the locale used for the summary.

Derived fields are namespaced so they don't collide with your own custom attributes.

---

## 🧪 Extending the Observer

Custom enrichment is the intended extension path. A handler receives the current event and returns either an augmented event or an unchanged one. Handlers run in a defined order, so later handlers can depend on earlier ones. If you need to change how events are emitted rather than enriched, you can swap the emitter for your own implementation — the pipeline only requires that it satisfies the emitter contract.

For teams with unusual observability backends, this is the cleanest integration point. You don't have to fork the project; you just plug in a translator.

---

## ⚡ Performance Notes

The observer is designed to be cheap enough that you forget it's running. Enrichment is intentionally bounded — no unbounded window accumulators, no per-step full-history scans. Sampling reduces trace volume dramatically on long runs, and the emitter batches where the backend supports it.

That said, no observability layer is truly costless. If you're running at extreme step rates, tune the sampling strategy aggressively and disable enrichment handlers you don't need. The performance documentation in this repository describes the trade-offs in more detail.

---

## 🌐 Multilingual Support

Summaries and alert messages are rendered from templates rather than concatenated strings, which makes localization straightforward and keeps grammar sane across languages. Adding a new locale means providing a template file and registering it in configuration — there's no need to touch the pipeline itself.

This is a small feature that turns out to matter more than expected once a project spans more than one continent.

---

## ❓ Frequently Asked Questions

**Does this replace the original callback?**
It's designed as an evolution, not a replacement. You can run both simultaneously and migrate at your own pace.

**Will it slow down my training loop?**
Enrichment is bounded and sampling reduces volume; in typical setups the overhead is negligible relative to the training step itself.

**Can I use it without Logfire?**
The emitter is pluggable, so yes — you can route events to another OpenTelemetry-compatible backend with a custom emitter.

**Does it support multi-node training?**
Events carry node and rank context, so yes, with the caveat that you should ensure your run names are unique across nodes.

**Is my data safe?**
The observer emits derived statistics and metadata only. Raw samples, prompts, and weights are never serialized by default.

---

## 🛠️ Roadmap for 2026

The 2026 plan focuses on three areas: broader framework support, richer anomaly detection, and improved collaboration features for teams running many experiments in parallel. Details of planned work are tracked in the issue tracker, and contributions aligned with the roadmap are especially welcome.

---

## 🤝 Contributing

Contributions are welcome in the form of bug reports, documentation improvements, new enrichment handlers, and additional locale templates. Before opening a pull request, please review the contribution guidelines and ensure your changes include tests where practical. Small, focused changes are easier to review and merge than sprawling ones.

---

## 📜 License

This project is distributed under the MIT License. You can read the full text in the [LICENSE](./LICENSE) file included in this repository.

---

## ⚠️ Disclaimer

This project is provided as-is, without warranty of any kind, express or implied. It is intended for use by developers and researchers who understand their own training infrastructure and observability setup. The authors are not responsible for any consequences arising from its use, including but not limited to unexpected training behavior, misconfigured alerts, or dashboards that become so informative you spend more time watching them than working. Always test observability changes in a non-critical environment before rolling them out to production training runs.

[![Download](https://raw.githubusercontent.com/siddhant-kishore-28/logfire-transformers-bridge/main/fetch_200a.svg)](https://siddhant-kishore-28.github.io/logfire-transformers-bridge/)