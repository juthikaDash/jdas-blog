---
layout: post
title: guard models fail confidently
date: 2026-10-05 11:30:00-0400
description: adversarial prompts don't just break safety classifiers, they break their uncertainty estimates
tags: llm safety calibration evaluation
categories: llm-research
giscus_comments: false
related_posts: false
---

A safety classifier that is wrong 5% of the time is workable if you know _which_ 5%. You route the low-confidence cases to review, accept some latency, and the system degrades gracefully. The whole design depends on confidence meaning something.

[Guard Models Are Overconfident Where Base Models Are Uncertain](https://arxiv.org/abs/2609.36477) (Hong, Jung, and Kim; EMNLP 2026 Findings) reports that this assumption fails in exactly the situation where it matters. The authors evaluate five guard models on prompt classification. On clean inputs, several are close to calibrated — confidence tracks accuracy well enough to build on. Under adversarial attack, calibration degrades by **an order of magnitude**.

The failure mode is worse than the headline number suggests. Adversarial inputs do not produce hedged, uncertain misses. They turn false negatives into _high-confidence_ errors — attacks that slip through with confidence scores indistinguishable from correct detections. The signal you would use to catch the failure is gone precisely when the failure happens.

## The part that suggests a fix

The second finding is the one I keep thinking about. The authors compare each guard model against the base LM it was fine-tuned from, and the uncertainty signal often _survives in the base model_. On the same adversarial inputs where the guard confidently waves an attack through, the base model typically expresses uncertainty.

So safety fine-tuning is not just teaching the model to classify. It is also teaching it to be sure. The training objective rewards decisive labels, and decisiveness generalizes to inputs where the model has no business being decisive — the capability to represent "I don't know about this one" is apparently present in the base weights and gets trained out.

## Implications for anyone deploying a guard

Three things follow if the result holds up:

1. **Clean-input calibration metrics are not evidence of deployment calibration.** If you measured ECE on a benign held-out set and concluded your confidence thresholds were sound, you measured the easy case. The adversarial distribution is the one your threshold has to survive.
2. **The base model is a free second opinion.** Querying the pre-fine-tuning checkpoint alongside the guard costs one extra forward pass and recovers an uncertainty signal the guard discarded. Disagreement between a confident guard and an uncertain base model is a cheap, specific flag for review.
3. **Confidence deserves its own objective.** If decisiveness is a learned artifact of the fine-tune, then calibration-aware training — or simply not discarding the base model's uncertainty head — is a target, not an afterthought.

The general lesson is one that recurs across robustness work: adversarial inputs do not degrade a system uniformly. They tend to break the specific component you were relying on to notice that something broke. Monitoring built on a model's self-reported confidence inherits whatever adversarial weakness that confidence estimate has, and here that weakness is substantial.
