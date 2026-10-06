---
layout: post
title: measuring whether agents actually collaborate
date: 2026-10-06 09:15:00-0400
description: AgentWorld, long-horizon multi-agent tasks, and a metric that asks which agent effort mattered
tags: llm agents evaluation benchmarks
categories: llm-research
giscus_comments: false
related_posts: false
---

Multi-agent evaluation has a measurement problem. Most benchmarks either put agents in competition, cap interaction at a couple dozen steps, or score a team by summing what its members did individually. None of those isolate collaboration. A team can post a good aggregate score while consisting of agents working in parallel and ignoring each other — which is a fine way to solve some tasks, but it is not the thing the benchmark claims to measure.

[AgentWorld](https://arxiv.org/abs/2609.31590) (Shu, Zhang, Cho, Yang, Yuan, Zheng, Guntuku, Ungar, Yu, and Zhang; COLM 2026) is built to close that gap, and the design choices are deliberate:

- **100 human-annotated tasks**, plus 100 augmented variants, set in an MMORPG sandbox.
- **50+ interaction rounds** per task — long enough that maintaining a shared plan becomes the binding constraint rather than single-step reasoning.
- **3–20 agents with asymmetric roles and abilities**, so the task cannot be solved by any one agent and coordination is structurally required.
- A **blackbox setting**: each agent acts independently with no access to others' internal states. Information has to move through communication, not through a shared scratchpad.

That last point is what makes the benchmark about collaboration rather than about planning. Give agents a shared memory and you have measured a single distributed planner. Force them to communicate and you have measured whether they can build and maintain a common model of the task.

## Causal Collaboration Effectiveness

The metric contribution is the part I find more durable than the benchmark itself. **Causal Collaboration Effectiveness (CCE)** builds a graph of causal dependencies between agent actions and asks what fraction of the team's total effort actually contributed to the outcome.

This gets at something task-success rate structurally cannot see. Two teams both finish the task; one did it with every agent's work feeding into the result, the other did it with four agents redundantly duplicating a fifth agent's work. Success rate scores those identically. CCE separates them. It is a wasted-effort measure, and wasted effort is the characteristic pathology of agent teams that have lost their shared plan.

## The results are humbling

Across Gemini 3 Flash, Claude Haiku 4.5, GPT-5 Mini, and DeepSeek R1-70B, **the best model reaches 52.0% task success.** The failure modes are specific and recognizable to anyone who has built a multi-agent system:

- communication breakdowns
- role confusion
- inability to maintain shared plans across rounds

Note that none of these are reasoning failures. Every model tested can plan a task like this when it holds the whole problem itself. What degrades is the maintenance of state _across agents and across time_ — remembering who was assigned what fifty rounds ago, noticing that a teammate's last message invalidated your subtask, keeping a role boundary stable under drift.

That pattern suggests the bottleneck for agent systems is not getting smarter components. It is coordination machinery: protocols that make role assignment explicit and re-checkable, plan representations that survive being passed between agents, and some mechanism for detecting that two agents' models of the task have diverged. Those are engineering problems with the shape of distributed-systems problems, which is an encouraging thing to discover about a research area — it means there is known prior art to borrow from.

The 52% ceiling is also a useful reality check against product claims about autonomous agent teams. On tasks explicitly built to require coordination over long horizons, frontier-adjacent models fail about half the time.
