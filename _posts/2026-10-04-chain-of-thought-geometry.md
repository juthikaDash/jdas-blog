---
layout: post
title: reasoning operations have a geometry
date: 2026-10-04 10:00:00-0400
description: a mechanistic look at what chain-of-thought steps actually correspond to inside the model
tags: llm interpretability reasoning
categories: llm-research
giscus_comments: false
related_posts: false
---

Chain-of-thought output reads like a sequence of distinct moves: restate the problem, break it into subgoals, deduce, check. The open question has always been whether those labels describe anything real inside the network, or whether they are post-hoc narration that the model emits because its training data was full of humans narrating that way.

[Beneath the Surface of Chains-of-Thought](https://arxiv.org/abs/2609.04753) (Jeong, Hwang, Han, Gu, Oh, and Kim; EMNLP 2026 Main) takes the question seriously and answers it with probes. The setup: label the functional operation each CoT span is performing — eight of them, including problem formulation, goal decomposition, and deduction — then ask whether those operations occupy separable regions of hidden-state space.

They do. One-vs-rest probes reach AUROC of 0.927–0.998 across operations on Qwen3-8B, and the result holds across three models and two reasoning datasets. Separability peaks in the intermediate layers, which is where you would expect abstract structure to live if the early layers are still doing lexical work and the late layers are already committing to tokens.

## Why the obvious objection doesn't land

The reflex response is that the probe is reading surface features. Reasoning steps use characteristic vocabulary — "first", "therefore", "so we need" — and they appear at characteristic positions in the trace. A probe that picks up on cue words and ordinal position would produce exactly this kind of AUROC without any claim about reasoning structure.

The paper's two controls are the interesting part. Operation signal is **distributed across the whole span**, not concentrated on the discourse markers that would carry a surface-wording explanation. And identical tokens get different representations depending on the reasoning context they sit in — the same word is encoded differently when it participates in decomposition than when it participates in deduction. Position and wording alone do not reconstruct the structure.

## What I take from it

This is evidence for a weaker and more useful claim than "models reason in steps." It says the model maintains a representation of _what kind of move it is currently making_, and that this representation is linearly accessible. That is a handle, and handles are what interpretability work is short on.

The practical reading is about monitoring. If operation identity is probe-accessible mid-trace, you can in principle watch a reasoning trajectory for structural failure — a model that never decomposes a problem it should have decomposed, or one that emits the surface form of verification without the representation that normally accompanies verification. That second case is the one worth chasing. Faithfulness work keeps running into the problem that stated reasoning and actual computation can diverge, and a probe that separates "checking" from "writing the words for checking" is a plausible instrument for detecting it.

What the paper does not show is that these operations are _causal_ — separability in representation space is not the same as the representation driving the next step. Activation patching is the natural follow-up, and the authors released [code and materials](https://github.com/naver-ai/beneath-cot) for anyone who wants to run it.
