---
title: "The Context We Can't Afford to Lose: Neural Networks from First Principles"
date: 2026-05-17
last_modified_at: 2026-05-17
published: true
emoji: "🧠"
excerpt: "The artificial neuron is from 1943. The learning rule, 1958. Backprop, 1986. A primer that builds the foundations of neural networks from first principles — and previews the lineage all the way to today's LLMs and agentic systems."
math: true
tags:
  - nn-timeline
  - Neural Networks
  - Deep Learning
  - First Principles
  - Backpropagation
  - Transformer
  - LLMs
  - History of AI
---

<div class="post-tldr"><strong>TL;DR</strong> — The artificial neuron is from 1943; backpropagation, 1986. Everything since — LSTM, Transformer, and the LLMs that followed — is the same unit cell applied to sequences and scaled. This primer builds the foundational math, then previews the arc to today's agentic systems.</div>

The math under today's largest models is decades old. McCulloch and Pitts wrote down the **artificial neuron** — a thresholded weighted sum — in **1943**. Rosenblatt added a learning rule in **1958**. The algorithm that trains deep networks, **backpropagation**, was popularized in **1986**. What changed since is not the ideas — it is the scale.

This primer covers the **Foundations** segment (1943–1991) from first principles — the math every later era builds on — and previews the arc from RNNs through attention to the Transformer, LLMs, and today's agentic systems. Each later era gets its own post; this is the map.


<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 820 268" role="img" aria-label="Neural network architecture timeline 1943–2026, two-row spiral layout" style="display:block;margin:0 auto 1.75rem;max-width:100%">
  <style>
    text{font-family:system-ui,sans-serif;fill:#1e293b}
    .era{font-size:12.5px;font-weight:600}
    .yr{font-size:10px;fill:#94a3b8}
    .ms{font-size:9px;fill:#64748b}
    g.seg{cursor:help}
    g.seg:hover rect{stroke-width:2.6}
    .loop{fill:none;stroke:#94a3b8;stroke-width:1.5;stroke-dasharray:5 3}
    .today{stroke:#dc2626;stroke-width:2;stroke-dasharray:4 3}
    .todaylbl{fill:#dc2626;font-size:11px;font-weight:700}
  </style>
  <defs>
    <pattern id="tF" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#d97706" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="tR" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#0891b2" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="tA" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#2563eb" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="tT" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#7c3aed" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="tL" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#0d9488" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="tG" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#059669" stroke-width="2" stroke-opacity=".07"/></pattern>
    <marker id="arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,1 L9,5 L0,9 z" fill="#94a3b8"/></marker>
  </defs>

  <!-- ROW 1 -->
  <text class="yr" x="152" y="33" text-anchor="middle">1943–1991</text>
  <g class="seg"><title>Foundations (1943–1991): MP neuron 1943 · Shannon 1948 · Perceptron 1958 · XOR limit 1969 · Backpropagation 1986 · Vanishing gradients 1991</title>
    <rect x="20" y="40" width="264" height="46" rx="4" fill="url(#tF)" stroke="#d97706" stroke-width="1.3"/>
    <text class="era" x="152" y="68" text-anchor="middle">Foundations</text>
  </g>
  <text class="ms" x="152" y="100" text-anchor="middle">MP neuron 1943 · Shannon 1948</text>
  <text class="ms" x="152" y="111" text-anchor="middle">Perceptron 1958 · XOR wall 1969</text>
  <text class="ms" x="152" y="122" text-anchor="middle">Backprop 1986 · vanishing ∇ 1991</text>

  <text class="yr" x="397" y="33" text-anchor="middle">1986–2014</text>
  <g class="seg"><title>RNN era (1986–2014): recurrent networks · LSTM 1997 · GRU and Seq2Seq 2014</title>
    <rect x="302" y="40" width="190" height="46" rx="4" fill="url(#tR)" stroke="#0891b2" stroke-width="1.3"/>
    <text class="era" x="397" y="68" text-anchor="middle">RNN era</text>
  </g>
  <text class="ms" x="397" y="100" text-anchor="middle">Recurrent nets · LSTM 1997</text>
  <text class="ms" x="397" y="111" text-anchor="middle">GRU · Seq2Seq 2014</text>

  <text class="yr" x="574" y="33" text-anchor="middle">2014–2017</text>
  <g class="seg"><title>Attention (2014–2017): Bahdanau additive attention 2015 · Luong multiplicative attention 2015</title>
    <rect x="510" y="40" width="128" height="46" rx="4" fill="url(#tA)" stroke="#2563eb" stroke-width="1.3"/>
    <text class="era" x="574" y="68" text-anchor="middle">Attention</text>
  </g>
  <text class="ms" x="574" y="100" text-anchor="middle">Bahdanau 2015</text>
  <text class="ms" x="574" y="111" text-anchor="middle">Luong 2015</text>

  <!-- SPIRAL connector: right of Attention → wrap around → left of row 2 -->
  <path class="loop" d="M638,63 H700 Q718,63 718,81 V150 Q718,168 700,168 H20" marker-end="url(#arr)"/>

  <!-- ROW 2 -->
  <text class="yr" x="107" y="181" text-anchor="middle">2017–2020</text>
  <g class="seg"><title>Transformer (2017–2020): Transformer 2017 · BERT and GPT 2018 · GPT-2 2019</title>
    <rect x="20" y="188" width="174" height="46" rx="4" fill="url(#tT)" stroke="#7c3aed" stroke-width="1.3"/>
    <text class="era" x="107" y="216" text-anchor="middle">Transformer</text>
  </g>
  <text class="ms" x="107" y="248" text-anchor="middle">Transformer 2017</text>
  <text class="ms" x="107" y="259" text-anchor="middle">BERT · GPT 2018</text>

  <text class="yr" x="330" y="181" text-anchor="middle">2020–2024</text>
  <g class="seg"><title>LLM scaling (2020–2024): GPT-3 2020 · scaling laws · InstructGPT / RLHF 2022 · ChatGPT</title>
    <rect x="212" y="188" width="236" height="46" rx="4" fill="url(#tL)" stroke="#0d9488" stroke-width="1.3"/>
    <text class="era" x="330" y="216" text-anchor="middle">LLM scaling</text>
  </g>
  <text class="ms" x="330" y="248" text-anchor="middle">GPT-3 2020 · scaling laws</text>
  <text class="ms" x="330" y="259" text-anchor="middle">RLHF / InstructGPT 2022</text>

  <text class="yr" x="572" y="181" text-anchor="middle">2024–2026</text>
  <g class="seg"><title>Agentic frontier (2024–2026): tool use · multi-step autonomy · reasoning models</title>
    <rect x="466" y="188" width="212" height="46" rx="4" fill="url(#tG)" stroke="#059669" stroke-width="1.3"/>
    <text class="era" x="572" y="216" text-anchor="middle">Agentic</text>
  </g>
  <text class="ms" x="572" y="248" text-anchor="middle">Tool use · multi-step planning</text>
  <text class="ms" x="572" y="259" text-anchor="middle">Reasoning models 2024–26</text>

  <!-- today marker -->
  <line class="today" x1="695" y1="181" x2="695" y2="238"/>
  <text class="todaylbl" x="695" y="175" text-anchor="middle">2026</text>
</svg>


## 1. Why first principles, why a timeline

As the architecture, training, and the models themselves centralize into a
handful of frontier labs, the broader community risks losing the conceptual
context — the *why it works* and *how the field got here*. Without that, it is easy
to drift toward *consuming* models rather than *understanding* them.

This primer is a small push in the other direction. Its bet is simple:
**understanding compounds when you see how each idea descends from the last.**
A timeline is not nostalgia — it is the most efficient way to learn, because
every architecture is a response to the limitations of the one before it. The
perceptron's failure motivates the multilayer network; the multilayer
network's training difficulty motivates everything after.

So this is not a survey. It is a *build*. Every concept gets three things:

- **Derivable math** — first principles, not "use this formula."
- **A minimal runnable sketch** — a few lines you can execute and poke at.
- **Its place in the lineage** — what it inherited, and what it became.

Here is the foundational arc.

## 2. The unit cell

Strip any modern network to its smallest repeating part and you find the same
object: a weighted sum followed by a non-linearity, $\phi(\mathbf{w}^\top\mathbf{x} + b)$.
McCulloch and Pitts gave the *structure* (a thresholded sum that can compute
logic).<sup id="cite-1">[[1]](#ref-1)</sup> Rosenblatt added the *learning rule*<sup id="cite-2">[[2]](#ref-2)</sup> — on each misclassified example
$(\mathbf{x}_i, y_i)$ with $y_i \in \{-1, +1\}$, nudge the weights in the
labeled direction:

$$
\mathbf{w} \leftarrow \mathbf{w} + \eta\, y_i \mathbf{x}_i,
$$

with a proof that, if the data is linearly separable, this converges.

That update rule is the ancestor of all of training. The threshold became the
activation function; the mistake-driven nudge became gradient descent; the
convergence proof became margin theory. The neuron is the cell; everything
else is composition and scale.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 820 150" role="img" aria-label="One neuron, composed and scaled, becomes every modern model" style="display:block;margin:1.5rem auto;max-width:100%">
  <style>text{font-family:system-ui,sans-serif;fill:#1e293b;font-size:12px}.b{font-weight:600}.ax{stroke:#334155;stroke-width:1.5;fill:none}rect{stroke-width:1.6}g.box{cursor:help}g.box:hover rect{stroke-width:2.6}</style>
  <defs>
    <marker id="m" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path d="M0,0 L10,5 L0,10 z" fill="#334155"/></marker>
    <pattern id="uA" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#d97706" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="uB" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#2563eb" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="uP" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#7c3aed" stroke-width="2" stroke-opacity=".07"/></pattern>
  </defs>
  <g class="box"><title>The atomic unit: a weighted sum passed through a non-linearity. McCulloch–Pitts (1943) gave the structure; Rosenblatt (1958) gave the learning rule.</title>
    <rect x="20"  y="50" width="190" height="50" rx="6" fill="url(#uA)" stroke="#d97706"/>
    <text class="b" x="115" y="72" text-anchor="middle">Artificial neuron</text>
    <text x="115" y="90" text-anchor="middle">φ(w·x + b)</text></g>
  <g class="box"><title>Stack neurons into hidden layers and train them with backpropagation (1986). This is what clears the XOR wall.</title>
    <rect x="320" y="50" width="190" height="50" rx="6" fill="url(#uB)" stroke="#2563eb"/>
    <text class="b" x="415" y="72" text-anchor="middle">Composed (MLP)</text>
    <text x="415" y="90" text-anchor="middle">+ hidden layers, backprop</text></g>
  <g class="box"><title>Scale the same loop — forward, loss, backward, update — to millions/billions of neurons: CNNs, RNNs, Transformers.</title>
    <rect x="620" y="50" width="190" height="50" rx="6" fill="url(#uP)" stroke="#7c3aed"/>
    <text class="b" x="715" y="72" text-anchor="middle">Scaled</text>
    <text x="715" y="90" text-anchor="middle">CNN · RNN · Transformer</text></g>
  <path class="ax" d="M210,75 L320,75" marker-end="url(#m)"/><text x="265" y="66" text-anchor="middle" font-size="11">compose</text>
  <path class="ax" d="M510,75 L620,75" marker-end="url(#m)"/><text x="565" y="66" text-anchor="middle" font-size="11">scale</text>
</svg>

## 3. The first wall

The perceptron has a famous failure: it cannot learn XOR. No single straight
line separates the diagonals of the unit square from the antidiagonals, and a
single neuron draws exactly one line. Minsky and Papert proved this in 1969,<sup id="cite-4">[[4]](#ref-4)</sup>
and the result chilled the field for years.

The escape is *depth* plus *non-linearity*. Add a hidden layer of neurons,
each bending the input space, and a final layer can combine the pieces into
the curved boundary XOR demands. Representational power was never the obstacle
once you stacked neurons. The obstacle was training them — which is where the
next two ideas come in.

## 4. Learning signals

Before you can train, you need to say *how wrong* a prediction is — not just
whether it's wrong. The perceptron's 0/1 mistake count has no gradient. The
fix comes from Claude Shannon's 1948 information theory:<sup id="cite-3">[[3]](#ref-3)</sup> **cross-entropy**,
the cost of a probabilistic prediction $q$ against a true label $p$,

$$
H(p, q) = -\sum_x p(x)\log q(x).
$$

It is smooth, differentiable, and brutally unforgiving of confident mistakes —
exactly the properties a gradient-based learner needs. Cross-entropy is still
the training objective of essentially every classifier and language model
today; next-token prediction is cross-entropy over a vocabulary.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 560 218" role="img" aria-label="Binary cross-entropy loss for a positive example: near zero for confident correct predictions, high for confident wrong ones" style="display:block;margin:1.5rem auto;max-width:100%">
  <style>text{font-family:system-ui,sans-serif;fill:#1e293b;font-size:11px}.ax{stroke:#94a3b8;stroke-width:1.5}</style>
  <defs>
    <marker id="axA" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto"><path d="M0,0 L9,5 L0,10 z" fill="#94a3b8"/></marker>
  </defs>
  <line class="ax" x1="60" y1="26" x2="60" y2="172" marker-end="url(#axA)"/>
  <line class="ax" x1="55" y1="167" x2="508" y2="167" marker-end="url(#axA)"/>
  <text x="283" y="196" text-anchor="middle" fill="#64748b">predicted probability  q = P(y=1)</text>
  <text x="0" y="0" fill="#64748b" transform="translate(18,97) rotate(-90)" text-anchor="middle">loss  −log q</text>
  <text x="104" y="184" text-anchor="middle" fill="#94a3b8">0.1</text>
  <text x="280" y="184" text-anchor="middle" fill="#94a3b8">0.5</text>
  <text x="456" y="184" text-anchor="middle" fill="#94a3b8">0.9</text>
  <!-- BCE curve: -log(q) for y=1, q mapped to x=60+q*440, loss/4.0*135 mapped to y -->
  <path d="M69,32 C76,48 84,63 104,86 C148,110 192,124 236,134 C280,143 324,149 368,155 C412,160 456,163 496,166" fill="none" stroke="#d97706" stroke-width="2.5"/>
  <!-- wrong+confident q=0.1 -->
  <line x1="104" y1="86" x2="104" y2="168" stroke="#dc2626" stroke-width="1" stroke-dasharray="3 2"/>
  <circle cx="104" cy="86" r="5" fill="#dc2626" stroke="white" stroke-width="1.5"/>
  <text x="104" y="75" text-anchor="middle" fill="#dc2626" font-weight="600">confident</text>
  <text x="104" y="64" text-anchor="middle" fill="#dc2626" font-weight="600">+ wrong</text>
  <!-- uncertain q=0.5 -->
  <line x1="280" y1="143" x2="280" y2="168" stroke="#d97706" stroke-width="1" stroke-dasharray="3 2"/>
  <circle cx="280" cy="143" r="5" fill="#d97706" stroke="white" stroke-width="1.5"/>
  <text x="280" y="132" text-anchor="middle" fill="#d97706" font-weight="600">uncertain</text>
  <!-- correct q=0.9 -->
  <circle cx="456" cy="163" r="5" fill="#16a34a" stroke="white" stroke-width="1.5"/>
  <text x="456" y="152" text-anchor="middle" fill="#16a34a" font-weight="600">correct</text>
  <text x="283" y="20" text-anchor="middle" fill="#94a3b8" font-style="italic">−log q for a positive example (y=1) — smooth, penalizes confident mistakes without limit</text>
</svg>

## 5. Training depth

With a loss in hand, **backpropagation** (Rumelhart, Hinton & Williams, 1986)<sup id="cite-5">[[5]](#ref-5)</sup>
computes every parameter's gradient in one organized backward sweep — the
chain rule, arranged so each layer's error is computed once and reused. The
error signal at layer $l$ obeys a single recursion:

$$
\boldsymbol{\delta}^{(l)} = \big((\mathbf{W}^{(l+1)})^\top \boldsymbol{\delta}^{(l+1)}\big) \odot \phi'(\mathbf{z}^{(l)}),
$$

and the weight gradient is $\partial\mathcal{L}/\partial\mathbf{W}^{(l)} = \boldsymbol{\delta}^{(l)}(\mathbf{a}^{(l-1)})^\top$. This is the algorithm behind
`loss.backward()` in every framework, unchanged in principle since.

But backprop carries its own curse. It *multiplies* a factor per layer, so in
a deep stack those factors compound — and gradients either **vanish** toward
zero or **explode**. This single fact, identified by Hochreiter (1991)<sup id="cite-6">[[6]](#ref-6)</sup> and
Bengio et al. (1994),<sup id="cite-7">[[7]](#ref-7)</sup> is the central obstacle to depth, and the reason much of
what follows exists.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 200" role="img" aria-label="Gradient magnitude vanishes or explodes with depth" style="display:block;margin:1.5rem auto;max-width:100%">
  <style>text{font-family:system-ui,sans-serif;fill:#1e293b;font-size:12px}.ax{stroke:#94a3b8;stroke-width:1.5}g.fig{cursor:help}</style>
  <defs>
    <pattern id="gGap" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#dc2626" stroke-width="1.6" stroke-opacity=".07"/></pattern>
  </defs>
  <g class="fig"><title>Backprop multiplies one factor per layer. If the typical factor is below 1 the gradient decays exponentially with depth (vanish); above 1 it grows without bound (explode). The hatched wedge is the widening spread.</title>
    <line class="ax" x1="60" y1="160" x2="600" y2="160"/>
    <line class="ax" x1="60" y1="160" x2="60" y2="25"/>
    <text x="330" y="190" text-anchor="middle" font-size="11" fill="#64748b">depth (layers) →</text>
    <text x="24" y="95" font-size="11" fill="#64748b" transform="rotate(-90 24,95)" text-anchor="middle">‖gradient‖</text>
    <path d="M60,150 C200,140 320,110 600,40 L600,159 C320,156 200,152 60,150 Z" fill="url(#gGap)" stroke="none"/>
    <path d="M60,150 C200,140 320,110 600,40"  fill="none" stroke="#dc2626" stroke-width="2.5"/>
    <path d="M60,150 C200,152 320,156 600,159"  fill="none" stroke="#2563eb" stroke-width="2.5"/>
    <text x="520" y="38"  fill="#dc2626" font-weight="600">explode</text>
    <text x="520" y="150" fill="#2563eb" font-weight="600">vanish</text>
  </g>
</svg>

A remarkable share of architectural innovation is, at heart, a single
question: *how to keep the per-layer factor near 1 so gradients survive
the trip back?* The recurring answers:

- **LSTM / GRU gating** — an additive memory path that doesn't multiply the gradient away.
- **Residual connections** — an identity shortcut that always offers a factor-1 route.
- **Normalization** (Batch / Layer / RMS) — re-center activations so units don't saturate.
- **Non-saturating activations** (ReLU and kin) — derivative 1 for active units, no 0.25 shrink.

Hold that question; it organizes the rest of the timeline.

## 6. From foundations to the frontier

The four ideas above are the whole game in miniature — *represent, measure,
differentiate, keep gradients alive.* Everything after is a way of applying
them to **sequences** and at **scale**.

### Sequences to the Transformer

**Sequences → attention.** RNNs (and the LSTM, 1997)<sup id="cite-8">[[8]](#ref-8)</sup> process tokens one at a
time, carrying a hidden state — but a fixed-size state is a bottleneck for long
inputs. **Attention** (Bahdanau, 2015)<sup id="cite-9">[[9]](#ref-9)</sup> fixed this by letting the model look
back at *all* prior states, weighted by relevance.

**Attention → Transformer.** In 2017 the Transformer<sup id="cite-10">[[10]](#ref-10)</sup> threw out recurrence
entirely and kept only attention. Its core operation, **self-attention**,
is one equation:

$$
\mathrm{Attention}(Q, K, V) = \mathrm{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V.
$$

Each token builds a query, compares it against every other token's key (a
similarity score), and reads a weighted blend of their values. It is fully
parallel — which is exactly what made training at scale practical.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 760 210" role="img" aria-label="Self-attention: token 2 queries all positions; attention weights determine how much of each value to mix into the output" style="display:block;margin:1.5rem auto;max-width:100%">
  <style>text{font-family:system-ui,sans-serif;fill:#1e293b;font-size:11px}.b{font-weight:600}.sm{font-size:9.5px;fill:#64748b}.wt{font-size:10px}</style>
  <defs>
    <marker id="aG" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto"><path d="M0,0 L9,5 L0,10 z" fill="#94a3b8"/></marker>
    <marker id="aQ" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto"><path d="M0,0 L9,5 L0,10 z" fill="#d97706"/></marker>
  </defs>

  <!-- Output box for token 2 -->
  <rect x="300" y="15" width="160" height="38" rx="4" fill="#ede9fe" stroke="#7c3aed" stroke-width="1.5"/>
  <text class="b" x="380" y="35" text-anchor="middle" fill="#7c3aed">output₂</text>
  <text class="sm" x="380" y="48" text-anchor="middle" fill="#7c3aed">= Σ αᵢ · Vᵢ</text>

  <!-- Lines from inputs to output (varying weight/opacity) -->
  <line x1="110" y1="143" x2="345" y2="53" stroke="#94a3b8" stroke-width="1.2" stroke-opacity=".5" marker-end="url(#aG)"/>
  <line x1="380" y1="143" x2="380" y2="53" stroke="#d97706" stroke-width="3.5" marker-end="url(#aQ)"/>
  <line x1="650" y1="143" x2="415" y2="53" stroke="#94a3b8" stroke-width="1.2" stroke-opacity=".5" marker-end="url(#aG)"/>

  <!-- Attention weight labels -->
  <text class="wt" x="215" y="92" text-anchor="middle" fill="#94a3b8">α = 0.15</text>
  <text class="wt" x="380" y="103" text-anchor="middle" fill="#d97706" font-weight="600">α = 0.70</text>
  <text class="wt" x="544" y="92" text-anchor="middle" fill="#94a3b8">α = 0.15</text>

  <!-- Formula annotation -->
  <text class="sm" x="380" y="126" text-anchor="middle" fill="#64748b" font-style="italic">αᵢ = softmax( Q₂ · Kᵢᵀ / √d )</text>

  <!-- Input boxes -->
  <rect x="40" y="143" width="140" height="38" rx="4" fill="#f8fafc" stroke="#94a3b8" stroke-width="1.3"/>
  <text class="b" x="110" y="166" text-anchor="middle">token 1</text>
  <rect x="300" y="143" width="160" height="38" rx="4" fill="#fef3c7" stroke="#d97706" stroke-width="1.5"/>
  <text class="b" x="380" y="166" text-anchor="middle" fill="#92400e">token 2 · query</text>
  <rect x="580" y="143" width="140" height="38" rx="4" fill="#f8fafc" stroke="#94a3b8" stroke-width="1.3"/>
  <text class="b" x="650" y="166" text-anchor="middle">token 3</text>

  <!-- Q K V role labels -->
  <text class="sm" x="110" y="196" text-anchor="middle">K · V</text>
  <text class="sm" x="380" y="196" text-anchor="middle">Q · K · V</text>
  <text class="sm" x="650" y="196" text-anchor="middle">K · V</text>
</svg>

### Scale, LLMs, and alignment

**Transformer → LLMs.** From there the story is mostly **scale plus
objective**:

- **Pretraining** is just cross-entropy (section 4) over a vocabulary,
  predicting the next token across trillions of them.
- **BERT**<sup id="cite-11">[[11]](#ref-11)</sup> vs **GPT**<sup id="cite-12">[[12]](#ref-12)</sup> (2018) split on objective: masked-token (bidirectional) vs
  next-token (causal). The causal branch became the LLM lineage.
- **Scaling laws**<sup id="cite-13">[[13]](#ref-13)</sup> (2020) turned "make it bigger" into a quantitative recipe;
  GPT-3<sup id="cite-14">[[14]](#ref-14)</sup> showed the payoff.
- **RLHF / instruction tuning**<sup id="cite-15">[[15]](#ref-15)</sup> (2022) aligned raw next-token predictors into
  assistants — adding a preference signal on top of cross-entropy.

**LLMs → today.** The 2024–2026 frontier moved from single answers to
**agentic** systems: tool use, multi-step planning, and reasoning models that
spend compute at inference time. The unit cell hasn't changed — it is still
$\phi(\mathbf{w}^\top\mathbf{x}+b)$, trained by backprop on cross-entropy. What
changed is the orchestration around it.

The formalizations behind these later stages — **softmax** for multi-class
outputs, **scaling laws** for the empirical $L \propto N^{-\alpha}$
relationship, **RLHF's** reward + KL objective — are deferred to their
respective deep-dives, where they motivate themselves naturally.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 820 60" role="img" aria-label="RLHF training pipeline: four stages from pretraining to alignment" style="display:block;margin:1.5rem auto 0;max-width:100%">
  <style>text{font-family:system-ui,sans-serif;fill:#1e293b;font-size:12px}.ttl{font-size:12px;font-weight:600}.ax{stroke:#94a3b8;stroke-width:1.5;fill:none}</style>
  <defs>
    <marker id="arrF" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L9,5 L0,10 z" fill="#94a3b8"/></marker>
    <pattern id="pPT" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#7c3aed" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="pSF" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#0d9488" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="pRM" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#2563eb" stroke-width="2" stroke-opacity=".07"/></pattern>
    <pattern id="pRL" width="8" height="8" patternUnits="userSpaceOnUse" patternTransform="rotate(45)"><line x1="0" y1="0" x2="0" y2="8" stroke="#059669" stroke-width="2" stroke-opacity=".07"/></pattern>
  </defs>
  <rect x="10" y="8" width="172" height="38" rx="5" fill="url(#pPT)" stroke="#7c3aed" stroke-width="1.3"/>
  <text class="ttl" x="96" y="32" text-anchor="middle">① Pretrain</text>
  <path class="ax" d="M182,27 H208" marker-end="url(#arrF)"/>
  <rect x="210" y="8" width="168" height="38" rx="5" fill="url(#pSF)" stroke="#0d9488" stroke-width="1.3"/>
  <text class="ttl" x="294" y="32" text-anchor="middle">② SFT</text>
  <path class="ax" d="M378,27 H404" marker-end="url(#arrF)"/>
  <rect x="406" y="8" width="180" height="38" rx="5" fill="url(#pRM)" stroke="#2563eb" stroke-width="1.3"/>
  <text class="ttl" x="496" y="32" text-anchor="middle">③ Reward model</text>
  <path class="ax" d="M586,27 H612" marker-end="url(#arrF)"/>
  <rect x="614" y="8" width="192" height="38" rx="5" fill="url(#pRL)" stroke="#059669" stroke-width="1.3"/>
  <text class="ttl" x="710" y="32" text-anchor="middle">④ RLHF / PPO</text>
</svg>
<table style="width:100%;max-width:820px;margin:.5rem auto 0;border-collapse:collapse;font-size:.82rem;color:#475569">
<thead><tr>
<th style="width:21%;padding:.3rem .5rem;color:#7c3aed;font-weight:600;text-align:left">① Pretrain</th>
<th style="width:20%;padding:.3rem .5rem;color:#0d9488;font-weight:600;text-align:left">② SFT</th>
<th style="width:22%;padding:.3rem .5rem;color:#2563eb;font-weight:600;text-align:left">③ Reward model</th>
<th style="width:37%;padding:.3rem .5rem;color:#059669;font-weight:600;text-align:left">④ RLHF / PPO</th>
</tr></thead>
<tbody><tr>
<td style="padding:.2rem .5rem;vertical-align:top">next-token prediction on large corpus<br><em>loss: −log P(next | context)</em></td>
<td style="padding:.2rem .5rem;vertical-align:top">fine-tune on human demonstrations<br><em>same CE on (prompt, response)</em></td>
<td style="padding:.2rem .5rem;vertical-align:top">train on preference rankings<br><em>learns r(prompt, response) → scalar</em></td>
<td style="padding:.2rem .5rem;vertical-align:top">maximize reward, stay near SFT policy<br><em>loss: −E[r] + β·KL(π ‖ π₀)</em></td>
</tr></tbody>
</table>
<p style="text-align:center;font-size:.78rem;color:#94a3b8;font-style:italic;margin:.4rem 0 1.5rem">Shannon's cross-entropy (1948) is the backbone at every stage — RLHF adds a KL penalty to keep the policy close to the reference</p>

> Every box in this section is a deep-dive in its own right — and each will get
> its own post. This primer is the **map**; the territory comes next.

## 7. Takeaways

1. The artificial neuron — $\phi(\mathbf{w}^\top\mathbf{x}+b)$ — is the unit
   cell of every model. Everything else is **composition and scale**.
2. The perceptron's **XOR wall** is what forced depth and non-linearity.
3. **Cross-entropy** (Shannon, 1948) is the learning signal that makes
   gradient descent possible — and still trains every LLM.
4. **Backprop** makes depth trainable; **vanishing/exploding gradients** make
   it hard — and motivate most of what came next.
5. The modern arc — attention, the Transformer, LLMs, agents — is these same
   ideas applied to **sequences** and at **scale**. The unit cell never changed.

A handful of ideas, spanning 1943 to today, sit under every model in
production. Understand them once, from first principles, and the modern
frontier stops looking like magic and starts looking like *consequences*.

## 8. What's next in this series

This post is **the map**. Each segment of the timeline above becomes its own
deep-dive — derivation, runnable code, and history — in upcoming posts:

- **The RNN era** — recurrence, LSTM gating, and the sequence bottleneck.
- **Attention** — Bahdanau and Luong, additive vs multiplicative, the alignment story.
- **The Transformer** — self-attention line by line, multi-head, positional encoding.
- **The LLM era** — pretraining, scaling laws, and alignment (RLHF / DPO).
- **The agentic frontier** — tools, planning, and reasoning at inference time.

They all build on the foundations here, so this is the post to read first. 

**End note** — this post distills the [`nn-timeline`](https://github.com/surafelml/nn-timeline) book, *Neural Network Architectures Through Time*; full formalizations and runnable reference code live in the repo. Read the draft [`nn-timeline book`](https://surafelml.github.io/nn-timeline/). 

---

## References

**1. Foundations**

<ol>
<li id="ref-1">McCulloch &amp; Pitts (1943), <em>A Logical Calculus of the Ideas Immanent in Nervous Activity</em>, Bulletin of Mathematical Biophysics. <a href="#cite-1" title="back to text">↩</a></li>
<li id="ref-2">Rosenblatt (1958), <em>The Perceptron: A Probabilistic Model for Information Storage and Organization in the Brain</em>, Psychological Review. <a href="#cite-2" title="back to text">↩</a></li>
<li id="ref-3">Shannon (1948), <a href="https://people.math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf"><em>A Mathematical Theory of Communication</em></a>, Bell System Technical Journal. <a href="#cite-3" title="back to text">↩</a></li>
<li id="ref-4">Minsky &amp; Papert (1969), <em>Perceptrons</em>, MIT Press. <a href="#cite-4" title="back to text">↩</a></li>
</ol>

**2. Training depth**

<ol start="5">
<li id="ref-5">Rumelhart, Hinton &amp; Williams (1986), <a href="https://www.nature.com/articles/323533a0"><em>Learning Representations by Back-propagating Errors</em></a>, Nature 323. <a href="#cite-5" title="back to text">↩</a></li>
<li id="ref-6">Hochreiter (1991), <em>Untersuchungen zu dynamischen neuronalen Netzen</em> (vanishing gradients), Diploma thesis, TU Munich. <a href="#cite-6" title="back to text">↩</a></li>
<li id="ref-7">Bengio, Simard &amp; Frasconi (1994), <a href="https://ieeexplore.ieee.org/document/279181"><em>Learning Long-Term Dependencies with Gradient Descent is Difficult</em></a>, IEEE Trans. Neural Networks. <a href="#cite-7" title="back to text">↩</a></li>
</ol>

**3. Sequences, attention and scale**

<ol start="8">
<li id="ref-8">Hochreiter &amp; Schmidhuber (1997), <a href="https://www.bioinf.jku.at/publications/older/2604.pdf"><em>Long Short-Term Memory</em></a>, Neural Computation. <a href="#cite-8" title="back to text">↩</a></li>
<li id="ref-9">Bahdanau, Cho &amp; Bengio (2015), <a href="https://arxiv.org/abs/1409.0473"><em>Neural Machine Translation by Jointly Learning to Align and Translate</em></a>. <a href="#cite-9" title="back to text">↩</a></li>
<li id="ref-10">Vaswani et al. (2017), <a href="https://arxiv.org/abs/1706.03762"><em>Attention Is All You Need</em></a>. <a href="#cite-10" title="back to text">↩</a></li>
<li id="ref-11">Devlin et al. (2018), <a href="https://arxiv.org/abs/1810.04805"><em>BERT: Pre-training of Deep Bidirectional Transformers</em></a>. <a href="#cite-11" title="back to text">↩</a></li>
<li id="ref-12">Radford et al. (2018), <a href="https://openai.com/index/language-unsupervised/"><em>Improving Language Understanding by Generative Pre-Training</em></a> (GPT-1). <a href="#cite-12" title="back to text">↩</a></li>
<li id="ref-13">Kaplan et al. (2020), <a href="https://arxiv.org/abs/2001.08361"><em>Scaling Laws for Neural Language Models</em></a>. <a href="#cite-13" title="back to text">↩</a></li>
<li id="ref-14">Brown et al. (2020), <a href="https://arxiv.org/abs/2005.14165"><em>Language Models are Few-Shot Learners</em></a> (GPT-3). <a href="#cite-14" title="back to text">↩</a></li>
<li id="ref-15">Ouyang et al. (2022), <a href="https://arxiv.org/abs/2203.02155"><em>Training Language Models to Follow Instructions with Human Feedback</em></a> (InstructGPT / RLHF). <a href="#cite-15" title="back to text">↩</a></li>
</ol>
