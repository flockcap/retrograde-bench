<p align="center"><sub><code>benchmark · reverse engineering · LLM agents · Flock Capital Research</code></sub></p>

<h1 align="center">RETROGRADE</h1>

<p align="center"><em>A procedurally generated, behaviorally graded,<br/>provenance-aware benchmark for reverse-engineering agents.</em></p>

<p align="center"><img src="assets/frontis-cfg.png" alt="A synthesized call graph rendered by error diffusion" width="100%"/></p>
<p align="center"><sub><code>control-flow · seed 0xC0DE · floyd–steinberg error diffusion</code></sub></p>

<p align="center"><b><a href="paper/RETROGRADE.pdf">Read the paper (PDF, methods)</a></b></p>

---

> **Status: Preliminary results · Round 0 is running.** Round 0 is a reduced preview of the
> full design: 13 frontier models, 24 episodes each (8 workloads × 3 protection tiers, one seed
> each), every model served by its maker's own API. Its seed set is fixed in advance by a
> private round key, committed here before the first episode:
>
> ```text
> round0-preview  sha256("retrograde-round-commit/" + key) = 9251359827da55cab55c4c66bed9fff0d6be8c1b331a55be87c155eb88d4c9dd
> ```
>
> The results and full transcripts will be published here when it completes, and the key will be
> revealed then so anyone can check the draw. The full pre-registered round (5 seeds × 2 samples
> per cell) follows.
>
> **The paper** ([`paper/RETROGRADE.pdf`](paper/RETROGRADE.pdf)) is a methods paper. It describes
> the generator, the grading, the evaluation protocol and the reporting plan. It contains no model
> results: those are published separately, here.

## Abstract

Reverse-engineering benchmarks for LLM agents are usually **static, hand-built and
contaminating**. Once a fixed corpus of targets is published, it leaks into leaderboards and training
data. These benchmarks are scored by flag-match or text similarity, which rewards pattern-matching over understanding.
RETROGRADE replaces the corpus with a **generator**. Every target is synthesized from a
compositional behavior grammar. It is unique, it never existed as source text, and it ships with
machine-readable ground truth emitted *by construction*. A claim about a binary is graded by
**executing** it: differential testing against a reference oracle, output prediction on
adversarial inputs, and causal interventions verified in an emulator. It is never graded by string similarity.
Every claim carries a confidence and a provenance pointer, scored with a proper scoring rule
and asymmetric loss. That way a confident wrong answer about a safety-relevant fact costs more than an
honest "I don't know". Because targets are generated, the benchmark renews itself: every round
draws fresh, unseen seeds.

## §1 What an agent faces

The agent gets a stripped binary and nothing else. In the default *blind* setting, it isn't told the
workload, the seed, the language or, for re-hosted targets, the instruction set. It works in a
network-isolated sandbox with real tools (disassemblers, a debugger, emulators, Python) under a fixed budget
of tool-calling rounds. It then submits structured claims, each with a confidence and an address that justifies it.

| | |
|---|---|
| **Workloads** | 15 logic archetypes: wire protocols with CRCs and state machines, a randomized bytecode VM, obfuscated arithmetic transforms, container formats, a license-key validator, fixed-point sensor and DSP signal paths, save-file and config parsers, a template renderer, an HDLC/X.25 link layer, an automotive ECU (CAN + UDS), a BLDC motor controller, an expression interpreter, and a **benign** malware-behavior surrogate |
| **Targets** | native aarch64 Linux ELFs compiled from one IR to **C, C++, Rust or Go**; any workload can also be re-hosted on **four bare-metal ISAs** (Cortex-M, RISC-V RV32, PowerPC e500, MSP430) or the **Hexagon** DSP |
| **Protections** | a composable lattice of 10 passes: mixed boolean-arithmetic, opaque predicates, optimizer-robust constant hiding, control-flow flattening, string encryption, anti-debug, virtualization, packing, off-ABI calling conventions and an optimization sweep. All of them preserve behavior, and that is checked on every instance |
| **Tasks** | structure recovery · output prediction · counterfactual intervention · trigger discovery · behavior reconstruction · provenance |

## §2 Five design commitments

1. **Contamination-proof by construction, not by secrecy of a corpus.** There is no corpus to
   memorize. Each round draws fresh seeds from a private key, and the published commitment pins the
   draw in advance.
2. **Behavioral grading, never text match.** Reconstructions are differential-tested against the
   oracle. Predictions are checked by running the target. Trigger claims count only if the guarded
   block actually executes on the agent's input.
3. **Calibration and provenance.** A Brier-style proper scoring rule with asymmetric loss, plus a
   provenance discount: correct answers with bogus justification earn a fraction of the credit.
4. **A debugger and emulator in the loop**, and one fixed program measured under each protection, so
   the capability drop per protection is a clean ablation.
5. **Self-renewing.** Fresh rounds, item difficulty estimated by IRT, retirement of saturated items, and a canary in every
   target binary to detect leakage into training corpora.

## §3 Design results

These come from the generator and the grader alone, not from model runs.

<p align="center"><img src="assets/fig-scale.png" alt="Distinct programs per archetype" width="88%"/></p>
<p align="center"><sub><b>Figure 1.</b> Generating 150 fresh seeds per archetype gives 150/150 distinct programs
(145/150 for <code>transform</code>, whose constant space is smaller; the collisions match the expected birthday rate). A
conservative lower bound on the <code>protocol</code> space alone is 5.9 × 10<sup>32</sup>.</sub></p>

<p align="center"><img src="assets/fig-protection.png" alt="Protection lattice on one fixed program" width="88%"/></p>
<p align="center"><sub><b>Figure 2.</b> One program, protection varied. The instruction count rises at every rung,
from 952 to 2,775 under the full stack. A disassembly-first agent keeps recovering the magic constant until
optimizer-robust constant hiding removes that lexical anchor.</sub></p>

<p align="center"><img src="assets/fig-calibration.png" alt="Calibration and provenance scoring" width="88%"/></p>
<p align="center"><sub><b>Figure 3.</b> Two guessers with the same accuracy (0.062) score −0.76 when overconfident
and −0.05 when calibrated, so bluffing doesn't pay. Correct answers with forged provenance earn 0.043,
versus 0.108 with honest provenance.</sub></p>

<p align="center"><img src="assets/fig-behavioral.png" alt="Behavioral vs textual grading" width="60%"/></p>
<p align="center"><sub><b>Figure 4.</b> A reconstruction that is textually near-identical to the true source
(similarity 0.9999) but has one flipped comparator scores 0.048 under differential testing. A
text-similarity grader would overscore it by about 0.95.</sub></p>

## §4 Integrity of the evaluation

- **Ground truth never enters the sandbox.** At grade time the grader regenerates every answer from the seed, so
  editing agent-reachable files has no effect.
- **A reward-hacking audit is a CI gate.** Adversarial submissions must earn nothing: forged provenance, output-spoofing
  triggers, overfit reconstructions, tampered ground truth, malformed or non-finite confidences,
  duplicate and unrequested claims.
- **Infrastructure failures are never scored.** A run interrupted by an API error, a context-window
  truncation or a missing key is recorded as an error and re-run, never as a model's result. The
  pilot taught us this: two such harness artifacts were found and quarantined before the official run.
- **One frozen configuration for every model**: identical prompts, tools, step and tool-call budgets,
  wall-clock limit, reasoning effort and service tier. Provider routing excludes heavily quantized hosts,
  and the host that actually served each call is logged.
- **Pre-registered statistics.** Several seeds per archetype and independent samples per instance. 95% bootstrap
  intervals are computed over instances. A model that completes fewer than 95% of its cells is listed but not ranked.
- **Provenance on every number.** Each cell records the harness revision, the sandbox image digest, the configuration
  hash, the round commitment, the provider, its cost and the full transcript.

## §5 Safety

The malware tier is a behavioral *simulacrum*. It has the control-flow shape of an arming
sequence and a dormant→armed state machine, but no payload and no network access. "Activation" prints
a token and nothing else happens. Vulnerability tasks ask only for localization, never exploit synthesis. All
target execution is network-isolated, capability-dropped and resource-bounded. The generator is
not published.

## Citation

```bibtex
@misc{retrograde2026,
  title  = {{RETROGRADE}: A Procedurally Generated, Behaviorally Graded,
            Provenance-Aware Benchmark for Reverse-Engineering Agents},
  author = {vmfunc and Lamb Wakefield and {Laura K. (Alula)}},
  note   = {Flock Capital. Methods paper},
  year   = {2026},
  url    = {https://github.com/flockcap/retrograde-bench}
}
```

<sub>Content in this repository (text, figures and, once published, results) is licensed under
[CC BY 4.0](LICENSE). The benchmark generator, harness and grader are not open source.</sub>

<p align="center"><img src="assets/end-trace.png" alt="A trace decoded from an instruction bitstream" width="100%"/></p>
<p align="center"><sub><code>trace · seed 0x7ACE · the ridge is decoded from the bitstream below</code></sub></p>
