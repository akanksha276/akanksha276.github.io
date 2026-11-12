---
title: "Your agent's solution depends on what you told it first"
tags: [AI, Agents, LLM, Debugging, Multi-agent]
style: fill
color: primary
description: A debugging story about four people, one bug, and what it taught me about context in LLM agents.
---

A few weeks ago, four of us on the same team were debugging the same problem. Same codebase, same failing runs, same goal. Each of us running our own agent session independently. By the end of the session, we had four different hypotheses and fixes that didn't quite agree with each other, none of them wrong exactly.

One person's agent was trying to fix the skill. Mine was looking at the testing framework. Someone else was convinced it was a timing issue. We were all talking to the same bug from different angles, because we had each given our agents a different slice of context to start from.

I'd read about context sensitivity in LLMs — how priming, retrieval context, and conversation history shape what a model attends to. But reading about it and watching four agents suggest fixes at completely different layers of the stack are different things. It made me think.

---

**The actual bug**

We were running a multi-agent coordination protocol across a set of app workflows. I've written about what that protocol does [on Outshift](https://outshift.cisco.com/blog/ai-ml/cognition-protocols-make-agents-think-together) if you want the full picture. The short version: it's a structured way for agents to align on purpose, resolve disagreements, and reach a shared decision before acting.

The runs looked fine on the surface. No errors, artifacts uploading cleanly. But the protocol was stalling. It would advance through the early phases and then stop. Never completing.

From the outside this looked like low adoption. Like the agents weren't picking up the protocol.

What was actually happening: the protocol generates more than 20 messages in a single turn across its coordination phases. The default summarising context manager was only keeping 20 messages in the agent's working memory. By the time the initiating agent reached the later phases, its protocol instructions had been quietly compressed out of context. It wasn't failing. It had forgotten what it was doing.

The fix was one config change, raising the working memory cap from 20 to 200. The 200 isn't an optimisation number. It's a correctness floor, enough to keep the protocol instructions live across all phases simultaneously.

---

**What this has to do with four people debugging the same thing**

When I look back at that session, what happened makes sense. Each of us had loaded a different part of the problem into context: our own recent work, the part of the codebase we owned, the symptoms we'd personally seen. Our agents inherited those frames. So they looked where we were already looking.

This isn't a failure mode. It's just how context works. An LLM agent doesn't have a view of the whole problem. It has a view of what you put in front of it. Change the context, and you change the hypothesis.

The uncomfortable version of this: the agent that happened to have the right context found the right answer first. Not because it was smarter. Because it was looking in the right place.

---

There's a version of this problem that happens at scale, when it's not four humans giving different context to their agents, but the agents themselves operating with divergent assumptions about what they're doing and why. Getting agents to actually share and align on context, not just pass messages, is a harder problem than it sounds.
