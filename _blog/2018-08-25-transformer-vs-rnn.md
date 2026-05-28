---
title: "A Comparison of Transformer and Recurrent Neural Networks on Multilingual Neural Machine Translation"
date: 2018-08-25
last_modified_at: 2018-08-25
emoji: "📄"
excerpt: "A comparative analysis of Transformer vs. recurrent architectures across bilingual, multilingual, and zero-shot translation — using professional post-edits and error categories, not just BLEU."
tags: [NMT, Transformer, RNN, Multilingual]
---

> **TL;DR** — Multilingual NMT works, but *what* is it actually good and bad at? We compare Transformer and recurrent (RNN) architectures across bilingual, multilingual, and zero-shot settings — grounding the analysis in professional post-edits and human error categories, not BLEU alone.

## The Problem

By 2018, multilingual NMT was clearly promising — one model handling many directions, strong in low-resource settings, and even capable of *zero-shot* translation between pairs never seen in training. But the field lacked a careful answer to a basic question: **what is a multilingual model actually capable of, and where does it break?** Aggregate BLEU scores hide *which kinds* of errors each system makes.

## Approach

We ran a controlled, comparative study with three axes:

1. **System type** — bilingual vs. multilingual vs. zero-shot.
2. **Architecture** — recurrent (RNN) vs. Transformer, the two dominant designs at the time.
3. **Language closeness** — how the relatedness of source and target languages affects zero-shot quality.

Crucially, the analysis goes beyond automatic metrics. We use **multiple professional post-edits** of the outputs and break errors into human-interpretable categories — **lexical, morphology, and word order** — alongside BLEU and TER.

## Key Findings

- The **Transformer** consistently produced higher-quality translations than the recurrent architecture, and the gap was most visible in the **zero-shot** directions.
- **Language closeness matters** for zero-shot: the more related the source and target languages, the better the zero-shot output.
- The **error-category lens** (lexical / morphology / word order) revealed differences that BLEU alone masked — i.e., two systems with similar BLEU can fail in qualitatively different ways.

For the full breakdown of BLEU/TER and per-category error counts, see the paper.

## Why It Matters

This was one of the early in-depth audits of *what multilingual NMT learns* rather than just *how high it scores*. The methodology — pairing automatic metrics with professional post-edits and error typologies — is a template for evaluating any multilingual or zero-shot system, and the architecture/closeness findings informed later multilingual MT design.

## Details & Resources

- **Paper:** [COLING 2018 (ACL Anthology)](https://aclanthology.org/C18-1054/) · [arXiv](https://arxiv.org/abs/1806.06957)
- **Citation:** S. M. Lakew, M. Cettolo, M. Federico. *Proceedings of the 27th International Conference on Computational Linguistics (COLING)*, Santa Fe, New Mexico, 2018.
