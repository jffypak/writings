---
title: "Why Streaming Breaks Traditional Web Assumptions"
tags:
  - coding
  - ai-agents
---

Streaming doesn't introduce new problems.
It exposes the ones we've been ignoring.

For most of the web's history, systems were built around a simple, comforting lie:

> A request comes in.
> Some work happens.
> A response goes out.
> The system rests.

Streaming breaks that lie — not because it's exotic, but because it's _honest_ about how work actually happens.

AI agents, real-time logs, progress updates, token streams, and live diffs don't fit neatly inside the old mental model. And once you introduce streaming, a lot of familiar assumptions quietly collapse.

---

## The Traditional Web Assumption: Work Is Atomic

Classic web architectures assume that work is:

- Short-lived
- Bounded
- Synchronous
- Completed before the response is sent

Even background jobs are often treated as:

> "The same request — just later."

Streaming refuses to play along.

The moment you stream, you admit:

- The work is ongoing
- The outcome is not yet known
- Partial results are meaningful
- Time matters

That single admission changes everything.

---

## Streaming Makes Time a First-Class Concept

In traditional web apps, time is mostly invisible:

- Requests are "fast" or "slow"
- Background jobs are "queued" or "done"
- Delays are hidden behind spinners

Streaming makes time explicit.

When you stream:

- You see progress
- You feel latency
- You notice pauses
- You observe stalls and retries

This is uncomfortable for systems designed to pretend time doesn't exist.

CRUD frameworks are optimized to answer:

> "What is the current state?"

Streaming forces you to answer:

> "What is happening _right now_?"

Those are different questions.

---

## Streaming Breaks the Request Boundary

The request boundary is sacred in traditional web frameworks.

It defines:

- Resource allocation
- Concurrency
- Error handling
- Observability
- Capacity planning

Streaming punches a hole straight through it.

A streamed response:

- Keeps connections open
- Ties up workers or threads
- Lives longer than typical requests
- Overlaps with other work
- Cannot be "rolled back"

This is why streaming inside request-oriented frameworks feels fragile.

The framework is still asking:

> "When does this request finish?"

Streaming answers:

> "It doesn't. It _progresses_."

---

## Streaming Turns Failure from an Exception into a State

In a non-streaming world:

- Success = response returned
- Failure = exception or error code

Streaming breaks this binary.

Failures become:

- Partial output
- Gaps in the stream
- Retries mid-execution
- Degraded progress
- Eventual success after visible failure

You can't hide failure anymore — the user sees it happen.

This is why streaming systems require:

- Idempotency
- Resume semantics
- Explicit state transitions
- Clear ownership of retries

Traditional web stacks were not designed for this level of honesty.

---

## Streaming Exposes Backpressure

Backpressure is always there.
Streaming just makes it visible.

When you stream:

- Slow consumers slow producers
- Overloaded systems stall
- Congestion propagates outward

In request/response systems, backpressure is masked:

- Requests time out
- Queues silently grow
- Systems fall over abruptly

Streaming forces you to confront:

- How many streams can we handle?
- What happens when clients are slow?
- Who owns flow control?

These questions don't go away if you ignore them.
Streaming just stops letting you.

---

## Streaming Collapses the Illusion of "Statelessness"

Statelessness works when:

- Requests are independent
- Responses are final
- Context is short-lived

Streaming is inherently stateful:

- There is a "before" and "after"
- Progress accumulates
- Order matters
- Interruptions matter

Once you stream, you're already managing state — whether you admit it or not.

The difference is whether that state is:

- Explicit and observable
    or
- Implicit and scattered across threads, jobs, and retries

Streaming punishes the second approach.

---

## Why Streaming Feels So Painful in Traditional Web Frameworks

Frameworks like Rails, Django, and Express aren't broken.

They're optimized for a world where:

- Work finishes quickly
- Responses are discrete
- Time is abstracted away
- Failure is rare

Streaming violates all of that.

So you see symptoms:

- Thread exhaustion
- Worker starvation
- Awkward async hacks
- Ad-hoc buffering
- "Just one more queue"

The pain isn't accidental.
It's diagnostic.

---

## Streaming Reveals the Need for an Execution Plane

This is where the Control Plane / Execution Plane split becomes unavoidable.

Streaming belongs to the **execution plane**:

- Where work runs
- Where progress happens
- Where time is real
- Where failure is expected

Trying to host streaming in the control plane leads to:

- Capacity collapse
- Complexity explosions
- Hidden coupling

Once execution is separate, streaming stops being scary.

It becomes just another event source.

---

## Why AI Made This Impossible to Ignore

We've had streaming before:

- Chat
- Notifications
- Logs

AI made it unavoidable because:

- Token streaming is the _default_
- Long-running tasks are normal
- Partial output is valuable
- Users expect visibility, not spinners

AI didn't invent streaming.

It just removed our ability to pretend we didn't need it.

---

## The Deeper Truth

Streaming doesn't break systems.

> **Streaming reveals whether your system was telling the truth about how it works.**

If your architecture assumes:

- Instant completion
- Atomic success
- Invisible time

Streaming will hurt.

If your architecture already models:

- Flow
- Progress
- Failure
- Time

Streaming feels natural.

---

## The Takeaway

The future web isn't "real-time" because it's trendy.

It's real-time because:

- Systems act autonomously
- Work unfolds over time
- Users want to see what's happening
- And pretending otherwise no longer works

Streaming is not a feature.

It's a forcing function.
