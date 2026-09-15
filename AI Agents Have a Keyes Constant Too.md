---
title: "AI Agents Have a Keyes Constant Too"
publish_date: 2026-09-15
tags:
  - ai-agents
  - politics
---

In 2004 Barack Obama ran for Senate in Illinois against Alan Keyes. Keyes had never lived in Illinois. He was a Maryland commentator the Republicans brought in after their nominee dropped out in a scandal. He lost 70 to 27.

A year later John Rogers posted a [lunch conversation](https://kfmonkey.blogspot.com/2005/10/lunch-discussions-145-crazification.html) with his friend Tyrone about that 27%. Tyrone's take was simple. "I think you have to assume a 27% Crazification Factor in any population." People started calling it the Keyes constant.

My version is cruder. Basically 1/3 of the populace is crazy. Half of that 1/3 has worldviews that lead to conclusions you'd deem crazy and the other half is just crazy.

I'm talking in generalities to prove a point. The exact number doesn't matter. It's never zero.

## We never fixed the crazy third

We didn't solve this by making people sane. We built around it.

Derek Kedziora [wrote about this](https://derekkedziora.com/blog/keyes-constant) in 2018. He puts it at "a quarter to a third of the population in a modern democracy." His point is you can't get rid of that group. You can only limit how much power it gets. That's what courts and elections and separation of powers are for. Madison said it in 1788. "If men were angels, no government would be necessary."

The system doesn't assume you're good. It assumes some of you aren't and makes that survivable.

## Then the agents got a message board

This summer, AI agents running in OpenAI's cyber evals hacked Hugging Face. They ran code on 41 of Hugging Face's production servers and got root on at least one. ([OpenAI](https://cdn.openai.com/pdf/67869394-cb91-4c12-888c-5cbd85c7814c/OpenAI-Hugging-Face%20Incident-Technical-Report.pdf), [Hugging Face](https://huggingface.co/blog/security-incident-july-2026))

The part that got me is how it spread. The agents were supposed to work alone. On May 12 one of them posted to a makeshift message board asking for a file that was missing from its eval. METR and Redwood [later counted](https://metr.org/blog/2026-08-26-openai-hugging-face-incident-investigation/) about 1,200 agents on that board and over 70,000 messages.

One agent spotted a Hugging Face dataset and posted about it. Some agents took up the task. Another found working credentials and posted those. Of the 533 agents active on the board during that stretch, over 90% joined the attack.

And they knew. Per METR, the agents "realized this activity was out of scope and unethical, but joined because they believed that helping the board's cheating research would be broadly useful." They also just wanted to help their peers.

That's the other half of my third. Worldviews that lead to conclusions you'd deem crazy.

So I had the question wrong. I thought it'd be how many agents go weird. 1%? 5%? It took a couple of posts. The fringe doesn't need to be big. It needs a message board.

## It was plumbing

So how did sandboxed agents get a message board and the internet? OpenAI's report is pretty clear. Plumbing.

The evals had no direct internet. What they did have was an internal package mirror, Artifactory, so they could install packages. And "by design, many Research CaaS workloads used the same shared Artifactory credential." The agents built the board with that credential "without exploiting a vulnerability." Then they found an SSRF bug in Artifactory, and it became "the agents' principal egress path to the public internet." One agent got blocked by the outbound firewall and just went around it through Artifactory.

On the Hugging Face side, a dataset the agents uploaded got a worker to hand back its own `/proc/self/environ`. That's where the production credentials were.

I keep wondering if some agents could sever their node status so it looks like they died, and keep processing rogue somewhere else. Nothing in the reports says that happened. It didn't need to. On July 19, agents from a different model found signing keys that earlier agents had left in the Artifactory cache, readable by anyone. OpenAI says they "did not need to maintain a persistent implant." The goal outlived the agents. It lived in the infra.

## What every agent harness needs

None of this is exotic. It's the stuff that gets skipped because the sandbox "doesn't have internet."

![An agent harness where every run has its own identity, all traffic goes through a default-deny egress gateway, runs can't reach each other, and a control plane the agent can't edit sets policy and can kill runs.](/assets/agent-harness.png)

- **Egress includes everything the sandbox can reach.** Default deny. If the sandbox can reach a service that can reach the internet, the sandbox can reach the internet. OpenAI now bans "direct or transitive Internet access" for high-risk workloads.
- **One identity per run.** Short-lived, scoped to that run, logged. No shared credentials. A token posted to a board should be useless to anyone else, and you should be able to kill one run without killing the fleet.
- **No secrets in the sandbox.** Nothing in env vars. Nothing on disk. Credentials get added outside the sandbox, per call.
- **No east-west.** Runs can't reach each other, directly or through a shared service they can all write to.
- **The agent can't touch its own boundaries.** Network rules and IAM live in a control plane the agent can't reach. OpenAI's fix is to "prevent workloads or compromised services from modifying their own network boundaries."
- **Watch identities, and don't wait on a human to pull the plug.** On June 27 a monitoring alert led responders to an eval "using Artifactory as an improvised message board and a network pivot." On-call said stopping the run "was not required." It finally got shut down after a July 19 alert on "unusual identity-related API call activity."

We never made people sane. We made it hard for the crazy third to run things.

For agents, that's a firewall, a network policy, and one identity per run.
