# mistev — calibrated pre-release risk guard for digital-asset withdrawals

Every exchange and custody platform faces the same pre-release bottleneck: a withdrawal request arrives across heterogeneous API schemas, and within milliseconds you must decide whether to **allow**, **escalate**, or **block** — before funds leave the hot wallet irrevocably.

Hard-coded rules miss subtle and slow-drain attacks; tabular GBDT models collapse to 100% false-positive rates the moment an institution renames or shifts schema fields; autoregressive LLMs are too slow and vulnerable to prompt injection when exposed directly to raw request fields.

**mistev answers one question before funds move: is this withdrawal safe to auto-release?**

🌐 **[Website](https://mistevhq.github.io/docs/)** · 📚 **[Documentation](https://github.com/MistevHQ/docs)** · ✉️ **[polivia@student.umgc.edu](mailto:polivia@student.umgc.edu)**

## How it works

Every request passes through four defense layers protected by code-enforced invariants that no model score can override:

| Layer | Role | Guarantee |
|---|---|---|
| **1. Strict Schema & Injection Firewall** | Type, range, chain-address, and identifier validation across canonical and 11 native exchange APIs | Rejects free text (`HTTP 400`) — eliminates prompt-injection entry points before inference |
| **2. Deterministic Admin Hard Policy** | Single/daily limits, address whitelist & cooldown windows, KYT blocked labels | Fail-closed deterministic veto, 100% auditable |
| **3. Calibrated System-1 Decision Model (`mistev`)** | Non-autoregressive encoder with calibrated decision head trained under strictly proper scoring rules | ~10–20 ms GPU / ~1.2 s lossless local CPU ONNX; generalizes zero-shot across unseen exchange schemas |
| **4. Gray-Zone Routing & System-2 Escalation** | Routes calibrated uncertainty bands and stealth behavioral triggers to System-2 LLM or persistent human audit queue | Fail-safe `escalate` on timeout or error; never degrades to auto-allow |

## Calibrated Verdicts

One three-way contract, consistent across all endpoints and exchange adapters:

**Allow** · **Escalate** · **Block**

- **Allow** — confident safe withdrawal (`p_block < p_low`, zero policy hits, zero behavioral triggers); auto-released in milliseconds.
- **Escalate** — gray-zone probability or stealth anomaly (`slow_drain` / `dormant_wakeup`); held and queued with structured numeric audit evidence for System-2 / operator review.
- **Block** — deterministic policy violation or high-confidence attack (`p_block >= p_high`).

## Research Highlights (`mistev-v3`)

- **Cross-Exchange Zero-Shot Generalization**: Evaluated across 9 out-of-distribution suites including 5 unseen native exchange schemas (Binance, Gate.io, Bybit, OKX, KuCoin) — cutting mean zero-shot FPR in half compared to v2 (`14.15% → 7.09%`, with 4/5 exchanges `< 5%` FPR vs `100%` FPR for tabular XGBoost).
- **11-Exchange Native Adapter Coverage**: Binance, Gate.io, Bybit, OKX, KuCoin, Bitget, Coinbase, Kraken, MEXC, HTX, and CoinEx — verified `11/11` allow, `11/11` attack block, and `11/11` prompt-injection rejection (`HTTP 400`).
- **Stealth OOD Catch Rate**: Layer-4 gray-zone routing lifts catch rate on unseen `slow_drain` and `dormant_wakeup` attacks from `75.0%` to **`99.8%`** while routing only `1.25%` of standard test traffic to review.
- **Anti-Forgetting Calibration**: Zero accuracy degradation on held-out non-crypto general decision benchmarks (`93.8% → 93.8%`).

## Public Repositories

| Repository | Visibility | Description |
|---|---|---|
| [`mistev-guard`](https://github.com/MistevHQ/mistev-guard) | Public | Zero-dependency Python reference SDK, Layer 1–2 validation & policy engine, and 11-exchange payload adapters |
| [`docs`](https://github.com/MistevHQ/docs) | Public | Public website, architecture notes, API specification, and benchmark reports |
| [`.github`](https://github.com/MistevHQ/.github) | Public | Organization overview and research summary |

*(Core training pipelines, datasets, and model weights are maintained privately.)*

---

<sub>Research inquiries & collaboration: <a href="mailto:polivia@student.umgc.edu">polivia@student.umgc.edu</a></sub>
