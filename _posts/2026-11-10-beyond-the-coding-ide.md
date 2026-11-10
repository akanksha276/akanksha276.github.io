---
title: "What actually comes after the coding assistant"
tags: [AI, Agents, LLM, Multi-agent, Engineering]
style: border
color: primary
description: Most people using AI coding tools are still in assistant mode. Here's what the next step actually looks like.
---

Using Claude to write a function is not "using AI agents." That's using a very capable tool. There's a difference, and it matters.

A coding assistant (Cursor, Claude Code, Copilot) is still fundamentally you in the loop. You ask, it generates, you review, you decide. That's a good workflow. It's faster. But the agent is not making decisions; it's responding to yours.

The shift happens when you ask: what if the task is too long, too complex, or too interconnected for a single prompt-response cycle?

---

**Two concepts worth understanding: recipe and harness**

When people talk about "building an agent," they usually mean two separate things that often get conflated, and it's an easy conflation to make early on.

A **recipe** is what you tell the agent to do: the instructions, the goals, the constraints, the steps. Think of it as a structured brief. A good recipe doesn't just say "fix the bug." It says what kind of bug, what the acceptable fix looks like, what not to touch, what done means. The quality of the recipe determines most of what the agent actually does.

A **harness** is the infrastructure around the agent: the framework that runs it, manages its memory, connects it to tools, decides when it's finished. LangGraph is a harness. A docker container with an agent loop inside it is a harness. The thing that takes your recipe and actually executes it.

A recipe feels sufficient at first. You've written good instructions, the agent knows what to do. But the harness is doing half the work without announcing itself — and you only notice it when something goes wrong.

---

**The step most people skip**

Once you have one agent working reliably on a scoped task, the natural instinct is to give it more. Bigger tasks, longer runs, less supervision.

This is where things get interesting — and where most tutorials stop being useful.

A single agent running a long task will eventually hit context limits. It will lose track of decisions it made earlier. It will forget instructions it was given at the start. This isn't a failure of the model, it's a constraint of how working memory works in LLM systems. The context window is not infinite, and when a summarising context manager starts compressing things to fit, it makes choices about what to keep. Sometimes it compresses out exactly what the agent needed to remember.

What makes this hard to debug is that the agent doesn't crash. There's no error, no stack trace. It just quietly starts doing something slightly different or stops advancing altogether. You look at the outputs and something feels off, but the system reports success. The recipe was right, the harness was running, and the agent still lost the plot halfway through.

The fix is usually mechanical once you find it: raise the memory cap, restructure what stays live in context, be explicit about what the agent needs to hold onto across a long run. But the debugging is slow because you're not looking for a failure, you're looking for a silent drift.

---

**Then there are multiple agents**

Specialisation is the natural next step. Some tasks are better handled by a team: one agent researches, one writes, one reviews, one decides. You decompose the problem, assign the pieces, coordinate the outputs.

There's a word that gets used a lot here: orchestration. It sounds like coordination but it isn't quite. Orchestration is structural — you define which agents run, in what order, with what inputs. It's a workflow. You can build a reasonably good orchestrated multi-agent system without the agents ever needing to agree on anything. Agent A finishes, its output becomes Agent B's input, and so on.

Coordination is different. It's what you need when the agents have to actually work together on something, when they need to share assumptions, resolve disagreements, maintain a consistent view of what they're doing and why. And this is where most multi-agent systems quietly fall apart, not because the models aren't capable, but because nobody gave the agents a protocol for actually thinking together.

The problem is subtle. Four agents working on the same task, each with a slightly different slice of context, will each build a slightly different mental model of the problem. They'll make locally reasonable decisions that are globally incompatible. No errors. Just outputs that don't quite fit together.

This isn't a new problem in distributed systems — it's consensus, essentially. But it's new enough in the LLM agent world that most frameworks don't have a real answer for it yet.

That's the problem I spend most of my time on. More on that in the next post.
