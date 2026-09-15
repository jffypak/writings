---
title: "In an AI World, CRUD is Not Enough"
publish_date: 2026-03-18
tags:
  - coding
  - ai-agents
---

For the last 15 years, being a good engineer mostly meant one thing. Get good at CRUD.

That meant everything from the frontend to the API endpoints to the models down in the backend. You knew how to model data, wrap it in transactions, and expose it with clean APIs. In a web 2.0 world, that was enough.

But AI agents are quickly making this expertise obsolete. Replacing it are queues, events, graphs, and workflows. A new center of gravity has cometh, and engineers have to adapt.

## CRUD is a worldview

CRUD isn't just create, read, update, delete. It's a way of thinking. Work is short-lived. Requests complete deterministically. Failure is exceptional. State is the main abstraction. Time is an implementation detail.

AI agents don't behave this way. They run for seconds, minutes, or hours. They fail partway and recover. They fan out to other tools and services. They produce intermediate results. They get interrupted, resumed, or redirected. They make progress even when nobody is watching.

Agents live in time, not transactions.

CRUD has no native way to say something failed, but only halfway. It can't tell you to try again later. It can't resume from step 4. So we've faked it with status columns, retry flags, cron jobs, and background jobs kicked off by request-driven systems. And it's worked, until it doesn't.

## Queues model reality better than tables

Queues, events, and workflows start from a different premise. Work is something that happens, not something that exists.

In a queue-first world, time is explicit. Failure is expected. Retries are normal. Partial progress counts. Backpressure is visible. Concurrency is on purpose.

That isn't an accident. Queues exist because the real world is messy, and agentic systems are messy by default. They're modeled after human thinking after all, and they're nowhere near as sophisticated as how humans think. They're just faster at the simpler tasks.

## Why this is surfacing now

For years we could get away with bending CRUD systems into shape. Rails + Sidekiq. Django + Celery. DB + Redis + "just one more worker."

AI agents are pushing these systems past their comfort zones. I've seen it at GitLab and I've heard about the problems companies have had with Celery. These stacks just aren't built for long-lived streams, event-driven orchestration, stateful async workflows, non-deterministic execution, or a human stepping in halfway through.

You can feel the strain right away. Threads get tied up. Retries get subtle. Race conditions multiply. Observability turns into guesswork.

It makes engineering look bad. But the architecture is lying about what the system really is.

## Control plane vs execution plane

It's not either/or. The pattern I see emerging uses both.

CRUD is the control plane. Queues and workflows are the execution plane.

The control plane handles config, auth, permissions, canonical state. It answers what is true.

The execution plane handles agents, tasks, retries, streaming progress. It answers what is happening.

AI agents live almost entirely in the execution plane.

## This is a skill shift

To step into this world, I've had to change how I think. It isn't so much learning Kafka or switching frameworks, like when we all had to learn React or GraphQL.

It's learning to think in state machines instead of controllers. Messages instead of mutations. Idempotency instead of transactions. Compensation instead of rollback. Visibility instead of hope.

Engineers who stay CRUD-only will still be useful. But I suspect AI coding assistants will get really good at writing code for these systems, so those engineers will get pushed to the edges.

Understanding queues, events, and workflows will shape the core. AI systems are going to force us to model reality as it actually behaves.

Not atomically, but asynchronously.
