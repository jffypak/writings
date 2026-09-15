---
title: "Why Rails Feels Painful in an Agentic World"
publish_date: 2026-06-25
tags:
  - coding
  - ai-agents
---

Rails didn't get worse. The world moved.

For over a decade Rails was the right abstraction for most of the web. Short requests. Synchronous logic. State in a database. That was the shape of the work, and Rails fit it.

Agents act over time. And Rails doesn't fail loudly when you build agents on it. It fails slowly, expensively, and with a lot of compensating code.

## The request is the unit of work

Rails is built around one idea. A request comes in, some logic runs, the request completes. Controllers, middleware, transactions, callbacks, thread-per-request servers. All of it assumes that.

Even background jobs are basically requests, but later.

That's fine when work finishes fast, has a clear start and end, is deterministic, and runs down one path. Agents break all four.

## Agents don't finish. They progress.

An agent starts. Thinks. Calls a tool. Waits on something. Produces partial output. Fails softly. Retries. Gets interrupted. Picks back up later.

There's no request boundary that holds that.

So teams fake it. A status column with pending, running, failed. Background jobs chained together. Retry logic nobody wrote down. Cron jobs polling. State machines hiding in models.

It works. But the abstractions are lying.

## Streams hold threads

Agents want to stream tokens, logs, tool calls, and progress. Rails can technically do it with SSE or chunked responses. But a long-lived stream holds a Puma thread the whole time. That's one less thread for normal web traffic. Enough of them and regular requests start starving, and now you're tuning Puma instead of building.

Rails assumes threads get freed fast. Streaming agents don't free them.

You don't get one big dramatic failure. You get capacity pressure everywhere.

## State-first vs flow-first

Rails wants you thinking in models, tables, associations, and validations. What's the state?

Agents need different questions. What happens next? What's waiting on what? What can be retried? What half worked? What failed but can keep going?

Rails has no words for any of that. So the flow logic ends up spread across controllers, service objects, jobs, callbacks, and whoever still remembers how it works. Eventually nobody knows what actually moves the system forward.

## Concurrency is the tax

Once you fake workflows on top of CRUD, you pay for it. Jobs run twice. Updates get lost. Reads go stale. Locks fight. Writes land halfway.

Rails gives you transactions, locks, and retries. It doesn't give you structure. You're solving distributed systems problems in a framework that assumes you aren't.

That's how teams burn years on DB race conditions, Redis inconsistencies, Sidekiq retry edge cases, and "this job ran twice" bugs. Rails isn't bad. It was just never meant to be an event engine.

## Time is an afterthought

In an agent system, time is the whole thing. Delays. Timeouts. Waiting. Progress happens with nobody clicking anything.

In Rails, time is cron jobs, polling, and timestamps sprinkled across tables. A workflow engine says this happens after that. Rails says this might happen later if something checks.

That gap compounds fast. And I think the worst cost isn't performance. It's that you can't tell what the system is doing without reading the code. Agents need the opposite. You have to be able to see what's happening.

## Rails still has a job

Rails is still great as the control plane. Config, auth, permissions, the source of truth, anything stable.

The mistake is asking it to run the agents too. Queues, event streams, workflow engines, long-running tasks, streaming output. That's the execution plane. When Rails owns orchestration and retries and streams, it's pretending to be something it isn't.

Agents are honest about time, failure, and flow. Rails was built to treat those as edge cases.
