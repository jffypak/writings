---
title: "Memory After CRUD: Pointers, DAGs, and Branching in Agentic Systems"
tags:
  - coding
  - ai-agents
---

If the philosophical claim is that **agents force us to rethink memory**, the technical reality is this:

> Linear logs and mutable state are no longer sufficient abstractions.

To build agentic systems that reason, revise, and explore, we need memory structures that preserve **history, divergence, and intent**—not just the latest value.

This essay is about what those structures actually look like.

---

## From "State" to "Structure"

CRUD systems treat memory as **state**:

- A row represents truth
- Updates overwrite the past
- History is optional or secondary

Agentic systems need **structure**:

- How did we get here?
- What alternatives existed?
- Which assumptions led to this outcome?
- What can we revisit?

That requires memory models that encode **relationships**, not just values.

---

## The Fundamental Shift: From Lists to Graphs

Most conversational and task systems model memory as a **list**:

```Mathematica
Message 1
Message 2
Message 3
Message 4
```

This implies:

- One past
- One present
- One future

Human reasoning does not work this way.

Agentic reasoning _definitely_ does not.

What we need instead is a **Directed Acyclic Graph (DAG)** of memory.

---

## Memory as a DAG

In a DAG-based memory model:

- Nodes represent thoughts, messages, decisions, or states

- Edges represent "derived from" or "continued from"

- Branches represent alternative paths of reasoning


Example:

```Mathematica
        A
        |
        B
       / \
      C   D
      |   |
      E   F
```

Here:

- `B` led to two possible continuations
- Both paths remain valid
- Neither is erased by choosing the other

This is **branching memory**.

---

## Why DAGs Matter for Agents

DAGs give agents abilities linear memory cannot:

### 1. Reversible reasoning

Agents can:

- Backtrack
- Compare alternatives
- Re-evaluate earlier assumptions

### 2. Parallel exploration

Multiple approaches can be explored without:

- Context collision
- Forced summarization
- Premature commitment

### 3. Explicit provenance

Every decision has a lineage:

> "This conclusion came from _that_ chain of reasoning."

This matters for trust, debugging, and revision.

---

## Event Logs as the Spine of Memory

Underneath most DAG-based systems is an **event log**.

Instead of storing:

> "The agent believes X"

You store:

> "The agent observed A, decided B, attempted C, failed D, retried E"

This is **append-only memory**.

Event logs:

- Preserve time
- Preserve order
- Preserve intent
- Avoid destructive updates

They are the raw material from which higher-level memory is built.

---

## Events vs State (Why This Is Hard for CRUD Minds)

State answers:

> "What is true?"

Events answer:

> "What happened?"

Agentic systems need both—but **events come first**.

State is a projection.
Memory is the source.

This is why event sourcing keeps reappearing in agent systems, even when teams swear they're not "doing event sourcing."

They are. They just don't call it that.

---

## Pointers: The Smallest Useful Abstraction

The real power move is not DAGs—it's **pointers**.

Instead of "current context," agents operate on:

- References to prior nodes
- Anchors into the graph
- Explicit selections of history

A pointer says:

> "This is the past I am reasoning from."

Two agents can share the same event log and still reason differently—because they point to different nodes.

This is how branching conversations work without duplicating memory.

---

## Branches Are Cheap, Collapsing Is Expensive

One counterintuitive insight:

> **Branches are cheap. Collapsing them is what requires judgment.**

Creating a new branch is trivial:

- New pointer
- New edge
- New continuation

Collapsing branches requires:

- Evaluation
- Comparison
- Confidence
- Loss of alternatives

This is why summaries are dangerous when done early.

They collapse structure irreversibly.

---

## Summaries as Derived Nodes (Not Replacements)

In a branching memory system:

- Summaries are _nodes_
- Not overwrites
- Not deletions

A summary becomes:

- One interpretation
- One compression
- One branch among many

This allows:

- Revisiting raw history
- Comparing summary vs source
- Re-summarizing under new goals

This is memory as _living structure_, not archival storage.

---

## Branching Conversations: The Concrete Case

Instead of:

`Conversation = Array<Message>`

You get:

`Conversation = DAG<MessageNode>`
`CurrentContext = Pointer`

Operations become:

- Fork here
- Explore alternative
- Rejoin later
- Compare branches
- Promote branch to primary

This is not a chat feature.

It's a **memory architecture**.

---

## Why Agents Need This More Than Humans

Humans can:

- Intuitively remember alternatives
- Reconstruct lost context
- Notice inconsistencies
- Feel uncertainty

Agents cannot.

If a branch is collapsed:

- It is gone
- Not "forgotten"
- Not "fuzzy"
- Deleted

So the cost of lossy memory is far higher.

---

## The Practical Stack (Conceptually)

You don't need exotic tech.

Conceptually, this is enough:

- Append-only event log
- DAG of memory nodes
- Pointers for active context
- Derived nodes for summaries
- Explicit branch metadata

You can build this with:

- Databases
- Content-addressed storage
- IDs and references
- Simple graph traversal

The complexity is **conceptual**, not infrastructural.

---

## The Deeper Pattern

CRUD systems optimize for:

> Correctness of the present

Agentic systems optimize for:

> Understanding of the past

That requires different memory primitives.

---

## Closing Thought

> **Logs remember what happened.
> Graphs remember how it connects.
> Pointers decide what matters now.**

Agents force us to use all three.

Memory is no longer just where data lives.

It's how thought remains possible.
