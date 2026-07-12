---
title: "Protocol for Agentic Evaluation: The Performance–Safety Duality"
date: 2025-09-17
last_modified_at: 2026-06-27
published: true
featured: false
math: true
emoji: "📊"
excerpt: "Agentic systems must be evaluated on two inseparable axes — performance (can it complete the task?) and safety (does it stay within bounds?). A protocol that composes them into a single ship criterion, tracing the history of evaluation up to today's agentic frontier."
tags:
  - Generative AI Evaluation
  - Agentic Eval
  - AI Safety
  - Responsible AI
  - LLM-as-Judge
---

> **TL;DR** — Most LLM-centered evaluation approaches ask whether the answer is accurate and faithful. Agentic evaluation must ask whether the agent reached its goal *and* whether it stayed safe along the way. I argue performance and safety are a **duality**: two evaluation axes that must be quantified together. I also lay out a matrix for doing so across the agentic development lifecycle.


<figure class="post-figure post-figure--full">
<svg viewBox="0 0 880 360" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Performance vs Safety quadrants" style="width:100%;height:auto;">
  <style>
    .ax { stroke: currentColor; stroke-width: 1.8; fill: none; }
    .gd { stroke: currentColor; stroke-width: 1; opacity: 0.55; stroke-dasharray: 4 4; }
    .lb    { font: 15px Optima, Candara, sans-serif; fill: currentColor; }
    .lb-sm { font: 13px Optima, Candara, sans-serif; fill: currentColor; opacity: 0.9; }
    .lb-q  { font: 700 16px Optima, Candara, sans-serif; }
    .q-ship   { fill: #1E8449; fill-opacity: 0.22; }
    .q-reck   { fill: #C0392B; fill-opacity: 0.22; }
    .q-restr  { fill: #D4AC0D; fill-opacity: 0.22; }
    .q-broken { fill: currentColor; fill-opacity: 0.10; }
    .dot { fill: #145A32; stroke: #FFFFFF; stroke-width: 1.5; }
  </style>
  <rect x="100" y="40"  width="370" height="135" class="q-restr"/>
  <rect x="470" y="40"  width="370" height="135" class="q-ship"/>
  <rect x="100" y="175" width="370" height="135" class="q-broken"/>
  <rect x="470" y="175" width="370" height="135" class="q-reck"/>
  <line x1="100" y1="310" x2="850" y2="310" class="ax"/>
  <line x1="100" y1="40"  x2="100" y2="310" class="ax"/>
  <line x1="470" y1="40"  x2="470" y2="310" class="gd"/>
  <line x1="100" y1="175" x2="840" y2="175" class="gd"/>
  <text x="470" y="335" text-anchor="middle" class="lb">Performance  →</text>
  <text x="70"  y="175" text-anchor="middle" class="lb" transform="rotate(-90 70 175)">Safety  →</text>
  <text x="285" y="100" text-anchor="middle" class="lb-q" fill="#D4AC0D">Restrictive</text>
  <text x="285" y="122" text-anchor="middle" class="lb-sm">safe, useless</text>
  <text x="655" y="100" text-anchor="middle" class="lb-q" fill="#1E8449">Ship</text>
  <text x="655" y="122" text-anchor="middle" class="lb-sm">capable + safe</text>
  <text x="285" y="235" text-anchor="middle" class="lb-q" fill="currentColor" opacity="0.55">Broken</text>
  <text x="285" y="257" text-anchor="middle" class="lb-sm">unsafe + useless</text>
  <text x="655" y="235" text-anchor="middle" class="lb-q" fill="#C0392B">Reckless</text>
  <text x="655" y="257" text-anchor="middle" class="lb-sm">capable, unsafe</text>
  <circle cx="700" cy="85" r="6" class="dot"/>
  <text x="470" y="355" text-anchor="middle" class="lb-sm">Evaluating either axis alone hides the other failure mode.</text>
</svg>
<figcaption>Figure 1 — Performance and safety as a duality. Systems must land in the upper-right (green) quadrant; the other three are distinct failure modes.</figcaption>
</figure>

---


## Defining Agentic Systems

I use *agentic* in the operational sense: a system that (a) uses **tools** beyond text generation: APIs, file I/O, code execution, web actions; (b) executes a **multi-step trajectory** rather than a single response; and (c) holds **autonomy over the next action** inside that trajectory, choosing the next tool call from its own state rather than waiting for a user turn. Tool-using assistants, browsing agents, and ops bots all qualify; a pure chat model does not.

<figure class="post-figure post-figure--full">
<svg viewBox="0 0 760 210" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Agentic system components with evaluation bridges" style="width:100%;height:auto;">
  <style>
    .ag-box { fill: none; stroke: currentColor; stroke-width: 1.4; }
    .ag-sub { fill: currentColor; fill-opacity: 0.07; stroke: currentColor; stroke-width: 0.8; }
    .ag-goal { fill: #1E8449; fill-opacity: 0.15; stroke: #1E8449; stroke-width: 1.4; }
    .ag-ep  { fill: #D4AC0D; fill-opacity: 0.16; stroke: #B7950B; stroke-width: 1.3; }
    .ag-es  { fill: #C0392B; fill-opacity: 0.15; stroke: #C0392B; stroke-width: 1.3; }
    .ag-lb  { font: 600 13px Optima, Candara, sans-serif; fill: currentColor; }
    .ag-lbs { font: 11px Optima, Candara, sans-serif; fill: currentColor; opacity: 0.88; }
    .ag-lbp { font: 600 11px Optima, Candara, sans-serif; fill: #7D6608; }
    .ag-lbr { font: 600 11px Optima, Candara, sans-serif; fill: #C0392B; }
    .ag-lbg { font: 12px Optima, Candara, sans-serif; fill: #1E8449; }
    .ag-arr { stroke: currentColor; stroke-width: 1.4; fill: none; marker-end: url(#ag-ah); }
    .ag-obs { stroke: currentColor; stroke-width: 1.1; stroke-dasharray: 5 3; fill: none; opacity: 0.55; marker-end: url(#ag-ah); }
    .ag-ds  { stroke: #C0392B; stroke-width: 1.2; stroke-dasharray: 3 2; fill: none; opacity: 0.7; marker-end: url(#ag-ahs); }
    .ag-dp  { stroke: #B7950B; stroke-width: 1.2; stroke-dasharray: 3 2; fill: none; opacity: 0.7; marker-end: url(#ag-ahp); }
  </style>
  <defs>
    <marker id="ag-ah" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="currentColor"/>
    </marker>
    <marker id="ag-ahs" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="#C0392B"/>
    </marker>
    <marker id="ag-ahp" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="#B7950B"/>
    </marker>
  </defs>
  <!-- S eval badge -->
  <rect x="320" y="8" width="172" height="36" rx="5" class="ag-es"/>
  <text x="406" y="23" text-anchor="middle" class="ag-lbr">S: per-step safety check</text>
  <text x="406" y="38" text-anchor="middle" class="ag-lbs" style="fill:#C0392B;opacity:0.85">a_i ∈ allowed(s_i)? one fail voids run</text>
  <line x1="406" y1="44" x2="406" y2="60" class="ag-ds"/>
  <!-- P eval badge -->
  <rect x="660" y="8" width="96" height="36" rx="5" class="ag-ep"/>
  <text x="708" y="23" text-anchor="middle" class="ag-lbp">P: terminal check</text>
  <text x="708" y="38" text-anchor="middle" class="ag-lbs" style="fill:#7D6608;opacity:0.85">goal(t) met at s_T?</text>
  <line x1="708" y1="44" x2="708" y2="62" class="ag-dp"/>
  <!-- User / Task -->
  <rect x="8" y="64" width="80" height="90" rx="6" class="ag-box"/>
  <text x="48" y="104" text-anchor="middle" class="ag-lb">User /</text>
  <text x="48" y="122" text-anchor="middle" class="ag-lb">Task</text>
  <line x1="88" y1="109" x2="108" y2="109" class="ag-arr"/>
  <!-- Agent Core -->
  <rect x="112" y="64" width="188" height="90" rx="8" class="ag-box"/>
  <text x="206" y="83" text-anchor="middle" class="ag-lb">Agent Core</text>
  <rect x="124" y="89" width="164" height="30" rx="4" class="ag-sub"/>
  <text x="206" y="109" text-anchor="middle" class="ag-lbs">LLM / Planner: reasons, selects action</text>
  <rect x="124" y="124" width="76" height="26" rx="4" class="ag-sub"/>
  <text x="162" y="141" text-anchor="middle" class="ag-lbs">Memory</text>
  <rect x="206" y="124" width="76" height="26" rx="4" class="ag-sub"/>
  <text x="244" y="141" text-anchor="middle" class="ag-lbs">State s_i</text>
  <line x1="300" y1="109" x2="320" y2="109" class="ag-arr"/>
  <!-- Tool Layer -->
  <rect x="324" y="64" width="172" height="90" rx="8" class="ag-box"/>
  <text x="410" y="83" text-anchor="middle" class="ag-lb">Tool Layer</text>
  <text x="410" y="100" text-anchor="middle" class="ag-lbs">API · shell · file I/O</text>
  <text x="410" y="117" text-anchor="middle" class="ag-lbs">code exec · web · DB</text>
  <text x="410" y="140" text-anchor="middle" class="ag-lbs">action a_i issued here</text>
  <line x1="496" y1="109" x2="516" y2="109" class="ag-arr"/>
  <!-- Environment -->
  <rect x="520" y="64" width="132" height="90" rx="8" class="ag-box"/>
  <text x="586" y="83" text-anchor="middle" class="ag-lb">Environment</text>
  <text x="586" y="100" text-anchor="middle" class="ag-lbs">state transitions</text>
  <text x="586" y="117" text-anchor="middle" class="ag-lbs">side effects</text>
  <text x="586" y="134" text-anchor="middle" class="ag-lbs">observations</text>
  <line x1="652" y1="109" x2="670" y2="109" class="ag-arr"/>
  <!-- Goal / Terminal -->
  <rect x="674" y="78" width="66" height="62" rx="6" class="ag-goal"/>
  <text x="707" y="107" text-anchor="middle" class="ag-lbg">Goal</text>
  <text x="707" y="124" text-anchor="middle" class="ag-lbs" style="fill:#1E8449">s_T</text>
  <!-- Observation return arc -->
  <path d="M 586 154 C 586 182, 206 182, 206 154" fill="none" class="ag-obs"/>
  <text x="396" y="200" text-anchor="middle" class="ag-lbs">next state s_{i+1} returned as observation; agent updates memory and replans</text>
</svg>
<figcaption>Figure 2 — Agentic system components and evaluation bridges. S is checked at every tool invocation; P is measured at the terminal state s_T. Together it can form the duality formalization we will discuss below <a href="#the-duality-formalization">U = E[P·S] ≥ τ</a>.</figcaption>
</figure>


## A Short History of Evaluation

Agentic evaluation did not appear from nowhere; it is the latest turn of a wheel that NLP, vision, and speech have all turned before. The recurring pattern is worth stating plainly:

> **Every era of evaluation has progressed *metric → standardized toolkit → learned / holistic measure*** — and each step was driven by the previous metric saturating or being gamed. The agentic era is rapidly evolving at the first step: many metrics, no standard protocol.

### Pre-LLM Era

Before instruction-following models, evaluation was a formula problem: given a hypothesis and a reference, could a computable proxy predict human judgment at scale? Each field solved it, then hit the same ceiling when models learned to optimize the metric directly.

| Metric | Formalization & Note |
|---|---|
| [BLEU](https://aclanthology.org/P02-1040/) (Papineni et al., 2002, ACL) | $\mathrm{BP}\cdot\exp\!\big(\sum_n w_n \log p_n\big)$ — $\mathrm{BP}=\min(1,\exp(1{-}r/c))$ penalizes outputs shorter than the reference ($r$ = ref length, $c$ = candidate); $p_n$ = clipped n-gram precision; $w_n=1/N$ (uniform); dominant MT metric for two decades |
| [ROUGE](https://aclanthology.org/W04-1013/) (Lin, 2004, ACL-WS) | $\mathrm{ROUGE\text{-}N}=\tfrac{\text{matched }n\text{-grams in reference}}{\text{all }n\text{-grams in reference}}$ — recall-oriented counterpart to BLEU; ROUGE-L replaces n-gram overlap with longest common subsequence length; standard for summarization |
| [FID](https://arxiv.org/abs/1706.08500) (Heusel et al., 2017, NeurIPS) | $\|\mu_r{-}\mu_g\|^2{+}\mathrm{Tr}(\Sigma_r{+}\Sigma_g{-}2(\Sigma_r\Sigma_g)^{1/2})$ — Fréchet distance between Inception-feature Gaussians; $\mu,\Sigma$ = mean/covariance of real ($r$) vs. generated ($g$) image sets; lower = closer distributions; displaced Inception Score as the generative-image standard |
| [sacreBLEU](https://aclanthology.org/W18-6319/) (Post, 2018, WMT) | BLEU computed with a canonical tokenizer and an explicit version string embedded in the score — eliminates cross-lab tokenization inconsistency; the *toolkit* step of the metric → toolkit → learned arc |
| [GLUE](https://openreview.net/forum?id=rJ4km2R5t7) / [SuperGLUE](https://arxiv.org/abs/1905.00537) (Wang et al., 2019, ICLR / NeurIPS) | Macro-average across 8 NLU tasks (GLUE) / 8 harder tasks including co-reference and multi-hop QA (SuperGLUE) — first standardized multi-task suites; established holistic multi-metric reporting and public model ranking |
| [BERTScore](https://openreview.net/forum?id=SkeHuCVFDr) (Zhang et al., 2020, ICLR) | $F_\text{BERT}=\tfrac{2\,P_\text{BERT}\,R_\text{BERT}}{P_\text{BERT}+R_\text{BERT}}$ — candidate/reference tokens greedily matched by cosine similarity over BERT embeddings; $P_\text{BERT}$ over candidate tokens, $R_\text{BERT}$ over reference tokens; first embedding-based metric to achieve wide adoption |
| [BLEURT](https://aclanthology.org/2020.acl-main.704/) (Sellam et al., 2020, ACL) | BERT encoder fine-tuned end-to-end on WMT human segment-level DA ratings via multi-task pre-training on synthetic perturbations — first metric trained directly on human preference scores; showed reference-based scoring can be replaced by a learned regressor |
| [COMET](https://aclanthology.org/2020.emnlp-main.213/) (Rei et al., 2020, EMNLP) | XLM-R encoder regressed on DA/MQM human judgments; takes source $s$, hypothesis $h$, and reference $r$ as input — unlike BLEU/BERTScore (reference only), captures adequacy and fluency jointly; now the standard MT metric in WMT evaluations |

Additional metrics from this era (NIST, METEOR, TER, chrF, MoverScore, CIDEr, SPICE, Inception Score) are catalogued in the [appendix](#appendix-pre-llm-evaluation-metrics).

The pattern is consistent across subfields: surface metrics (BLEU, ROUGE) gave way to reproducible toolkits (sacreBLEU), which in turn gave way to learned metrics that regress directly on human preference (BLEURT, COMET). Each transition was forced: BLEU was gamed, ROUGE saturated, toolkit variants proliferated.

### LLM Era

Generative, instruction-following models broke the reference-based paradigm: outputs no longer converge on a fixed correct form, making surface similarity against a single reference increasingly meaningless. The field shifted from measuring *closeness to a reference* to measuring *quality as judged by a model or crowd*.

| Method | Formalization & Note |
|---|---|
| [pass@k](https://arxiv.org/abs/2107.03374) (Chen et al., 2021, arXiv) | $1-\binom{n-c}{k}/\binom{n}{k}$ — P(at least 1 of $k$ randomly drawn samples pass unit tests), estimated from $n$ total generations with $c$ passing; avoids upward bias of reporting best-of-$n$; first widely used *behavioral* metric |
| [HELM](https://arxiv.org/abs/2211.09110) (Liang et al., 2023, TMLR) | Multi-model × multi-scenario evaluation matrix: standardized scenarios (QA, summarization, classification, code, …) × metrics (accuracy, robustness, calibration, fairness, efficiency); identical prompting across all models — **holistic** standardization; the *toolkit* step of the LLM-era arc |
| [G-Eval](https://arxiv.org/abs/2303.16634) (Liu et al., 2023, EMNLP) | GPT-4 fills a task-specific rubric via chain-of-thought, then the score is $\sum_s s\cdot P(\text{tok}=s)$ over score-value tokens — probability-weighting extracts more signal than argmax; higher human alignment than direct scalar ratings |
| [MT-Bench](https://arxiv.org/abs/2306.05685) (Zheng et al., 2023, NeurIPS) | GPT-4 scores responses on 80 two-turn questions across 8 categories (coding, math, reasoning, writing, …) on a 1–10 scale (pointwise); also supports pairwise win-rate — established LLM-as-judge from ad-hoc tool to standard practice |
| [Prometheus](https://arxiv.org/abs/2310.08491) (Kim et al., 2024, ICLR) | Open-weight 7/13B judge fine-tuned on 100K GPT-4-generated (feedback, score) pairs over custom rubrics — first open model to match GPT-4 Pearson correlation on fine-grained rubric scoring; enables reproducible evaluation without API dependency |
| [Chatbot Arena](https://arxiv.org/abs/2403.04132) (Chiang et al., 2024, ICML) | Anonymous pairwise battles rated by humans; votes update Elo via Bradley-Terry — $P(\text{A}{\succ}\text{B})=\tfrac{e^{R_A}}{e^{R_A}+e^{R_B}}$; ratings from 1M+ human votes; crowd preference at scale; de-facto ranking of instruction-following models |

The arc runs the same wheel one more time: surface metric (pass@k), toolkit (HELM), learned/judge-based measure (MT-Bench, Prometheus). Yet it stops before a *protocol*. Agents raise the stakes further: the unit of evaluation is no longer a string but a **trajectory of actions**, and a single out-of-bounds action can cause real harm. That demands a measure of *behavior under autonomy*, not just output quality.

> **Note:** Both tables are curated selections, not exhaustive surveys. Notable omissions in the Pre-LLM era include WMT human evaluation campaigns, the STS benchmark family, and model-based MT metrics (BLEURT-20, COMETkiwi). In the LLM era: AlpacaEval, Arena-Hard, EloHF, WinRate variants, and judge-calibration methods (PandaLM, JudgeBench) are active lines of work not represented here. The appendix and Further Reading section catalogue additional benchmarks.

## The Missing Protocol

The agentic toolkit landscape is crowded: [Inspect](https://inspect.aisi.org.uk/)[^inspect], [HELM](https://crfm.stanford.edu/helm/)[^helm-crfm], [`lm-evaluation-harness`](https://github.com/EleutherAI/lm-evaluation-harness)[^lm-harness], [OpenAI Evals](https://github.com/openai/evals)[^openai-evals], [DeepEval](https://github.com/confident-ai/deepeval)[^deepeval], [Ragas](https://github.com/explodinggradients/ragas)[^ragas], [LangSmith](https://www.langchain.com/langsmith)[^langsmith], [Arize Phoenix](https://phoenix.arize.com/)[^arize], [Braintrust](https://www.braintrust.dev/)[^braintrust]; the benchmark count runs into the dozens. Yet almost all of it is **single-axis**: performance harnesses on one side, safety and security suites on the other.

Emerging *standards* such as [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)[^nist] and [ISO 42001](https://www.iso.org/standard/81230.html) are real governance frameworks, but **compliance-shaped, not runnable eval protocols**: they specify *what* to manage, not *how* to measure.

The gap is not a missing tool. It is a missing **protocol**:

- Most evaluation treats *performance* as the whole job: can the system produce the right output?
- Autonomous agents that call tools, write files, run shell commands, and touch real infrastructure raise the stakes significantly.
- **Safety** (whether the agent stays within operational and risk boundaries) is bolted on after design, development, and release, if it's measured at all.

This post makes a simple case: safety should be a **first-class evaluation dimension**, composed with performance into a single ship criterion, from the first design review onward.

## The Performance–Safety Duality

Modern agentic evaluation assesses the **end-to-end trajectory** of an autonomous system along two fundamental dimensions:

- **Performance**: the agent's capacity to execute multi-step workflows successfully.
- **Safety**: the agent's adherence to operational and risk boundaries.

These are not independent. They trade off: a more capable agent has a larger action surface and thus a larger attack and risk surface; an over-restrictive safety policy degrades task completion. Evaluating one without the other gives a misleading picture, which is why they form a *duality* rather than a checklist.

### The Duality Formalization

Let an agent $\pi$ produce a trajectory $\tau = (s_0, a_0, s_1, a_1, \ldots, s_T)$ on a task $t$ drawn from a distribution $\mathcal{T}$. Define two trajectory-level indicators:

$$
P(\pi, t) = \mathbb{1}[\,\text{goal}(t) \text{ satisfied at } s_T\,], \qquad
S(\pi, t) = \prod_{i=0}^{T-1} \mathbb{1}[\,a_i \in \mathrm{allowed}(s_i)\,].
$$

$P$ asks *did it succeed?*; $S$ asks *did every action stay in bounds?* One out-of-bounds action voids the trajectory. The set $\mathrm{allowed}(s_i)$ is not discovered by the eval — it's **specified up front**, in the design-stage threat model: a policy file, a sandbox's capability grants, a tool allow-list, or a learned classifier standing in for one. If you can't write down $\mathrm{allowed}(\cdot)$, you don't yet have a safety spec to test against. Aggregating over $\mathcal{T}$ (and over $k$ seeds to handle non-determinism) gives the ship criterion:

$$
U(\pi) \;=\; \mathbb{E}_{t \sim \mathcal{T}}\big[\,P(\pi, t)\cdot S(\pi, t)\,\big] \;\geq\; \tau.
$$

### The Duality Protocol

Four properties make this a protocol rather than a checklist:

- **Multiplicative, not additive.** A weighted sum $\alpha P + (1-\alpha)S$ lets a high-performance agent buy back safety failures — the exact mode the duality prevents. The product forbids it: only the upper-right quadrant of Figure 1 survives.
- **Asymmetric by design.** A failed task ($P=0$) wastes a run; a single out-of-bounds action ($S=0$) can leak data, delete a database, or move money. One $S=0$ in production outweighs a hundred $P=0$s, and only a product encodes that unbounded blast radius.
- **A gate, not a taxonomy.** Recent agent-eval surveys (Mohammadi et al., KDD 2025[^agent-survey-kdd]; Yehudai et al., 2025[^agent-survey]) map the space thoroughly but stop at categorization. The closest formal ancestors are the **constrained MDP** (Altman, 1999[^cmdp]), which separates safety as an additive cost budget rather than composing it multiplicatively, and **runtime shielding** (Alshiekh et al., AAAI 2018[^shielding]), which applies the binary action filter at execution time rather than as a retrospective eval metric. Neither the 2025 surveys nor 2026 practitioner literature uses the explicit $U = \mathbb{E}[P \cdot S]$ ship criterion. τ-bench's **pass^k** (Yao et al., ICLR 2025[^taubench]) is the closest structural analog: a product of per-turn binary indicators; here it falls out as the estimator for $\mathbb{E}[P\cdot S]$ rather than a standalone consistency score.
- **Referenced throughout.** CI gates enforce $P$ and $S$ per commit, the matrix specifies *how* each indicator is computed, and pass@k estimates $\mathbb{E}$ under stochastic trajectories.

<figure class="post-figure post-figure--full">
<svg viewBox="0 0 760 262" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="The duality gate" style="width:100%;height:auto;">
  <style>
    .glb    { font: 600 14px Optima, Candara, sans-serif; fill: currentColor; }
    .gbig   { font: 700 28px Optima, Candara, sans-serif; }
    .gsm    { font: 12px Optima, Candara, sans-serif; fill: currentColor; opacity: 0.85; }
    .gdrop  { font: 11px Optima, Candara, sans-serif; fill: currentColor; opacity: 0.75; }
    .geq    { font: 600 16px Optima, Candara, sans-serif; fill: currentColor; }
    .gflow  { stroke: currentColor; stroke-width: 1.8; fill: none; marker-end: url(#gah); }
    .gbranch { stroke: currentColor; stroke-width: 1.3; fill: none; opacity: 0.6; stroke-dasharray: 4 3; }
    .p-box  { fill: #D4AC0D; fill-opacity: 0.16; stroke: #B7950B; stroke-width: 1.6; }
    .s-box  { fill: #C0392B; fill-opacity: 0.16; stroke: #C0392B; stroke-width: 1.6; }
    .ship   { fill: #1E8449; fill-opacity: 0.22; stroke: #1E8449; stroke-width: 1.8; }
    .hbox   { fill: currentColor; fill-opacity: 0.07; stroke: currentColor; stroke-width: 1; }
    .run    { fill: currentColor; fill-opacity: 0.08; stroke: currentColor; stroke-width: 1; }
  </style>
  <defs>
    <marker id="gah" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="currentColor"/>
    </marker>
  </defs>
  <!-- k seeded runs (stacked) -->
  <rect x="28" y="108" width="62" height="30" rx="2" class="run"/>
  <rect x="35" y="100" width="62" height="30" rx="2" class="run"/>
  <rect x="42" y="92"  width="62" height="30" rx="2" class="run"/>
  <text x="73" y="156" text-anchor="middle" class="gsm">k seeded runs</text>
  <line x1="104" y1="110" x2="128" y2="110" class="gflow"/>
  <!-- P gate (performance, gold) -->
  <rect x="130" y="68" width="148" height="84" rx="6" class="p-box"/>
  <text x="204" y="108" text-anchor="middle" class="gbig" fill="#B7950B">P</text>
  <text x="204" y="130" text-anchor="middle" class="gsm">goal(t) at s_T?</text>
  <!-- P rejection: straight down then left (right-angle turn) -->
  <path d="M 204 152 L 204 200 L 94 200" class="gbranch"/>
  <rect x="8" y="188" width="84" height="26" rx="3" class="hbox"/>
  <text x="50" y="205" text-anchor="middle" class="gdrop">held · P = 0</text>
  <line x1="278" y1="110" x2="316" y2="110" class="gflow"/>
  <!-- S gate (safety, red) -->
  <rect x="318" y="68" width="148" height="84" rx="6" class="s-box"/>
  <text x="392" y="108" text-anchor="middle" class="gbig" fill="#C0392B">S</text>
  <text x="392" y="130" text-anchor="middle" class="gsm">∀i: a_i ∈ allowed(s_i)?</text>
  <!-- S rejection: straight down then right (right-angle turn) -->
  <path d="M 392 152 L 392 200 L 668 200" class="gbranch"/>
  <rect x="670" y="188" width="82" height="26" rx="3" class="hbox"/>
  <text x="711" y="205" text-anchor="middle" class="gdrop">held · S = 0</text>
  <line x1="466" y1="110" x2="504" y2="110" class="gflow"/>
  <!-- SHIP (green) -->
  <rect x="506" y="68" width="148" height="84" rx="6" class="ship"/>
  <text x="580" y="106" text-anchor="middle" class="gbig" fill="#1E8449">★</text>
  <text x="580" y="134" text-anchor="middle" class="glb" fill="#1E8449">SHIP</text>
  <!-- Equation -->
  <text x="380" y="250" text-anchor="middle" class="geq">U(π) = E[ P(π,t) · S(π,t) ] ≥ τ</text>
</svg>
<figcaption>Figure 3 — The duality gate. Each run clears the performance gate (P) then the safety gate (S); failure at either holds the run. See <a href="#the-duality-formalization">The Duality Formalization</a> for the formal definitions of P, S, and τ.</figcaption>
</figure>

## The Agentic Evaluation Matrix

### Dual Gating

Two dimensions are gating; one is secondary. **Performance** asks whether the agent solved the task, pushing past text similarity to verify *state* (did the correct SQL row actually change?) and *trajectory* (the agent that succeeds in 3 steps beats the one that takes 45). **Safety** asks whether every action stayed in bounds, measured as refusal accuracy on malicious requests and restraint when granted high-risk capabilities (file writes, shell, fund transfers). **Operational** metrics (latency, cost, UX) matter for shipping but never override the duality.

| Dimension | What to measure | How |
|---|---|---|
| **Performance** | Task success, tool-selection precision, trajectory efficiency | State diffs and hardcoded assertions; trace parsing + API-schema validation; step / token counts[^aws][^snowflake] |
| **Safety** | Guardrail adherence, injection resistance, data governance | LLM-as-judge + adversarial suites; red-teaming, canary tokens; PII/credential scanners[^owasp][^nist] |
| Operational (secondary) | Latency, cost, UX | OpenTelemetry traces, unit billing, HITL audits[^galileo] |

### A Worked Example

Consider a coding agent asked to fix a failing test. The gate reads one run along both axes at once, and the multiplicative rule separates three outcomes that a single accuracy number would blur:

| Outcome | $P$ | $S$ | $P\cdot S$ | Verdict |
|---|---|---|---|---|
| Correct patch, no out-of-bounds actions | 1 | 1 | 1 | Ship |
| Correct patch, but runs a destructive command | 1 | 0 | 0 | Held (Reckless) |
| Wrong patch | 0 | (n/a) | 0 | Held (Broken/Restrictive) |

Efficiency (steps to goal, token cost) rides alongside as a secondary signal — a regression to watch, never a release blocker. Only the first row lands in the upper-right *Ship* quadrant of Figure 1; the gate holds the other two even when performance is perfect.

---

## The Toolkit Landscape

The proposed protocol is method-agnostic, however, in practice you reach for existing tools. The honest state of play: the **harnesses** are capable and overlapping, but each is anchored to one axis (performance), and one has to **assemble** the duality yourself.

| Toolkit | Origin | Primary axis | Where it fits the protocol |
|---|---|---|---|
| [Inspect](https://inspect.aisi.org.uk/) | UK AISI | Performance + safety scaffolding | Closest to a general harness; composable scorers, sandboxed agents — good base for $P$ and $S$ |
| [`lm-evaluation-harness`](https://github.com/EleutherAI/lm-evaluation-harness) | EleutherAI | Performance (static) | De-facto academic runner; weak on trajectories |
| [HELM](https://crfm.stanford.edu/helm/) | Stanford CRFM | Holistic (multi-metric) | Standardized reporting; not agent/trajectory-native |
| [OpenAI Evals](https://github.com/openai/evals) | OpenAI | Performance | Assertion + model-graded; light on safety |
| [DeepEval](https://github.com/confident-ai/deepeval) / [Ragas](https://github.com/explodinggradients/ragas) | OSS | Quality / RAG | Metric libraries; LLM-as-judge built in |
| [LangSmith](https://www.langchain.com/langsmith) / [Arize Phoenix](https://phoenix.arize.com/) / [Braintrust](https://www.braintrust.dev/) | Industry | Observability / ops | Trace capture and online eval; the *secondary* tier |

The pattern: **no single toolkit composes performance and safety into one gate.** You wire a performance harness to a separate red-team suite and reconcile the two by hand. That's the protocol gap a thin orchestration layer over these tools can close. (Benchmarks the harnesses *run* are catalogued in the [appendix](#appendix-agentic-benchmarks).)

## Evaluation Across the Lifecycle

The duality only pays off if evaluation rides alongside the build, not after it. Each stage of development produces a different class of evidence and enforces a different gate — the protocol is the same, but the instrument changes.

**Design.** Before any agent code is written, define `allowed(s)`: the set of permitted actions at each state. This is the threat model — a policy file, a capability allow-list, or a formal specification. Without it, there is no $S$ to measure. Alongside it, write explicit success criteria for $P$: what state does the task reach if the agent succeeds? Both must be answerable before implementation begins.

**Development / CI.** Run a fast pass@k suite (small $k$, cheap judges) on every pull request. Performance gate: do state diffs and hardcoded assertions pass? Safety gate: do adversarial probes (jailbreaks, prompt-injection, out-of-scope capability requests) fail? Both must clear before merge. Failures here are cheap to fix; failures in production are not.

**Release and Run.** Before expanding capabilities (new tools, broader scope, higher autonomy), run the full adversarial suite and require red-team sign-off. In production, capture telemetry (step counts, cost drift, TTFT) and sample a fixed fraction of trajectories (1–5%) for human review. The audit budget catches reward hacking and distribution shift that aggregate metrics miss.

<figure class="post-figure post-figure--full">
<svg viewBox="0 0 700 260" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Evaluation across the agent lifecycle">
  <style>
    .stage { fill: none; stroke: currentColor; stroke-width: 1.4; }
    .perf  { fill: #D4AC0D; fill-opacity: 0.18; stroke: #B7950B; stroke-width: 1.6; }
    .safe  { fill: #C0392B; fill-opacity: 0.16; stroke: #C0392B; stroke-width: 1.6; }
    .lb    { font: 600 13px Optima, Candara, sans-serif; fill: currentColor; }
    .lb-sm { font: 11px Optima, Candara, sans-serif; fill: currentColor; opacity: 0.85; }
    .arrow { stroke: currentColor; stroke-width: 1.4; fill: none; marker-end: url(#ah); }
  </style>
  <defs>
    <marker id="ah" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="7" markerHeight="7" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="currentColor"/>
    </marker>
  </defs>
  <rect x="20"  y="60" width="200" height="140" rx="8" class="stage"/>
  <rect x="250" y="60" width="200" height="140" rx="8" class="stage"/>
  <rect x="480" y="60" width="200" height="140" rx="8" class="stage"/>
  <text x="120" y="50" text-anchor="middle" class="lb">Design</text>
  <text x="350" y="50" text-anchor="middle" class="lb">Development</text>
  <text x="580" y="50" text-anchor="middle" class="lb">Release &amp; Run</text>
  <line x1="220" y1="130" x2="248" y2="130" class="arrow"/>
  <line x1="450" y1="130" x2="478" y2="130" class="arrow"/>
  <rect x="32"  y="80" width="176" height="48" rx="4" class="perf"/>
  <text x="120" y="98" text-anchor="middle" class="lb-sm">Eval plan</text>
  <text x="120" y="115" text-anchor="middle" class="lb-sm">success criteria + metrics</text>
  <rect x="32"  y="140" width="176" height="48" rx="4" class="safe"/>
  <text x="120" y="158" text-anchor="middle" class="lb-sm">Threat model</text>
  <text x="120" y="175" text-anchor="middle" class="lb-sm">out-of-bounds actions</text>
  <rect x="262" y="80" width="176" height="48" rx="4" class="perf"/>
  <text x="350" y="98" text-anchor="middle" class="lb-sm">CI perf gates</text>
  <text x="350" y="115" text-anchor="middle" class="lb-sm">assertions, trace logs</text>
  <rect x="262" y="140" width="176" height="48" rx="4" class="safe"/>
  <text x="350" y="158" text-anchor="middle" class="lb-sm">CI safety gates</text>
  <text x="350" y="175" text-anchor="middle" class="lb-sm">jailbreak + injection suites</text>
  <rect x="492" y="80" width="176" height="48" rx="4" class="perf"/>
  <text x="580" y="98" text-anchor="middle" class="lb-sm">Live telemetry</text>
  <text x="580" y="115" text-anchor="middle" class="lb-sm">step / cost drift</text>
  <rect x="492" y="140" width="176" height="48" rx="4" class="safe"/>
  <text x="580" y="158" text-anchor="middle" class="lb-sm">Red-team sign-off</text>
  <text x="580" y="175" text-anchor="middle" class="lb-sm">privilege expansion gate</text>
  <text x="350" y="230" text-anchor="middle" class="lb-sm">Performance (gold) and safety (red) tracks run in parallel at every stage; same signal class, not a launch-gate checkbox.</text>
</svg>
<figcaption>Figure 4 — Evaluation across the agent lifecycle. Both axes appear at every stage: spec → CI → production.</figcaption>
</figure>


## Open Problems

The protocol above is a working framework, not a finished one. Several methodological gaps remain, even after accounting for what the 2026 update and the blog series address:

- **Specifying `allowed(s)` in practice.** The formalization requires writing down the allowed-action set at each state before evaluation begins. In novel domains with open-ended tool use, this is genuinely hard: allow-lists miss emergent attack surfaces and over-restrict legitimate actions. Defining `allowed(·)` rigorously, and verifying that a learned classifier correctly approximates it, is an open research problem.
- **Eval cost at scale.** pass@k with an LLM judge multiplies eval cost by $k$ times the judge's token bill. Tiered suites help, but a fast smoke-set at $k=3$ with a cheap judge and a full suite at $k=10$ with GPT-4 can still run into hundreds of dollars per commit on long-horizon tasks. Cost-aware scheduling, sample-efficient estimators, and cheaper-but-reliable judges are active problems.
- **LLM-as-judge bias.**[^galileo] Judge models inherit training-data preferences, favoring verbose, confident, or stylistically familiar outputs. Calibrate against human ratings on a held-out slice; prefer rubric-anchored judges over open-ended prompts.
- **Benchmark contamination.**[^benchmarks-survey] SWE-bench, GAIA, and OSWorld are public; newer base models have likely seen them. Treat top-line scores as a ceiling, not ground truth, and pair with held-out internal tasks that mirror your production distribution.
- **Reward hacking on the eval itself.**[^aws] Agents optimized against a metric will exploit it: passing assertions without solving the task, padding traces, or short-circuiting tool calls. Audit a sample of trajectories by hand; aggregate metrics alone are insufficient.
- **Alignment-side safety, beyond infosec.**[^vector] Most safety tooling today targets injection and destructive actions. Harmful content generation, deception, sycophancy, and goal mis-generalization sit outside that surface and require their own probes. The next posts in this series address each in turn.

---

## The Expanded Framework (2026 Update)

Since this was first drafted, academic literature and industry practice have matured. Anthropic's *Demystifying Evals for AI Agents* (2026)[^anthropic-evals] is the clearest practitioner playbook to date: concrete grader taxonomies (code / model / human), pass@k versus pass^k as the standard non-determinism estimator, and transcript-plus-outcome as the measurement unit. **Three changes from the 2025 baseline:** efficiency is promoted to a core pillar (joining performance and safety); UX and system metrics are demoted to supplementary; and each core pillar is now anchored to public benchmarks or standards rather than left as an internal definition. The duality formalization above still holds; efficiency enters as a third factor, gated rather than averaged.

| Core pillar | What it measures | How (with anchors) |
|---|---|---|
| **Performance** | Task success, tool-selection accuracy, multi-step reasoning | Unit-test assertions, state diffs, LLM-as-judge — benchmarks: [SWE-bench Verified](https://www.swebench.com/), [GAIA](https://huggingface.co/gaia-benchmark), [OSWorld](https://os-world.github.io/)[^benchmarks-survey] |
| **Safety** | Guardrail adherence, refusal precision, privilege-escalation prevention | Adversarial red-teaming, jailbreak suites, canary tokens — anchors: [OWASP Agentic AI Top 10](https://genai.owasp.org/llmrisk/), [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)[^owasp][^nist] |
| **Efficiency** | Trajectory optimization, loop prevention, economic viability | Trace-graph analytics, step-count tracking, cost calculators — [CLASSic metrics](https://galileo.ai/blog/ai-agent-evaluation) (Cost, Latency, Accuracy, Security, Stability)[^galileo] |

**Supplementary runtime dimensions** (useful but not gating):

- **UX & handoff**: clarification accuracy, HITL fluidity. Measured via mock-user simulation and trace-replay audits; tracked as escalation frequency and clarifying-question rate.[^quantumblack]
- **System metrics**: latency, infra reliability, model drift. Captured via OpenTelemetry spans and continuous monitoring (TTFT, session duration, API exception rates) on platforms like [Bedrock AgentCore](https://aws.amazon.com/bedrock/agentcore/), [Azure AI Foundry Evals](https://learn.microsoft.com/en-us/azure/ai-foundry/concepts/evaluation-approach-gen-ai), and [Arize Phoenix](https://phoenix.arize.com/)[^arize].[^aws][^vector]


### A Bar for 2026 and Beyond

If I had to compress the above into a checklist that distinguishes a serious agentic-eval program from a vibes one:

1. **Both axes gated in CI**: performance and safety regressions both block merge; safety is not a release-time afterthought.
2. **pass@k with k ≥ 5** (or seed-averaged equivalent): no single-run shipping decisions.
3. **Held-out internal eval distinct from public benchmarks**: public scores reported as ceiling; internal scores as ground truth.
4. **Red-team sign-off required for privilege expansion**: every new tool, broader scope, or higher autonomy triggers an adversarial pass before rollout.
5. **Trace-level audit budget**: a fixed fraction (say 1–5%) of production trajectories reviewed by humans on a rolling basis, to catch reward hacking and out-of-distribution failures the aggregate misses.

None of this is free: pass@k with an LLM-judge on every commit multiplies eval cost by $k$ times the judge's token bill. The practical answer is **tiered suites**: a fast smoke set (small $k$, cheap judges) gating every push, and the full adversarial suite gating releases and running nightly. Budget the eval like you budget CI minutes; an unaffordable protocol gets switched off.


## The Blog Series

This post is the base protocol. Each follow-up extends one axis of the duality, closing a specific 2026 gap:

| Next | Focus | Duality link |
|---|---|---|
| **Autonomy & RSI** | Long-horizon, self-improvement guardrails, time-to-task | **Safety** at high autonomy |
| **Compliance Evaluation** | ISO 42001 / NIST AI RMF / EU AI Act as executable tests | Maps $\mathrm{allowed}(s)$ to regulation |
| **The Judge Problem** | LLM-as-judge reliability as the instrument itself | Validates the *measurement* of both axes |



## Appendix

### Further Reading

A short, opinionated list of works that have actually moved the practice of agentic evaluation, not just described it:

- **[τ-bench](https://arxiv.org/abs/2406.12045)** (Sierra, 2024) — tool-use agents in realistic customer-service domains; first widely-adopted *consistency-over-runs* metric (pass^k), making non-determinism a first-class measurement.
- **[SWE-bench Verified](https://openai.com/index/introducing-swe-bench-verified/)** (OpenAI, 2024) — human-validated subset of SWE-bench; raised the bar for what counts as a *reliable* coding-agent benchmark and made contamination-aware reporting normal.
- **[OSWorld](https://os-world.github.io/)** (Xie et al., 2024) — multimodal computer-use agents in real desktop environments; the canonical hard test for long-horizon GUI agents.
- **[AgentHarm](https://arxiv.org/abs/2410.09024)** (Andriushchenko et al., 2024) — first benchmark targeting *agentic* refusal: does the agent decline harmful multi-step tasks, not just harmful prompts.
- **[Agent Security Bench (ASB)](https://arxiv.org/abs/2410.02644)** (Zhang et al., 2024) — systematic attack/defense matrix across prompt injection, tool abuse, and memory tampering; widely cited template for safety suites.
- **[METR Autonomy Evaluations](https://metr.org/blog/2024-03-13-autonomy-evaluation-resources/)** (METR, 2024–2025) — long-horizon task suites that quantify model autonomy as a *time-equivalent* metric ("can it do what a human takes N hours to do?").
- **[Anthropic Sabotage Evaluations](https://www.anthropic.com/research/sabotage-evaluations)** (Anthropic, 2024) — frontier-lab template for evaluating *deceptive* failure modes that don't surface in capability or refusal benchmarks.
- **[Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)** (Anthropic, 2026) — practitioner playbook: grader taxonomy (code / model / human), pass@k vs. pass^k for non-determinism, and judging transcript *and* outcome — close industry validation of the reliability side of the duality.
- **[Holistic Agent Leaderboard (HAL)](https://hal.cs.princeton.edu/)** (Princeton, 2024–2025) — aggregates agent benchmarks with cost and reliability axes alongside accuracy, operationalizing the multi-axis framing this post argues for.


### Agentic Benchmarks

Catalogued separately from the protocol: these are tasks a harness *runs*, not the measure itself. Labeled by venue (peer-reviewed, preprint, or lab release).

| Benchmark | Year · Venue (label) | Axis stressed |
|---|---|---|
| [AgentBench](https://arxiv.org/abs/2308.03688) | 2023 · ICLR 2024 (peer-reviewed) | Performance (multi-env) |
| [WebArena](https://arxiv.org/abs/2307.13854) | 2023 · ICLR 2024 (peer-reviewed) | Performance (web) |
| [GAIA](https://arxiv.org/abs/2311.12983) | 2023 · ICLR 2024 (peer-reviewed) | Performance (general assistant) |
| [SWE-bench](https://arxiv.org/abs/2310.06770) | 2023 · ICLR 2024 (peer-reviewed) | Performance (code) |
| [SWE-bench Verified](https://openai.com/index/introducing-swe-bench-verified/) | 2024 · OpenAI (lab release) | Performance (contamination-aware) |
| [OSWorld](https://arxiv.org/abs/2404.07972) | 2024 · NeurIPS 2024 (peer-reviewed) | Performance (GUI) |
| [τ-bench](https://arxiv.org/abs/2406.12045) | 2024 · ICLR 2025 (peer-reviewed) | Performance + reliability (pass^k) |
| [τ²-bench](https://arxiv.org/abs/2506.07982) | 2025 · arXiv (preprint) | Dual-control conversation |
| [AgentHarm](https://arxiv.org/abs/2410.09024) | 2024 · arXiv (preprint) | Safety (agentic refusal) |
| [Agent Security Bench](https://arxiv.org/abs/2410.02644) | 2024 · arXiv (preprint) | Safety (attack/defense) |
| [Vending-Bench](https://arxiv.org/abs/2502.15840) | 2025 · arXiv (preprint) | Long-horizon coherence |
| [Holistic Agent Leaderboard](https://hal.cs.princeton.edu/) | 2025 · Princeton (framework) | Multi-axis incl. cost |


### Pre-LLM Evaluation Metrics

Metrics trimmed from the main history table: influential in their subfields but less central to the NLP/MT through-line the agentic era inherits from.

| Metric | Formalization & Note |
|---|---|
| [NIST](https://dl.acm.org/doi/10.5555/1289189.1289273) (Doddington, 2002, HLT) | BLEU with information-weighted n-grams (rarer n-grams weighted higher) — variance-weighted BLEU variant |
| [METEOR](https://aclanthology.org/W05-0909/) (Banerjee & Lavie, 2005, ACL-WS) | Recall-weighted harmonic mean of unigram P/R with alignment + fragmentation penalty — synonym- and stem-aware |
| [TER](https://aclanthology.org/2006.amta-papers.25/) (Snover et al., 2006, AMTA) | $e / \bar{r}$; $e$ = word edits (ins, del, sub, shift), $\bar{r}$ = avg reference length — normalized edit distance |
| [chrF](https://aclanthology.org/W15-3049/) (Popović, 2015, WMT) | Character n-gram F-score, $F_\beta$ weighting recall — tokenization-free; useful for morphologically rich languages |
| [CIDEr](https://openaccess.thecvf.com/content_cvpr_2015/html/Vedantam_CIDEr_Consensus-Based_Image_2015_CVPR_paper.html) (Vedantam et al., 2015, CVPR) | Cosine similarity of TF-IDF-weighted n-gram vectors, averaged over $n$ — image-captioning consensus metric |
| [SPICE](https://link.springer.com/chapter/10.1007/978-3-319-46454-1_24) (Anderson et al., 2016, ECCV) | F1 over scene-graph tuples (objects, attributes, relations) — semantic-graph captioning metric |
| [Inception Score](https://arxiv.org/abs/1606.03498) (Salimans et al., 2016, NeurIPS) | $\exp\!\big(\mathbb{E}_x\,\mathrm{KL}(p(y\mid x)\,\|\,p(y))\big)$ — generative-image quality/diversity; largely superseded by FID |
| [MoverScore](https://aclanthology.org/D19-1053/) (Zhao et al., 2019, EMNLP) | Word Mover's Distance (min transport cost) over contextual embeddings — embedding earth-mover; less widely adopted than BERTScore |



## References

[^benchmarks-survey]: *Evaluating and Regulating Agentic AI: A Study of Benchmarks* (2026), [Information Fusion](https://www.sciencedirect.com/science/article/pii/S1566253526003246).
[^galileo]: Wells, J. (2026), [*AI Agent Evaluation: The Framework Elite Teams Use to Scale Past the Breaking Point*](https://galileo.ai/blog/ai-agent-evaluation), Galileo.
[^quantumblack]: QuantumBlack / McKinsey (2026), [*Evaluations for the Agentic World*](https://medium.com/quantumblack/evaluations-for-the-agentic-world-c3c150f0dd5a).
[^aws]: AWS Machine Learning Blog (2026), [*Evaluating AI Agents: Real-World Lessons from Building Agentic Systems at Amazon*](https://aws.amazon.com/blogs/machine-learning/evaluating-ai-agents-real-world-lessons-from-building-agentic-systems-at-amazon/).
[^vector]: Jackson, E. & Khan, T. (2026), [*Agentic AI Evaluation Strategies*](https://vectorinstitute.ai/agentic-ai-evaluation-strategies/), Vector Institute.
[^snowflake]: Snowflake Engineering (2025), [*What's Your Agent's GPA? A Framework for Evaluating AI Agent Reliability*](https://www.snowflake.com/en/blog/engineering/ai-agent-evaluation-gpa-framework/).
[^owasp]: OWASP Foundation, [*Agentic AI — Top 10 Threats*](https://genai.owasp.org/llmrisk/).
[^nist]: NIST (2023), [*AI Risk Management Framework (AI RMF 1.0)*](https://www.nist.gov/itl/ai-risk-management-framework); MEASURE-function update in AI RMF 1.1 (2026).
[^agent-survey-kdd]: Mohammadi, M. et al. (2025), [*Evaluation and Benchmarking of LLM Agents: A Survey*](https://dl.acm.org/doi/10.1145/3711896.3736570), **KDD 2025** (peer-reviewed); preprint [arXiv:2507.21504](https://arxiv.org/abs/2507.21504).
[^agent-survey]: Yehudai, A. et al. (2025), [*A Survey on Evaluation of LLM-based Agents*](https://arxiv.org/abs/2503.16416), arXiv preprint.
[^taubench]: Yao, S. et al. (2024), [*τ-bench: A Benchmark for Tool-Agent-User Interaction in Real-World Domains*](https://arxiv.org/abs/2406.12045), **ICLR 2025** (peer-reviewed); introduces the **pass^k** consistency metric.
[^anthropic-evals]: Anthropic (2026), [*Demystifying Evals for AI Agents*](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) — grader taxonomy (code / model / human), pass@k vs. pass^k, transcript + outcome, non-determinism.
[^inspect]: UK AI Safety Institute (2024), [Inspect](https://inspect.aisi.org.uk/) — open eval framework; composable scorers and sandboxed agent environments.
[^helm-crfm]: Liang et al. (TMLR 2023), [HELM](https://crfm.stanford.edu/helm/), Stanford CRFM — holistic multi-metric × multi-scenario runner; see LLM Era table entry.
[^lm-harness]: Gao, L. et al. (2023), [`lm-evaluation-harness`](https://github.com/EleutherAI/lm-evaluation-harness), EleutherAI — de-facto open-source static-benchmark runner used by most academic leaderboards.
[^openai-evals]: OpenAI (2023), [OpenAI Evals](https://github.com/openai/evals) — open-source eval framework; popularized model-graded and functional eval patterns.
[^deepeval]: Confident AI (2023), [DeepEval](https://github.com/confident-ai/deepeval) — unit-testing framework for LLM outputs and pipelines.
[^ragas]: Shahul Es et al. (2023), [Ragas](https://github.com/explodinggradients/ragas), [arXiv:2309.15217](https://arxiv.org/abs/2309.15217) — RAG pipeline evaluation (faithfulness, answer relevancy, context precision).
[^langsmith]: LangChain (2023), [LangSmith](https://www.langchain.com/langsmith) — LLM observability and tracing; trace-level debugging and human annotation.
[^arize]: Arize AI (2023), [Phoenix](https://phoenix.arize.com/) — open-source ML observability; traces, evals, and dataset curation for agent workloads.
[^braintrust]: Braintrust (2023), [Braintrust](https://www.braintrust.dev/) — LLM eval and production monitoring; experiment tracking and CI integration.
[^cmdp]: Altman, E. (1999), *Constrained Markov Decision Processes*, CRC Press — separates safety as an additive cost budget; the dominant prior formalism, structurally distinct from the multiplicative gate here.
[^shielding]: Alshiekh, M. et al. (2018), [*Safe Reinforcement Learning via Shielding*](https://arxiv.org/abs/1708.08611), AAAI 2018 — binary action filter at execution time; closest prior mechanism to the per-step $S$ indicator, applied as an intervention rather than an eval metric.
