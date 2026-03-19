---
title: "Why Rails Feels Painful in an Agentic World"
tags:
  - coding
  - ai-agents
---

Rails didn't get worse.
The world just moved.

For over a decade, Rails was the _correct_ abstraction for most software. It optimized for the dominant shape of work on the web: short-lived requests, synchronous logic, and database-backed state.

But AI agents — systems that _act over time_ — expose a mismatch that's becoming harder to ignore.

Rails doesn't fail loudly in an agentic world.
It fails _slowly_, expensively, and with a lot of compensating code.

---

## Rails Assumes the Request Is the Unit of Work

At its core, Rails is built around a simple idea:

> A request comes in, some logic runs, the request completes.

Everything in the framework reinforces this:

- Controllers
- Middleware
- Transactions
- Callbacks
- Thread-per-request servers
- Strong assumptions about request lifetimes

Even background jobs in Rails ecosystems are treated as **"requests, but later."**

This is fine — even elegant — when work:

- Completes quickly
- Has a clear beginning and end
- Is deterministic
- Is owned by a single execution path

Agentic systems break all four assumptions.

---

## Agents Don't Finish — They Progress

An AI agent doesn't "complete" in the Rails sense.

It:

- Starts
- Thinks
- Calls tools
- Waits on external systems
- Produces partial output
- Fails softly
- Retries
- Gets interrupted
- Continues later

There is no single request boundary that naturally contains this.

So in Rails, teams fake it:

- Status columns (`pending`, `running`, `failed`)
- Background jobs chained together
- Ad-hoc retry logic
- Cron-driven polling
- Hand-rolled state machines hidden in models

The system _works_, but the abstractions are lying.

---

## Long-Lived Streams Are a Structural Mismatch

Agentic systems want to stream:

- Tokens
- Logs
- Tool calls
- Intermediate reasoning
- Progress updates

Rails can technically stream responses (SSE, chunked responses), but at a real cost.

A long-lived stream:

- Holds a Puma thread
- Reduces overall request capacity
- Competes with normal web traffic
- Requires careful tuning to avoid starvation

Rails was designed assuming:

> Threads are freed quickly.

Agent streaming violates that assumption by design.

The result isn't a single dramatic failure — it's **capacity pressure everywhere**.

---

## Rails Is State-First; Agents Are Flow-First

Rails encourages you to think in terms of:

- Models
- Tables
- Associations
- Validations
- Callbacks
- Transactions

This is _state-first thinking_.

Agents require _flow-first thinking_:

- What happens next?
- What is waiting on what?
- What can be retried?
- What partially succeeded?
- What failed but can continue?

Rails has no native vocabulary for this.

So flow logic gets embedded:

- In controllers
- In service objects
- In background jobs
- In callbacks
- In comments and tribal knowledge

Eventually, nobody knows what actually drives the system forward.

---

## Concurrency and Race Conditions Become the Tax

Once you simulate workflows on top of CRUD systems, concurrency becomes your constant tax.

You start worrying about:

- Duplicate job execution
- Lost updates
- Stale reads
- Lock contention
- Idempotency
- Partial writes

Rails gives you tools — transactions, locks, retries — but not _structure_.

You're solving distributed systems problems inside a framework that assumes you aren't.

This is why teams accumulate years of engineering effort fixing:

- DB race conditions
- Redis inconsistencies
- Sidekiq retry edge cases
- "This job ran twice" bugs

Not because Rails is bad — but because it was never meant to be an event engine.

---

## Rails Makes Time an Afterthought

In agentic systems, time is central:

- Delays matter
- Timeouts matter
- Waiting is normal
- Progress happens without user interaction

In Rails, time is usually handled by:

- Cron jobs
- Polling
- Scheduled workers
- Timestamps sprinkled across tables

This works — but it obscures intent.

A queue or workflow system says:

> "This happens after that."

Rails says:

> "This might happen later if something checks."

That difference compounds fast.

---

## The Hidden Cost: Cognitive Load

Perhaps the biggest problem isn't performance or scaling.

It's **cognitive load**.

In a Rails-based agent system:

- Flow is implicit
- State transitions are scattered
- Failure paths are unclear
- Recovery logic is ad-hoc
- Observability is stitched together

You don't know what the system is doing without reading the code.

Agentic systems demand the opposite:

> You must be able to _see_ what is happening.

Rails doesn't give you that view for free.

---

## This Isn't "Rails Is Dead"

Rails still shines as:

- A control plane
- A configuration system
- An auth layer
- A canonical source of truth
- A place to model stable state

The mistake is asking it to be the execution engine for agents.

When Rails owns orchestration, streaming, retries, and long-running workflows, it's being forced to impersonate a system it is not.

---

## The Emerging Shape of Systems

The pattern that's quietly winning looks like this:

- Rails:
    - User intent
    - Configuration
    - Permissions
    - Durable state
- Agent systems:
    - Queues
    - Event streams
    - Workflow engines
    - Long-running tasks
    - Streaming output

Rails remains important — just no longer central.

---

## The Real Lesson

Rails feels painful for agents because:

> **Agentic systems are honest about time, failure, and flow.
> Rails is optimized for pretending those things are edge cases.**

As AI systems move from "respond" to "act," that honesty stops being optional.

And the frameworks we choose — and how we use them — will increasingly determine whether our systems feel elegant or exhausting.
