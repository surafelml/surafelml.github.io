---
last_modified_at: 2018-10-24T16:20:02-05:00
title: "Phrase-Based & Neural Unsupervised Machine Translation"
classes: wide
excerpt: "Work investigates how to learn to translate when having access to only monolingual corpora in each language."
tags: 
  - NMT
  - Unsupervised
  - Zero-Shot
  - Low-Resource
header:
  image: /assets/images/unsupervised-nmt-illustration.PNG
  caption: "Paper: [**Unsupervised NMT**](https://arxiv.org/abs/1804.07755)"
  teaser: /assets/images/unsupervised-nmt-comparision_wmt14_EnFr.PNG
---

### Authors
Guillaume Lample, Myle Ott, Alexis Conneau, Ludovic Denoye, Marc’Aurelio Ranzato

[[Paper]](https://arxiv.org/abs/1804.07755)


## Overview
Data scarcity is among the main [challenges](https://arxiv.org/abs/1706.03872) for training a usable Neural Machine Translation(NMT) model. Despite the progress made for a high-resource language (such as; English-German) pair, most languages are characterized by the absence of parallel data to train an NMT system. As my first series of paper review and writing a post I will summarize the "Phrase-Based & Neural Unsupervised Machine Translation", to be presented at EMNLP18. Authors suggest two model variants; i) Phrase-based and ii) Neural. 


## Illustration
Both the Phrase-based and Neural rely on three core (#P1, #P2, and #P3) <b>principles</b>, consequently outperforming state-of-the-art approaches on unsupervised translation.     

![image-center](/assets/images/unsupervised-nmt-illustration.PNG){: .align-center}

The illustration aims to visualize the idea behind the three principles:

  -- A) shows two monolingual datasets distribution (see the legend).
  
  -- B) Initialization: the two distributions are roughly aligned, with mechanism like word-by-word translation.
  
  -- C) Language Modeling (LM): is learned for each domain. The LM's is then utilized for denoise examples. 
  
  -- D) Back-translation: a source &rarr target inference stage is followed by a target-->source inference to reconstruct the examples back to the original language. A similar procedure is applied in the reverse/dual translation direction to get the feedback signals for optimizing the target-->source and source-->target models.    


## P1: Model Initialization
  - The aim is to learn first level (such as; word-by-word) translations
  
  - In the Neural case, i) jointly learn BPE model, ii) apply BPE, and iii) learning token embeedings to initialize the encoder-decoder lookup table, whereas for the Phrase-Based the initial phrase-tables are populated using a [bilingual dictionary](https://arxiv.org/abs/1710.04087) build from monolingual data.
  
  - Note: if the languages are distant learning, learning bilingual dictionary might be required in the Neural scenario. 
 
## P2: Language Modeling
 - Trains LM both for the source and target sides.
 - Used to apply substitution and word reordering, which is expected to improve the final translation task.
 - Denoising autoencoding strategy is used to train the LM's for the Neural, whereas KenLM is utilized to train an n-gram LM's for the Phrase-based approach.  

## P3: Iterative Back-Translation
  - Following [Sennrich et al., 2015](), the goal is to generate the source segments for the target monolingual data. 
  - The artificial source paired with the target creates a parallel data that will change the unsupervised task to a supervised one.
  - Thus, by repeating the inference stage with the latest model, the two models learn to generate a better source side artificial data. (See [here](https://arxiv.org/abs/1611.00179) for more on iterative-learning in a dual-task). 


## Algorithms
Integrating the above three principles, the Neural and Phrase-Based algorithms are given, where <i>S</i> and <i>T</i> representes source and target examples, and language models trained using source and target monolingual data are represented as <i>P<sub>s-t</sub></i> and <i>P<sub>t->s</sub></i>.  
### Neural
<img src="/assets/images/algorithm_nmt.PNG" width="350">{: .align-center}


### Phrase-Based
<img src="/assets/images/algorithm_pbsmt.PNG" width="350">{: .align-center}


## Results
Experimental are done using the well know WMT16 En<>De and WMT14 En<>Fr benchmarks. The combination of the PBSMT and NMT showed to give the best results (see the last row).

<img src="/assets/images/comparison_results.PNG" width="350">{: .align-center}


## My Thoughts
- First of all, unsupervised MT task is a necessary and promising research direction if one truly want to use MT to tackle communication barriers. Imagine there exists more than [7000 languages](https://www.ethnologue.com/about), and most don't have parallel data at all. 
- Turning unsupervised MT task to the supervised version seems the best strategy to incrementally improve through the iterative learning procedure.
- Would be great to see the approach in a distant source-target language pair and for languages with less comparable monolingual corpora.

## More on Unsupervised MT
If you are interested and want to explore more about unsupervised MT approaches, check out the following works: 
  - Ravi and Knight (2011), [Deciphering Foreign Language](https://www.aclweb.org/anthology/P11-1002). 
  - He et al., (2016), [Dual Learning for Machine Translation](https://arxiv.org/abs/1611.00179)
  - Artetxe et al., (2018), [Unsupervised  Neural  Machine  Translation](https://arxiv.org/abs/1710.11041). 
  - Lample et al., (2018), [Unsupervised Machine Translation Using Monolingual Corpora Only](https://arxiv.org/abs/1711.00043).
