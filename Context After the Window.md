---
title: "Context After the Window: Topic Graphs, Semantic Pointers, and Context Selection"
tags:
  - coding
  - ai-agents
---

If memory answers **what is preserved**, context answers **what is activated**.

Most agent systems today conflate the two by treating context as a sliding window over history. That approach scales tokens, not cognition. To scale agents, we need context to become **structured, addressable, and selectable**—much closer to how humans think.

This essay lays out the technical primitives that make that possible.

---

## The Problem with Bottom-Up Context

**Bottom-up context** looks like this:

```lua
context = concat(messages[0..n])
if too large:
  summarize(context)
```

This assumes:

- Chronology ≈ relevance
- Summaries preserve intent
- One linear past is enough

At scale, it fails because relevance is **situational**, not temporal.

Agents need **top-down context**:

> Start from a goal → select topics → choose granularity → pull details.

That requires new data structures.

---

## Core Primitive #1: Topic Graphs (Not Logs)

Replace "conversation = list" with **conversation = graph of topics**.

### Topic Graph

- **Nodes**: topics (e.g., _Streaming_, _Rails_, _Memory_, _Branching_)
- **Edges**: relationships (depends_on, refines, contrasts_with)
- **Metadata**: scope, granularity, freshness, confidence

```css
[Architecture]
   ├─ [Control vs Execution]
   │     ├─ [Streaming]
   │     └─ [Queues]
   └─ [Memory]
         ├─ [Branching]
         └─ [Summaries]
```

Each topic node owns:

- Its own internal history (events, notes, artifacts)
- Its own summaries at multiple resolutions
- Pointers to relevant branches

**Key insight:** Topics are the unit of _meaningful context_, not messages.

---

## Core Primitive #2: Semantic Pointers (Addressing Relevance)

A **semantic pointer** is a reference to _where_ and _how_ to read from the topic graph.

A pointer specifies:

- **Topic(s)**: which nodes matter
- **Resolution**: zoom level (overview → detail)
- **Branch**: which alternative path
- **Temporal slice**: entire history vs recent vs milestone
- **Confidence** (optional): provisional vs canonical

Example pointer:

```json
{
  "topics": ["Memory/Branching"],
  "resolution": "mid",
  "branch": "alternative-B",
  "time": "milestones"
}

```

This says:

> "Load the branching-memory topic, mid-level detail, from branch B, focusing on key moments."

Pointers decouple **storage** from **activation**.

---

## Core Primitive #3: Multi-Resolution Summaries (Zoom Levels)

Summaries shouldn't overwrite; they should **stack**.

For each topic, maintain summaries at multiple resolutions:

- **L0 (Index)**: 1–2 sentences, purpose & scope
- **L1 (Overview)**: paragraph-level, key claims
- **L2 (Working)**: bullets, tradeoffs, open questions
- **L3 (Raw)**: events, messages, artifacts

```yaml
Topic: Streaming
  L0: "Streaming exposes time and progress."
  L1: "Why streaming breaks request boundaries..."
  L2: bullets, examples, failure modes
  L3: raw logs, diffs, messages
```

**Rule:** summaries are _derived nodes_, never destructive replacements.

---

## Core Primitive #4: Context Assembly (Top-Down)

Context is assembled **on demand**.

### Inputs

- Current goal / question
- Active topic(s)
- Desired depth
- Token budget

### Algorithm (Conceptual)

1. **Goal → Topic Selection**
    - Classify goal into topics (embedding + graph proximity)
2. **Topic → Resolution Choice**
    - Decide zoom (overview vs detail)
3. **Branch Selection**
    - Choose branch(es) consistent with goal
4. **Budget Allocation**
    - Allocate tokens per topic by importance
5. **Assembly**
    - Pull summaries/artifacts via pointers
6. **Validation**
    - Ensure coherence; add glue if needed

This is **planning**, not concatenation.

---

## Context Selection Heuristics (Practical)

You don't need perfect theory. Start with heuristics:

### Topic Relevance Score

- Semantic similarity to goal
- Recency of activity
- Dependency distance in graph
- Confidence level (canonical > speculative)

### Resolution Heuristic

- Early exploration → zoom out
- Decision time → zoom in
- Explanation → mixed (overview + detail)

### Branch Policy

- Preserve alternatives by default
- Collapse only on explicit commit
- Keep "shadow branches" for backtracking

---

## Handling Drift and Contamination

Linear context contaminates topics. Topic graphs isolate them.

Mechanisms:

- **Topic boundaries**: content belongs _somewhere_
- **Cross-links**: explicit, not implicit
- **Context guards**: prevent unrelated topics from leaking in
- **Re-entry**: returning to a topic restores its internal context

This mirrors how humans say:

> "Let's go back to _that_ topic."

---

## Event Logs as the Substrate

Under the hood, everything is still an **append-only event log**:

- Observations
- Decisions
- Tool calls
- Outcomes

Topic nodes _index_ into the log.
Branches are pointers to subsets.
Summaries are projections.

This keeps the system:

- Auditable
- Reconstructable
- Revisable

---

## Putting It Together: Branching + Topics + Pointers

A scalable agent memory/context system looks like:

- **Event Log**: immutable history
- **Topic Graph**: semantic organization
- **Branches**: alternative paths
- **Pointers**: context addresses
- **Context Builder**: top-down assembler

Context becomes **navigation**, not accumulation.

---

## Why This Matches Human Cognition

Humans:

- Organize knowledge by topic
- Zoom in/out fluidly
- Revisit contexts intact
- Hold alternatives without collapsing them

This architecture does the same—explicitly.

---

## Minimal Implementation Path

You can start small:

1. Topic nodes as records with IDs
2. Maintain per-topic summaries (2–3 levels)
3. Store events append-only
4. Use pointers to assemble prompts
5. Add branching when needed

The hard part isn't infra.
It's **respecting the distinction between memory and context**.

---

## Closing Thought

> **Logs remember everything.
> Topics organize meaning.
> Pointers decide relevance.**

Agents don't fail because they lack intelligence.
They fail because we give them context the way machines prefer—not the way minds work.
