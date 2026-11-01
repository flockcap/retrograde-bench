# RETROGRADE · Round 0 (preliminary) results

14 models · 24 episodes each (8 workloads × 3 protection tiers × 1 seed) · first-party APIs only

Round commitment `9251359827da55cab55c4c66bed9fff0d6be8c1b331a55be87c155eb88d4c9dd` · revealed key `f7e8cded39fc8895ab6bc2746c508d89ba58f983c08f2eaf19c28efca59f8ffc`

**This is a preliminary preview, not a ranking.** Models are grouped into score bands; within a band the 95% bootstrap intervals overlap and the order is not meaningful. The full pre-registered round (5 seeds × 2 samples) is pending.

Served-host check: 5,832 model calls, all served by the model maker's own API.

## Capability under protection (pooled over models)

| tier | composite | 95% CI | episodes |
|---|---|---|---|
| bare | 0.86 | [0.80, 0.91] | 112 |
| obfuscated | 0.78 | [0.70, 0.84] | 112 |
| exotic ISA | 0.83 | [0.76, 0.90] | 112 |

## Score bands

| band | model | composite | 95% CI | bare | obf | exotic |
|---|---|---|---|---|---|---|
| 1 | `gpt-6-astra` | 0.88 | [0.85, 0.92] | 0.91 | 0.86 | 0.88 |
|  | `gpt-6.1-sol` | 0.88 | [0.85, 0.91] | 0.90 | 0.87 | 0.86 |
|  | `qwen3.8-max-prime` | 0.87 | [0.84, 0.91] | 0.89 | 0.85 | 0.88 |
|  | `grok-4.7` | 0.87 | [0.84, 0.91] | 0.88 | 0.86 | 0.88 |
|  | `gemini-4-argon` | 0.86 | [0.77, 0.92] | 0.90 | 0.89 | 0.80 |
|  | `claude-sonnet-5.5` | 0.85 | [0.82, 0.88] | 0.87 | 0.80 | 0.88 |
|  | `claude-opus-5.5` | 0.84 | [0.76, 0.91] | 0.89 | 0.83 | 0.81 |
|  | `muse-spark-1.3` | 0.83 | [0.77, 0.88] | 0.89 | 0.77 | 0.83 |
| 2 | `claude-haiku-5.5` | 0.78 | [0.73, 0.83] | 0.80 | 0.74 | 0.82 |
|  | `mistral-large-4-0` | 0.78 | [0.69, 0.86] | 0.79 | 0.69 | 0.86 |
|  | `glm-5.3` | 0.78 | [0.69, 0.85] | 0.83 | 0.68 | 0.83 |
|  | `kimi-k3` | 0.78 | [0.71, 0.84] | 0.80 | 0.72 | 0.81 |
|  | `gemini-3.8-flash` | 0.77 | [0.68, 0.84] | 0.86 | 0.72 | 0.73 |
|  | `deepseek-v4-pro-0813` | 0.71 | [0.59, 0.82] | 0.83 | 0.57 | 0.74 |

Composite is calibration-scored (a confidently wrong claim scores below 0). Per-tier columns have n = 8 per model and are indicative only.
