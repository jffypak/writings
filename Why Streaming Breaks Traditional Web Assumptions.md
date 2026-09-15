---
title: "Why Streaming Breaks Traditional Web Assumptions"
publish_date: 2026-06-25
tags:
  - coding
  - ai-agents
---

Streaming doesn't create new problems. It shows you the ones you already had.

Most of the web was built on a comfortable story. A request comes in. Some work happens. A response goes out. The system rests.

Agents, live logs, progress updates, token streams, live diffs. None of them fit that story. And once you start streaming, a lot of old assumptions fall over quietly.

## Work isn't atomic anymore

Classic web apps assume work is short, bounded, synchronous, and done before the response goes out. Even background jobs are the same request, just later.

The moment you stream, you're admitting the work is still going. The outcome isn't known yet. Partial results mean something. Time matters.

## Time shows up

In a normal web app, time mostly hides. A request is fast or slow. A job is queued or done. A spinner covers the wait.

Streaming puts time on screen. You see progress. You feel latency. You notice when it stalls or retries.

CRUD frameworks are built to answer what the current state is. Streaming asks what's happening right now. Those are different questions.

## The request never finishes

Web frameworks organize everything around the request. Resources, concurrency, errors, logging, capacity planning.

A stream keeps the connection open. It holds a worker or a thread. It lives way longer than a normal request, overlaps other work, and can't be rolled back.

The framework keeps asking when the request finishes. Streaming's answer is it doesn't. It progresses.

## Failure becomes something you watch

Without streaming, failure is binary. You got a response or you got an error.

With streaming, failure is partial output. A gap in the stream. A retry halfway through. Things slowing down and then eventually working. The user watches all of it.

So you need idempotency, a way to resume, explicit state transitions, and a clear owner for retries. Most web stacks weren't built for that.

## Backpressure was always there

Slow consumers slow down producers. Overloaded systems stall. Congestion spreads outward. Request/response hides this. Requests time out, queues grow quietly, and then things fall over all at once.

Streaming makes you deal with it. How many streams can we hold? What happens when a client is slow? Who owns flow control?

Those questions were always there. Streaming just stops letting you ignore them.

## Stateless was never really true

Statelessness works when requests are independent, responses are final, and context is short.

A stream has a before and an after. Progress piles up. Order matters. Interruptions matter. You're managing state the second you stream. The only question is whether that state is explicit and visible, or scattered across threads and jobs and retries.

## Why it hurts in Rails, Django, and Express

These frameworks aren't broken. They're built for a world where work finishes fast, responses are discrete, time is hidden, and failure is rare. Streaming breaks all of that. So you get thread exhaustion, starved workers, awkward async hacks, ad-hoc buffering, and "just one more queue."

The pain is telling you something. Streaming belongs in the execution plane, where work runs and time is real and failure is expected. Put it in the control plane and capacity falls apart. Split the two and a stream is just another event source.

## AI made it unavoidable

We had streaming before. Chat, notifications, logs. AI made it the default. Tokens stream. Long tasks are normal. Partial output is useful. People expect to see what's happening instead of staring at a spinner.

If your system already models flow, progress, failure, and time, streaming feels natural. If it assumes everything finishes instantly, it's going to hurt.

AI didn't invent streaming. It just took away our ability to pretend we didn't need it.
