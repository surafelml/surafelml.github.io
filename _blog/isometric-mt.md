---
title: "Isometric MT: Neural Machine Translation for Automatic Dubbing"
date: 2022-05-22
last_modified_at: 2022-05-22
emoji: "🌐"
excerpt: "Teaching a transformer to translate within ±10% of the source length directly — no N-best generation, no re-ranking — for length-matched automatic dubbing."
tags: [NMT, Automatic Dubbing, Length Control, Transformer]
---

> **TL;DR** — Automatic dubbing needs translations that match the source *length* so speech stays in sync. Prior methods over-generate then re-rank. **Isometric MT** teaches a single transformer to produce length-matched output directly — simpler, and better than the more complex alternatives.

## The Problem

In automatic dubbing, the translated speech has to fit the time the original speaker was talking. That makes **length** a first-class constraint: the translation should land within roughly **±10% of the source character count**, *without* sacrificing quality. The two pull against each other — squeezing output to a target length usually degrades translation. The common fix is a two-step pipeline: generate an **N-best list** of hypotheses, then **re-rank** them by a length-and-quality function. It works, but it's heavy: multiple decodes plus an auxiliary ranker.

## Approach

We replace that pipeline with a **self-learning** approach: the transformer learns to generate length-compliant translations **directly**, in a single pass. No N-best list, no separate ranking function — the length-matching behavior is baked into the model itself.

<figure class="post-figure post-figure--full">
  <img src="/assets/images/isometric-mt-approach.png" alt="Conventional N-best generation and re-ranking versus the Isometric MT self-learning approach">
  <figcaption>Left: the conventional generate-N-best-then-re-rank pipeline. Right: Isometric MT learns to produce length-matched output directly, in one decode.</figcaption>
</figure>

## Key Results

- Evaluated on **four language pairs** — English → French, Italian, German, Spanish — on a publicly available benchmark.
- **Both automatic and manual evaluation** show Isometric MT **outperforms the more complex N-best + re-ranking approaches** from the literature — while being a single model with a single decoding pass.

## Why It Matters

Isometric MT makes length control a property of the model rather than a bolt-on pipeline, which is cheaper to run and easier to deploy in a real dubbing system. It's a cornerstone of the broader automatic-dubbing line of work — from [verbosity control](https://arxiv.org/abs/2103.10532) and [isochrony-aware translation](https://arxiv.org/abs/2112.08548) to [jointly optimizing translation and speech timing](https://arxiv.org/abs/2302.12979) — and it underpinned the [Isometric Spoken Language Translation shared task at IWSLT 2022](https://github.com/amazon-science/isometric-slt).

## Details & Resources

- **Paper:** [ICASSP 2022 — arXiv](https://arxiv.org/abs/2112.08682)
- **Code:** [github.com/amazon-science/isometric-slt](https://github.com/amazon-science/isometric-slt)
- **Citation:** S. M. Lakew, Y. Virkar, P. Mathur, M. Federico. *Isometric MT: Neural Machine Translation for Automatic Dubbing.* ICASSP 2022.
