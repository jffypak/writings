---
title: "In an AI World, CRUD is Not Enough"
tags:
  - coding
  - ai-agents
---

For the last 15 years, being a 'good engineer' mostly meant one thing:

> Get good at CRUD

This meant everything from the frontend, API endpoints, to the models down in the backend. You knew how to model data, wrap it in transactions, and expose them with clean APIs. In a web 2.0 world, that was enough. 

But the rise of **AI agents** is quickly making this expertise obsolete. Replacing it are queues, events, graphs, and workflows. A new center of gravity has cometh, and the modern engineer must adapt. 

## CRUD is a worldview, not just an API style

CRUD isnt' just "create, read, update, delete." It's a way of thinking. 

- Work is short-lived
- Requests complete deterministically
- Failure is exceptional
- State is the primary abstraction
- Time is an implementation detail. 

But AI Agents don't behave this way. They run for seconds, minutes, or hours. They fail partially and recover. They fan out to other tools and services. THey produce intermediate results. The processes get interrupted, resumed, or reidrected. They make progress even when nobody is watching. 

In other words: agents live in time, not transactions. 

CRUD doesn't have a native way to express that something failed, but only halfway. It doesn't tell you to try again later. It can't resume from step 4. So we've faked it with status columns, retry flags, cron jobs, background jobs triggered by request-driven systems. And it's worked until it doesn't. 

## Queues model reality better than tables

Queues, events, and workflows are built around a different premise:

> work is something that happens, not something that exists. 

In a queue-first world:

- Time is explicit
- Failure is expected
- Retries are normal
- Partial progress is first-class
- Backpressure is visible 
- Concurrency is intentional

This isn't accidental. Queues exist because the real world is messy, and agentic systems are messy by default. They're systems modeled after human thinking after all, and agentic systems are nowhere near as sophisticated as how humans think. They're just faster at the simpler tasks. 

## Why this is surfacing now

For years, we could get away with bending CRUD systems into shape. 

Rails + Sidekiq
Django + Celery
DB + Redis + "just one more worker"

But AI Agents are pushing these systems past their comfort zones. I've seen it at GitLab and I've heard about problems that companies have had with Celery. They just aren't natively built for 

- Long-lived streams
- Event-driven orchestration
- Stateful async workflows
- Non-deterministic execution
- Human-in-the-loop interruption

You can feel the strain immediately. Threads get tied up, reties get subtle and race conditions multiply. Not to leave out that observability becomes guesswork. 

It makes engineering look bad, but the architecture is lying about what the system really is. 

## The Right Mental Model: Control plane vs Execution Plane

It's not an either/or choice. The emerging pattern involves both CRUD and Queues/Workflows. 

CRUD = control plane
Queues / workflows = execution plane

The control plae handles configs, auth, permissions, canonical state. It answers the question: what is true? 

The execution plane handles agents, tasks, retries, streaming progress. It answers the question: what is happening? 

And all AI agents live almost entirely in the execution plane. 

## This is a skill shift, not a tool shift. 

To step into this new world, I've noticed that I have had to take on a mindset shift. It isn't so much "learning Kafka" or "switching frameworks" as it once was when we all had to learn React or GQL. 

It's about learning to think in: 

- state machines instead of controllers
- messages instead of mutations
- idempotency instead of transactions
- compensation instead of rollback
- visibility instead of hope. 

Engineers who stay CRUD-only will still be useful, but I suspect a lot of these AI coding assistants will get really good at writing code for these systems. So these engineers will be increasingly confined to the edges of the systems. 

Understanding queues, events, and workflows will shape the core. Flow mastery will define the next era of software because AI systems will force us to model reality as it actually behaves. Not atomically, but asynchronously. 