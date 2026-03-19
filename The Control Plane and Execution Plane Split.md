---
title: "The Control Plane and Execution Plane Split (Why AI Systems Are Forcing a New Architecture)"
tags:
  - coding
  - ai-agents
---

Most engineering pain in AI systems today comes from a single mistake:

> We keep trying to run **execution** inside systems designed for **control**.

CRUD frameworks, web servers, and monolithic apps were never meant to _run_ work.
They were meant to _describe_ it.

AI agents make that distinction impossible to ignore.

---

## Two Planes, One System

In distributed systems, there's an old idea that's suddenly relevant again:

> **Separate the control plane from the execution plane.**

You already know this pattern — even if you've never named it.

### Control Plane answers:

- What should happen?

- Who is allowed to do it?

- What is the current state?

- What is the desired configuration?


### Execution Plane answers:

- How does it actually happen?

- What is running right now?

- What failed?

- What needs to be retried?

- What's waiting on what?


Most traditional web apps collapse these into one.

AI systems cannot.

---

## Why CRUD Systems Collapse Under Agents

CRUD frameworks assume:

- Requests are short-lived

- State is the source of truth

- Time is incidental

- Execution is synchronous or near-synchronous


Agentic systems assume the opposite:

- Work is long-lived

- Progress is incremental

- Failure is expected

- Time is central

- Execution happens without users watching


Trying to force agents into CRUD systems leads to:

- Status columns pretending to be workflows

- Background jobs pretending to be orchestration

- Controllers pretending to be schedulers

- Databases pretending to be message buses


The result _works_, but feels wrong — because it is.

---

## The Control Plane (What CRUD Is Actually Good At)

CRUD systems shine when modeling **intent and authority**.

The control plane should own:

- User intent

- Configuration

- Permissions

- Policies

- Canonical state

- Final outcomes


This includes:

- "Run the agent"

- "Cancel the task"

- "Retry with new parameters"

- "This is allowed"

- "This is complete"


Think **stable, inspectable, durable state**.

### Control Plane Diagram

```pgsql
+----------------------+
|      Control Plane   |
|----------------------|
| Users                |
| API / UI             |
| Auth                 |
| Permissions          |
| Configuration        |
| Canonical State      |
+----------------------+
            |
            | commands / intent
            v

```

CRUD is _excellent_ here.

This is where Rails, Django, and similar frameworks still dominate.

---

## The Execution Plane (Where Agents Actually Live)

The execution plane owns **motion**.

It deals with:

- Tasks
- Messages
- Events
- Retries
- Partial progress
- Timeouts
- Backpressure
- Concurrency
- Streaming output

Execution systems assume:

- Work may fail
- Work may resume
- Work may branch
- Work may outlive any single request
- Work must be observable _while it runs_

### Execution Plane Diagram

```lua
            +----------------------+
            |   Execution Plane    |
            |----------------------|
            | Queues               |
            | Workers              |
            | Agents               |
            | Tool Runners         |
            | Workflow Engine      |
            | Event Stream         |
            +----------------------+

```
This is where:

- Agents think
- Tools execute
- Logs stream
- Progress accumulates

Trying to do this _inside_ a CRUD framework is what causes pain.

---

## How the Two Planes Interact

The planes communicate via **commands and events** — not shared logic.

### Full System Diagram

```lua
+----------------------+
|   Control Plane      |
|----------------------|
| UI / API             |
| Auth                 |
| Config               |
| State                |
+----------------------+
            |
            | Command: "Run agent"
            v
+----------------------+
|   Execution Plane    |
|----------------------|
| Queue                |
| Agent Coordinator    |
| Workers              |
| Tools                |
+----------------------+
            |
            | Events: progress, output, failure
            v
+----------------------+
|   Control Plane      |
|----------------------|
| Update state         |
| Persist outcomes     |
| Expose status        |
+----------------------+

```
Key rule:

> **The control plane never blocks on execution.**

It _observes_ execution.
It _does not host_ it.

---

## Streaming Makes the Split Obvious

Streaming output (tokens, logs, diffs) exposes the lie immediately.

If execution lives in the control plane:

- Threads get tied up
- Capacity collapses
- Latency spikes
- Backpressure becomes invisible

If execution lives in the execution plane:

- Streaming is natural
- Workers emit events
- The UI listens
- The system stays honest about time

This is why SSE + queues works so well — and SSE + Rails controllers feels brittle.

---

## Why This Pattern Keeps Reappearing

You've seen this split before:

- Kubernetes
    - Control plane: API server
    - Execution plane: nodes, pods
- CI systems
    - Control plane: pipeline definition
    - Execution plane: runners
- Databases
    - Control plane: metadata, schema
    - Execution plane: query workers

AI agents are just the next system to force the same realization.

---

## The Failure Mode When You Don't Split

When you don't separate planes, you get:

- Controllers doing orchestration
- Background jobs doing coordination
- Databases doing scheduling
- Redis doing locking
- Humans debugging timing bugs at 3am

Every workaround is a symptom.

The architecture is asking one system to be two things at once.

---

## What This Means for Engineers

This split is becoming a **career boundary**.

Engineers who only know CRUD will:

- Build control planes
- Work on config, auth, and state
- Stay important — but peripheral

Engineers who understand execution systems will:

- Design agent workflows
- Own reliability
- Shape AI platforms
- Decide how systems _actually behave_

Both are needed — but they are no longer the same job.

---

## The Quiet Shift

The most important architectural shift of the AI era isn't models.

It's this:

> **Execution is moving out of web frameworks.
> Control is staying behind.**

Once you see the control plane / execution plane split, a lot of modern system design suddenly makes sense.

And once you don't see it — everything feels painful.
